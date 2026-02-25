# Claude Code 进阶指南：Skills、Subagents 和 MCP，官方文档没写的实战经验





This is the official Claude Code tutorial part 2, where I cover more advanced concepts that help you get even more out of Claude Code. If you haven't read part one, I’d highly recommend it before you read this article. This builds directly on those fundamentals.

Part one went mega viral, but if you missed it, read it first here: 

https://x.com/eyad_khrais/status/2010076957938188661?s=20

I've been a SWE for 7 years, between Amazon, Disney, and Capital One. The code I've shipped touches millions of users. Part one covered the basics that most people get wrong, but this post goes deeper into the systems underneath Claude Code that separate competent usage from exceptional usage.

There are 3 main capabilities that most people still don’t know how to use effectively, which are: skills, subagents, and MCP connectors. All of these capabilities are in the documentation, the only problem is that the documentation doesn't tell you how these pieces fit together in practice, or which configurations actually matter for real work.

What follows is everything I've learned about these systems after using them daily to build production software. Some of this took me weeks to figure out, while some through trial and error. Hopefully it saves you some time.

## **The Context Window Problem**

Before we get into the advanced features, there's something fundamental that affects everything else. If you're using multiple AI coding tools, you've probably assumed they all handle context the same way (spoiler alert: they don’t).  According to a detailed comparison from Qodo's engineering team, Claude Code provides a "more dependable and explicit 200K-token context window," while Cursor's "practical usage often falls short of the theoretical 200K limit" due to "internal truncation for performance or cost management." The system applies internal safeguards that silently reduce your effective context. I cover why context is so important in part 1.

While you could get pissed at Cursor for having these limitations, you have to understand that different tools are optimized for different workflows. If you're working on large, interconnected codebases where you need the model to understand how your authentication system connects to your API routes and how that connects to your database schema, that context matters. So for larger projects I would recommend using Claude Code directly. Claude Code delivers the full 200K consistently.

This is also why the features I'm about to cover work so well in Claude Code specifically. Skills, subagents, and MCP connections all benefit from having predictable context to work with.

## **Skills: Teaching Claude Your Specific Workflows**

A skill is a markdown file that teaches Claude how to do something specific to your work. When you ask Claude something that matches a skill's purpose, it automatically applies it. The structure is dead simple.

1. Create a folder with a 

   [SKILL.md](https://SKILL.md)

    file: ~/.claude/skills/your-skill-name/

   [SKILL.md](http://skill.md/)

2. Or for project-specific skills that you want to share with your team: .claude/skills/your-skill-name/SKILL.md

Every 

[SKILL.md](https://SKILL.md)

 starts with YAML frontmatter:

name: code-review-standards

description: Apply our team's code review standards when reviewing PRs or suggesting improvements. Use when reviewing code, discussing best practices, or when the user asks for feedback on implementation.

The description is critical. Claude uses it to decide when to apply the skill. Be specific about the trigger conditions. You can also explicitly tell Claude to “utilize x skill” and it will do so. But the goal is for Claude to recognize when it needs to utilize the skill on its own accord.

Below the frontmatter, write the actual instructions in markdown. Here's a minimal example:

\---

name: commit-messages

description: Generate commit messages following our team's conventions. Use when creating commits or when the user asks for help with commit messages.

\---

\# Commit Message Format

All commits follow conventional commits: - feat: new feature - fix: bug fix - refactor: code change that neither fixes nor adds - docs: documentation only - test: adding or updating tests

Format: `type(scope): description`

Example: `feat(auth): add password reset flow`

Keep the description under 50 characters. If more context is needed, add a blank line and then the body. It’s awkward to write in this format at first (since writing normally-sounding English sentences is your usual), but the difference in quality is noticeable.

The key architectural principle is progressive disclosure. Claude pre-loads only the name and description of every installed skill at startup (roughly 100 tokens each). The full instructions only load when Claude determines the skill that is relevant, which means that you can have dozens of skills available without bloating your context.

You can add supporting files to your skill folder. If you have extensive reference material, put it in a separate file and reference it in 

[SKILL.md](https://SKILL.md)

. Claude will read it only when needed.

Also important to note that skills aren't limited to just code. I've seen engineers build skills for:

- Database query patterns specific to their schema
- API documentation formats their company uses
- Meeting notes templates
- Even personal workflows like meal planning or travel booking

The pattern works for anything where you find yourself repeatedly explaining the same context or preferences to Claude.

To see what skills are currently loaded, ask Claude directly: "What skills do you have available?" It will list them (or go settings → capabilities → scroll down and you’ll see skills)

## **Subagents: Parallel Processing With Isolated Context**

A subagent is a separate Claude instance with its own context window, system prompt, and tool permissions. This is where Claude Code's architecture really differentiates itself. When Claude delegates to a subagent, that subagent operates independently and returns a summary to the main conversation.

It’s important to remember that context degradation happens around 45% of your context window. Subagents let you offload complex research or implementation tasks to a fresh context, then bring back only the relevant results, which means your main conversation stays clean.

Claude Code includes three built-in subagents:

**Explore**: A fast, read-only agent for searching and analyzing codebases. Claude delegates here when it needs to understand your code without making changes. When used correctly, Claude specifies thoroughness: quick, medium, or very thorough.

**Plan**: A research agent used during plan mode to gather context before presenting a plan. It investigates your codebase and returns findings so Claude can make informed architectural decisions.

**General-purpose**: A capable agent for complex, multi-step tasks requiring both exploration and action. Claude delegates here when the task needs multiple dependent steps or complex reasoning.

**Creating Custom Subagents**

Just like you need a custom skill, I would highly recommend creating your own custom subagents. Run /agents in Claude Code to see all available subagents and create new ones.

To create one manually, add a markdown file to ~/.claude/agents/ (user-level, available in all projects) or .claude/agents/ (project-level, shared with your team).

An exemplar structure:

\---

name: security-reviewer

description: Reviews code for security vulnerabilities. Invoke when checking for auth issues, injection risks, or data exposure.

tools: Read, Grep, Glob

\---

You are a security-focused code reviewer. When analyzing code:

1. Check for authentication and authorization gaps 2. Look for injection vulnerabilities (SQL, command, XSS) 3. Identify sensitive data exposure risks 4. Flag insecure dependencies

Provide specific file and line references for each finding. Categorize by severity: critical, high, medium, low.

The tools field controls what the subagent can do. For a read-only reviewer, restrict to read, grep and glob commands.  For an implementation agent, include write, edit, and bash commands.

**How Subagents Communicate**

This is the part most people miss. Subagents don't share context directly with each other, since they’re operating in isolation. Communication happens through the delegation and return pattern:

1. Main agent identifies a task suitable for delegation
2. Main agent invokes subagent with a specific prompt describing the task
3. Subagent executes in its own context window
4. Subagent returns a summary of findings/actions to main agent
5. Main agent incorporates the summary and continues

The summary is the key. A well-designed subagent doesn't dump its entire context back. This is why subagent descriptions and system prompts need to be explicit about output format.

For complex workflows, you can chain subagents. The main agent orchestrates:

Main Agent

 |── Delegates research to Explore subagent │   └── Returns: "Found 3 relevant files: 

[auth.py](https://auth.py)

, 

[middleware.py](https://middleware.py)

, 

[routes.py](https://routes.py)

" |── Delegates implementation to custom implementer subagent │   └── Returns: "Added password reset endpoint, updated 2 files" └── Delegates testing to custom test-runner subagent     └── Returns: "All 12 tests passing, coverage at 94%"

Each subagent gets fresh context for its specific task. The main agent only holds the summaries, not the full exploration history. This prevents the context pollution that kills long coding sessions.

One important constraint: subagents cannot spawn other subagents. This prevents infinite nesting and keeps the architecture predictable.

**Practical Subagent Patterns**

**Large refactoring**: Have the main agent identify all files that need changes, then spin up a subagent for each logical group. Each subagent handles its scope and returns a summary. The main agent never needs to hold the full context of every file simultaneously.

**Code review pipeline**: Create three subagents: style-checker, security-scanner, test-coverage and run them in parallel against a PR. Each returns findings in a consistent format  →  main agent synthesizes into a single review.

**Research tasks**: When you need to understand an unfamiliar part of the codebase, delegate to *Explore* with specific questions. It returns a distilled map of relevant files and patterns, keeping your main context focused on the actual implementation work.

## **MCP Connectors: Never Leave Claude**

MCP (Model Context Protocol) is a standardized way for AI models to call external tools and data sources through a unified interface instead of custom integrations for each one. You don’t have to go into github, you don’t have to go into slack, gmail, drive, etc.. You can get AI to “talk” to all of those through the Claude interface via an MCP server.

The command to add a connector:

\# HTTP transport (recommended for remote servers)

claude mcp add --transport http <name> <url>

\# Example: Connect to Notion

claude mcp add --transport http notion 

https://mcp.notion.com/mcp

\# With authentication

claude mcp add --transport http github 

https://api.github.com/mcp

 \

--header "Authorization: Bearer your-token"

Or if you want the super simple route in the web app, you go settings → connectors → find your server → configure → give permissions and you’re set.

Some examples of what MCP servers have done for me in the last 6 months:

- Implement features from issue trackers: "Add the feature described in JIRA issue ENG-4521"

- Query databases: "Find users who signed up in the last week from our PostgreSQL database"

- Integrate designs: "Update our email template based on the new Figma designs"

- Automate workflows: "Create Gmail drafts inviting these users to a feedback session"

- Summarize Slack threads: "What did the team decide in the 

  [#engineering](https://x.com/search?q=%23engineering&src=hashtag_click)

   channel about the API redesign?"

The power isn't any single integration.

A workflow that used to require five context switches (check the issue tracker, look at the design, review the Slack discussion, implement the code, update the ticket), now happens in one continuous session. You’re in flow state 24/7.

I’d recommend connecting the following MCP servers:

- **GitHub**: Repository management, issues, PRs, code search
- **Slack**: Channel history, thread summaries, message search
- **Google Drive**: Document access for reference during implementation
- **PostgreSQL/databases**: Direct queries without leaving Claude
- **Linear/Jira**: Issue tracking integration

To see your current MCP connections, run /mcp in Claude Code.

Third-party MCP servers aren't verified by Anthropic, so be careful. For sensitive integrations, review the server's source code or use official connectors from the service providers.

## **The Compound Effect**

Here's where this all comes together. A skill that knows your codebase patterns + a subagent that handles testing + MCP connections to your issue tracker = a system that is unmatched.

The skill encodes your team's conventions. You don’t need to worry about context. The subagents keep your main conversation clean while handling complex subtasks. The MCP connections eliminate the context switching that fragments your attention.

The engineers I've watched who get the most from Claude Code aren't using it for one-off tasks, but they’re treating it as a system to multiply their work capabilities. They invest time configuring skills, defining subagents, connecting services. That investment then rightfully pays dividends on every subsequent task.

If you’re scared of where to start, just start with one skill for something you explain repeatedly. Or create just a singular agent. Then test and go from there. No need to overwhelm yourself with trying everything at once.

## **TLDR**

**Context windows aren't equal.** Claude Code delivers consistent 200K tokens. Cursor often truncates to 70-120K in practice due to internal safeguards. This matters for large codebases.

**Skills teach Claude your specific workflows.** Create ~/.claude/skills/skill-name/SKILL.md with YAML frontmatter (name, description) and markdown instructions. Claude applies them automatically when relevant.

**Subagents provide isolated context for complex tasks.** Each gets its own 200K window. Built-in: Explore, Plan, general-purpose. Create custom ones in ~/.claude/agents/. They communicate through delegation and summary, not shared context.

**MCP connectors eliminate context switching.** Connect to GitHub, Slack, databases, issue trackers. Chain workflows that would normally require five tabs into one continuous session. Command: claude mcp add --transport http <name> <url>

**These compound.** Skills encode patterns, subagents handle subtasks, MCP connects services. Together, they build a system that improves with use.

If you're building with Claude Code and want more tactical breakdowns like this, subscribe to my weekly AI newsletter: 

https://varickagents.com/newsletter



**一、这篇文章是写给谁的**

你用过 Claude Code，觉得还行，但总感觉没发挥出全部实力。

前面的那篇「[Claude Code 完全指南：一位资深工程师的实战心得](https://mp.weixin.qq.com/s?__biz=MzIzMTM3MjU1Mw==&mid=2247483725&idx=1&sn=7ea02976594433c9568127374551bea3&token=1530652544&lang=zh_CN&scene=21#wechat_redirect)」，讲的是基础用法和常见的坑。这篇是第二部分，直接进深水区：**Skills（技能）、Subagents（子代理）、MCP（模型上下文协议）**。

这三个功能官方文档都有。问题是文档只说是什么，不说怎么组合、哪些配置有用。Eyad 花了几周才搞明白，有些靠反复试错。他写出来，省别人的时间。

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/yO9TCuwGNaJvosrkaEeHgmkrCQn5ajVkjm1XF5gX5bkRoozc74xp5Suww1Fh2cRKePXp086HXwdIlia1r8SI2pQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

本文编译自 Eyad Khrais 的 Twitter 长文《The Claude Code Tutorial Level 2》。Eyad 是 7  年经验的工程师，在 Amazon、Disney、Capital One 待过，写的代码服务过数百万用户。我在翻译的基础上加了些自己的理解。

读完的感受：这不是功能介绍，是实战手册。

------

## 二、上下文窗口

讲高级特性之前，有个底层问题要说清楚。

你可能觉得不同 AI 编程工具处理上下文的方式差不多。不是的。

Qodo 工程团队做过对比：Claude Code 提供货真价实的 200K token 上下文窗口。Cursor 理论上也是 200K，实际使用往往达不到，因为内部有截断机制控制性能和成本。这些机制静默执行，你不知道上下文被砍了。

这不是说 Cursor 不好。工具针对不同场景优化。小项目、文件关系简单，Cursor 够用。但大型项目里，认证系统连着 API 路由、API 路由连着数据库 schema，上下文完整性就很重要了。

Skills、Subagents、MCP 在 Claude Code 里效果好，原因之一就是它们依赖可预测的完整上下文。

------

## 三、Skills：把工作流教给 Claude

### 3.1 什么是 Skill

Skill 是一个 Markdown 文件，告诉 Claude 如何处理特定任务。你的问题命中适用场景，Claude 自动调用。

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/yO9TCuwGNaJvosrkaEeHgmkrCQn5ajVkxc1UGofKGBhe6ibnialnWUQibCKxzib8WpBicawoGLndXibmTEXEJ2w8FEJQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)



结构简单：

**全局 Skill**（所有项目生效）：

```
~/.claude/skills/your-skill-name/SKILL.md
```

**项目级 Skill**（和团队共享）：

```
.claude/skills/your-skill-name/SKILL.md
```

### 3.2 怎么写 SKILL.md

每个 **SKILL.md** 以 YAML frontmatter 开头：

```
---
name: code-review-standards
description: Apply our team's code review standards when reviewing PRs or suggesting improvements. Use when reviewing code, discussing best practices, or when the user asks for feedback on implementation.
---
```

`description` 字段很关键。Claude 根据它判断什么时候用这个 Skill，要写具体，把触发条件说清楚。

你也可以直接告诉 Claude "使用 xxx skill"，它会照做。但理想状态是让它自己识别。

一个 Git 提交消息生成的例子：

```
---
name: commit-messages
description: Generate commit messages following our team's conventions. Use when creating commits or when the user asks for help with commit messages.
---

# Commit Message Format

All commits follow conventional commits:

-feat: new feature
-fix: bug fix
-refactor: code change that neither fixes nor adds
-docs: document ationonly
-test: adding or updating tests

Format: `type(scope):description`

Example: `feat(auth): add password resetflow`
```

描述保持 50 字符以内。如果需要更多上下文，加一个空行然后写正文。

Eyad 说一开始用这种格式写 commit message 有点别扭，毕竟正常人不这么写句子，但质量提升肉眼可见。

### 3.3 渐进式加载

架构上有个关键设计：**Claude 启动时只预加载每个 Skill 的名称和描述**，大概每个 100 token。完整指令只在需要时才加载。

你可以装几十个 Skill，不会让上下文臃肿。

Skill 需要引用大量参考资料的话，放单独文件里，在 SKILL.md 中引用。Claude 需要时才读。

### 3.4 Skill 不只写代码

Eyad 见过工程师用 Skill 做各种事：

- 自家数据库 schema 的查询模式
- 公司 API 文档格式
- 会议记录模板
- 个人生活流程，做饭计划、旅行预订

发现规律：你反复向 Claude 解释同一件事，就可以做成 Skill。

查看当前加载了哪些 Skill：直接问 Claude 「你有哪些可用的 skills？」

------

## 四、Subagents：隔离上下文并行处理

### 4.1 什么是 Subagent

Subagent 是独立的 Claude 实例，有自己的上下文窗口、系统提示和工具权限。

Claude 把任务委派给 Subagent，Subagent 独立运行，完成后把总结返回给主对话。

为什么需要？上下文质量随着使用下降。Eyad 提到，**上下文占用超过 45% 时效果就开始变差**。Subagent 让你把复杂任务"外包"给新鲜的上下文，只带回相关结果。主对话保持干净。

### 4.2 内置三个 Subagent

**Explore**：快速只读代理，搜索和分析代码库。Claude 需要理解代码但不修改时用它。可以指定深度：quick、medium、very thorough。

**Plan**：研究型代理，规划模式下收集上下文。调查代码库，返回发现，让 Claude 做有据可依的架构决策。

**General-purpose**：能力更全，用于需要探索和行动的复杂多步骤任务。

### 4.3 创建自定义 Subagent

Eyad 建议创建自己的 Subagent。

运行 `/agents` 查看所有可用 Subagent 并创建新的。

手动创建：把 Markdown 文件放到 `~/.claude/agents/`（用户级）或 `.claude/agents/`（项目级）。

安全审查示例：

```markdown
---
name: security-reviewer
description: 
Reviews code for security vulnerabilities. Invoke when checking for auth issues, injection risks, or data exposure.
tools: Read,Grep,Glob
---

You are a security-focused code reviewer. When analyzing code:

1.Check for authentication and authorization gaps
2.Look for injection vulnerabilities (SQL, command, XSS)
3.Identify sensitive data exposure risks
4.Flag insecure dependencies

Provide specific file and line references for each finding. Categorize by severity:critical,high,medium,low.
```

`tools` 字段控制 Subagent 能做什么。只读审查者限制为 Read、Grep、Glob。实现型代理加上 Write、Edit、Bash。

### 4.4 Subagent 如何通信

大多数人没搞懂这部分。

Subagent 之间不直接共享上下文，隔离运行。通信通过「委派-返回」模式：

1. 主 Agent 识别适合委派的任务
2. 主 Agent 用具体 prompt 调用 Subagent
3. Subagent 在自己的上下文窗口执行
4. Subagent 返回发现/操作的总结
5. 主 Agent 整合总结，继续工作

总结是关键。设计良好的 Subagent 不会把整个上下文倒回来。Subagent 的描述和系统提示需要明确规定输出格式。

### 4.5 链式调用

复杂工作流可以串联多个 Subagent，主代理负责协调：

```
主代理（Main Agent）
├── 委派研究给 Explore subagent
│   └── 返回: "找到3个相关文件: auth.py, middleware.py, routes.py"
├── 委派实现给自定义 implementer subagent
│   └── 返回: "添加了密码重置接口，更新了2个文件"
└── 委派测试给自定义 test-runner subagent
    └── 返回: "全部12个测试通过，覆盖率94%"
```

每个 Subagent 为特定任务获得新鲜上下文。主代理只保留总结，不保留完整探索历史。防止上下文污染。

一个限制：Subagent 不能再生成 Subagent。防止无限嵌套，保持架构可预测。

### 4.6 实用模式

**大型重构**：主代理识别所有需要修改的文件，为每个逻辑分组启动一个 Subagent。每个处理自己的范围并返回总结。主代理不需要同时持有所有文件的完整上下文。

**代码审查流水线**：创建 style-checker、security-scanner、test-coverage 三个 Subagent，并行审查 PR。每个用一致格式返回发现，主代理综合成完整审查意见。

**研究任务**：理解代码库不熟悉的部分时，把问题委派给 Explore。返回相关文件和模式的精炼地图，主上下文专注实现工作。

------

## 五、MCP 连接器：不用离开 Claude

### 5.1 什么是 MCP

MCP（Model Context Protocol）是标准化方式，让 AI 模型通过统一接口调用外部工具和数据源，不用为每个服务写单独集成。

你不用切换到 GitHub、Slack、Gmail、Google Drive，让 AI 通过 MCP 服务器直接和这些服务对话。

### 5.2 添加 MCP 连接器

命令行：

```
# HTTP 传输（推荐用于远程服务器）
claude mcp add --transport http <name> <url>

# 连接 Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 带认证
claude mcp add --transport http github https://api.github.com/mcp \
  --header "Authorization: Bearer your-token"
```

或者图形界面：Settings → Connectors → 找服务 → Configure → 授权。

### 5.3 MCP 能做什么

Eyad 过去 6 个月用 MCP 做过的事：

- **从 issue tracker 实现功能**："添加 JIRA ENG-4521 中描述的功能"
- **查询数据库**："从 PostgreSQL 数据库中查找上周注册的用户"
- **整合设计稿**："根据新的 Figma 设计更新电子邮件模板"
- **自动化工作流**："创建 Gmail 草稿，邀请这些用户参加反馈会议"
- **总结 Slack 讨论**："团队在 [#engineering](javascript:;) 频道中就 API 重新设计做出了什么决定？"

### 5.4 真正的价值

单个集成不算什么，威力在于把这些串起来。

以前需要切换五次上下文的工作流（看 issue tracker、查设计稿、读 Slack 讨论、写代码、更新 ticket），现在在一个会话里完成。Eyad 说可以 24/7 保持心流状态。

### 5.5 推荐 MCP 服务

- **GitHub**：仓库管理、issues、PRs、代码搜索
- **Slack**：频道历史、帖子总结、消息搜索
- **Google Drive**：实现过程中引用文档
- **PostgreSQL/数据库**：不离开 Claude 直接查询
- **Context7**：检索开源项目文档

查看当前 MCP 连接：运行 `/mcp`。

### 5.6 安全提醒

第三方 MCP 服务器没经过 Anthropic 验证。敏感集成要审查服务器源代码，或用服务提供商的官方连接器。

------

## 六、复合效应

这是所有东西组合的地方。

一个了解代码库模式的 Skill + 一个处理测试的 Subagent + 连接 issue tracker 的 MCP，组成一个完整系统。

- Skill 编码团队约定
- Subagent 处理复杂子任务，保持主对话干净
- MCP 消除上下文切换

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/yO9TCuwGNaJvosrkaEeHgmkrCQn5ajVkQFLZ4RGFwRw0asl58AvxWuOHwYflFHESEw5IETw7CxTLCY5BDSZYpQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)



Eyad 观察到，从 Claude Code 获益最多的工程师不是把它当"随用随问"的工具，而是当成系统来经营。投入时间配置 Skill、定义 Subagent、连接服务。这些投入在后续每个任务上产生回报。

不知道从哪开始？从一个 Skill 开始，针对你反复解释的事情。或者创建一个 Subagent。测试，迭代。不用一次性搞定所有东西。

------

## 七、一点补充

**配置即能力**

很多人用 AI 编程工具停留在「问问题得答案」。Eyad 展示的是另一种思路：把知识、偏好、工作流固化到配置里，让 AI 变成持续进化的协作者。

**上下文管理是核心**

不管是 Skill 的渐进加载还是 Subagent 的隔离上下文，核心问题都是：有限的上下文窗口里装最有价值的信息。想明白这个，用任何 AI 工具都更顺手。

**MCP 被低估了**

很多人还在标签页之间切换，手动复制粘贴。MCP 提供更优雅的方式，目前生态还不成熟，但方向是对的。

------

## 八、总结

- **上下文窗口不一样**：Claude Code 稳定 200K token，Cursor 实际常被截断到 70-120K。大型代码库里这很重要。
- **Skills 教 Claude 特定工作流**：创建 `~/.claude/skills/skill-name/SKILL.md`，YAML frontmatter + Markdown 指令。相关时自动应用。
- **Subagents 提供隔离上下文**：每个有自己的 200K 窗口。内置 Explore、Plan、General-purpose。自定义放 `~/.claude/agents/`。通过委派和总结通信，不共享上下文。
- **MCP 消除上下文切换**：连接 GitHub、Slack、数据库、issue tracker。五个标签页的工作流串成一个会话。命令：`claude mcp add --transport http <name> <url>`
- **复合效应**：Skill 编码模式，Subagent 处理子任务，MCP 连接服务。组合起来，越用越强。