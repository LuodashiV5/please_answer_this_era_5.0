你是否遇到过这种情况：每次和 AI 对话，都要重新解释一遍你们的项目规范、数据结构、业务逻辑？明明团队写了详细的文档，AI 还是按照它的"常识"来回答，结果和实际需求对不上？

最近几个月，AI 圈子里有个东西火得一塌糊涂 **Claude Skills**。从 Anthropic 官方推出到现在，短短几个月时间，生态已经发展到将近 **8.4** 万个技能包（数据来自 https://skillsmp.com/）。

我身边有个朋友这么说：“装了几个 Skills 之后，AI 就像在我们公司待了三年，什么规矩都懂。”

# **为什么需要 Skills？**

先聊聊现在 AI 用起来都有哪些痛点。

## **痛点一：对话记不住**

每次开新对话都得从零开始。AI 不记得你们之前讨论过什么、项目背景是什么、团队怎么做事。你每次都得反复解释：我们数据结构是这样的、这个字段是那个意思、公司规范要求这样做……

## **痛点二：知识到处都是**

团队经验散落在 Wiki、文档、代码注释里，AI 调用不了。你明明写过详细的 API 文档，AI 还是一遍遍猜接口怎么用。你明明有规范的 Git 提交格式，AI 还是生成一些不伦不类的 commit message。

## **痛点三：老是重复劳动**

每次对话都要重新解释细节，既耗时又容易让 AI 输出不一致。你得一遍遍"教"它你们的做事方式，就像在培训一个永远记不住的新员工。

**Skills 就是为了解决这些问题。**

它把团队的工作流程、业务逻辑、领域知识打包成可复用的指令包，让 AI 能够稳定执行特定任务。就像是给 AI 做了一次入职培训，但这培训 AI 永远不会忘。

# **Claude Code 和 Skills 是什么关系？**

在聊 Skills 之前，得先说说 Claude Code。

**Claude Code 是 Anthropic 官方出的命令行工具**，让你在终端里直接用 Claude。和网页版不同，Claude Code 直接在你的项目目录里跑，能读写文件、执行命令、操作 Git。不用切浏览器，不用复制粘贴，终端里直接告诉它要做什么就行。

很多开发者用了之后说回不去。为什么？因为网页版 AI 的主要问题是"不懂你的项目"，而 Claude Code 直接在你的代码库里工作，就像给团队配了个随时待命的工程师。

**那 Skills 是什么？**

如果说 Claude Code 是操作系统，那 Skills 就是应用程序。Claude Code 本身能写代码、改文件，但装了 Skills 之后能生成 PPT、处理 Excel、发 Twitter、画架构图。

**简单理解：**

- **Claude Code** = 基础平台，提供 AI 能力

- **Skills** = 功能扩展，让 AI 懂得怎么干活

它们的关系就像浏览器和插件。浏览器本身能浏览网页，但装了插件之后能看视频、听音乐、拦截广告。

# **Skills 的核心机制：渐进式披露**

Skills 之所以强大，是因为它采用了"**渐进式披露**"（Progressive Disclosure）的设计。

什么意思？想象你招了个新员工。传统做法是入职第一天把公司所有流程文档、规章制度、操作手册全部打印出来堆在他桌上。这就像很多 AI 工具的做法，把所有知识一股脑塞给 AI，结果就是上下文爆炸。

Skills 的做法是：先给一份简短的岗位说明，等他遇到具体问题时，再告诉他去翻哪本手册的哪一页。

**技术上，Skills 分三层加载：**

**第一层：元数据（启动时加载）**

- 只有名称和简短描述
    
- 每个 Skill 约 100 tokens
    
- 装 100 个 Skill 也只占 10,000 tokens
    

**第二层：完整指令（相关时加载）**

- 当 AI 判断某个 Skill 与任务相关时，才读取完整的 SKILL.md
    
- 建议控制在 5,000 tokens 以内
    

**第三层：参考资料（需要时加载）**

- 详细的技术文档、API 说明、示例代码
    
- 按需读取，用多少加载多少
    

这意味着：一个 Skill 可以打包整套 API 文档、完整的数据字典、几百页参考手册，但只要任务不需要，这些内容就永远不会占用上下文。

# **Skills 的杀手锏：自带脚本**

Skills 还有个能力很多人忽略了：**它可以自带可执行脚本**。

一个典型的 Skill 文件夹结构是这样的：

```
my-skill/├── SKILL.md          # 核心指令（必选）├── scripts/          # 可执行脚本（可选）│   ├── validate.py│   ├── generate.sh│   └── process.js├── references/       # 参考文档（可选）└── assets/           # 模板、配置文件（可选）
```

关键在于：当 AI 运行 scripts/validate.py 时，脚本代码本身不会加载到上下文，只有执行结果会返回。

这是什么概念？假设你有一个 500 行的 Python 脚本处理 PDF 表单。用传统方式，AI 要么自己写代码（消耗大量 tokens 生成），要么读取你的脚本再执行（脚本内容占用上下文）。而用 Skills，AI 直接运行预写好的脚本，整个过程可能只消耗 50 tokens 的输出结果。

**脚本执行 = 零上下文成本 + 确定性结果**

更重要的是：这些脚本通过 Agent 内置的 bash 工具执行，不需要额外的 MCP 或其他工具。这意味着文件读写、数据处理、格式转换、本地 API 调用这些任务，Skills + 内置工具就能搞定。

# **快速上手：5 分钟安装配置**

## **安装 Claude Code**

终端输入：

```
# macOS, Linux, WSL ## 1.原生安装(推荐)curl -fsSL https://claude.ai/install.sh | bash## 2.brewbrew install --cask claude-code# Windows PowerShellirm https://claude.ai/install.ps1 | iex# Windows CMDcurl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

装完选个文件夹，终端输入 `claude` 就能启动。

## **配置 API**

使用 Claude Code 需要配置 API。几种办法：

- 官方 Claude（贵）
    
- 中转 API（性价比高）
    
- GLM 4.7（相对划算）
    

通常会装个 CC Switch（https://github.com/farion1231/cc-switch） 管理各种 API 配置。

## **安装 Skills**

**方式一：自然语言安装**

直接告诉 Claude Code：

```
帮我安装这个 skill，地址：https://github.com/anthropics/skills/blob/main/skills/pptx
```

**方式二：手动安装**

下载 skill 安装包，放到 `~/.claude/skills/` 或项目根目录的 `.claude/skills/` 目录下。

**方式三：插件市场**

在 Claude Code 中运行：

```
/plugin marketplace add anthropics/skills
```

然后在插件市场搜索或直接安装：

```
/plugin install document-skills@anthropic-agent-skills
```

装完重启 Claude Code，然后试试：

```
用 pptx skill 创建一个关于 Claude Skills 的演示文稿
```

几分钟就搞定一个 PPT。

# **哪些场景适合用 Skills？**

Skills 能做的事情很多，让我举几个实际场景。

## **场景一：规范 Git 提交**

没有 Skills 之前，每次提交代码都要想半天怎么写 commit message，或者让 AI 生成但经常不符合团队规范。

有了 git-commit Skill，它能直接按照你们的团队规范生成提交信息。不用每次想半天怎么写 commit。

```
用 git-commit skill 生成提交信息
```

## **场景二：处理办公文档**

**处理 Word：**

```
用 docx skill 分析这份报告的结构
```

**处理 Excel：**

```
用 xlsx skill 统计这个季度的销售数据
```

**生成 PPT：**

```
用 pptx skill 创建一个产品介绍 PPT
```

这些 Skills 都能读取、编辑、分析文档，还能生成图表、格式化内容。

## **场景三：封装团队业务逻辑**

假设你们公司有一套特定的代码审查流程：

1. 检查代码是否符合规范
    
2. 运行测试套件
    
3. 检查安全漏洞
    
4. 生成审查报告
    

你可以把整个流程封装成一个 code-review Skill，AI 就能按照你们的规范一步步执行。不需要每次都解释流程，不需要担心 AI 遗漏某个步骤。

## **场景四：API 接口调用**

假设你们团队有很多微服务，每个服务的 API 都不一样。你可以把所有 API 文档打包成一个 Skill，包含：

- 每个服务的接口定义
    
- 请求参数说明
    
- 返回值格式
    
- 调用示例
    

当 AI 需要调用某个 API 时，它能直接查阅相关文档，准确生成调用代码。

# **好用的 Skills 推荐**

Anthropic 官方仓库：https://github.com/anthropics/skills

Skills 市场：https://skillsmp.com/zh

现在有近 8 万个 Skills，可以用 AI 搜索或按分类查找。

## **官方出品**

**skill-creator**

Anthropic 官方出品，能自动写 skill 的 skill。想自己写 Skills 的话，先装这个。

https://github.com/anthropics/skills/tree/main/skills/skill-creator

```
# claude code 中运行/plugin marketplace add anthropics/skills
```

## **文档处理类**

**pptx / docx / pdf / xlsx**

处理 Office 文档的四大金刚。读取、编辑、分析、转换格式都行。

https://github.com/anthropics/skills

```
npx skills add anthropics/skills
```

**Obsidian Skills**

Obsidian 老板亲自写的 skills，能生成 Obsidian 增强型 Markdown，自动添加标签、日期，不破坏原有格式，还能生成 Obsidian Canvas 白板。

https://github.com/davila7/claude-code-templates

```
npx skills add davila7/claude-code-templates
```

## **开发工具类**

**Superpowers**

一个完整的软件开发工作流程的 skill，包含需求文档、开发、测试等流程。

https://github.com/obra/superpowers

```
npx skills add obra/superpowers
```

**git-workflow**

提交、分支与拉取请求的 Git 工作流指南

https://github.com/agno-agi/agno

```
npx skills add agno-agi/agno
```

**mcp-builder**

创建 MCP（Model Context Protocol）服务器，集成外部 API 和服务。

https://github.com/anthropics/skills

```
npx skills add anthropics/skills
```

## **内容创作类**

**X Article Publisher Skill**

方便写 X（Twitter）长文，解决长文发布的痛点。

https://github.com/wshuyi/x-article-publisher-skill

```
npx skills add wshuyi/x-article-publisher-skill
```

**NotebookLM skill**

在 Claude Code 里直接和 NotebookLM 对话，上传 PDF 到 NotebookLM。

https://github.com/PleasePrompto/notebooklm-skill

```
npx skills add PleasePrompto/notebooklm-skill
```

# **怎么写自己的 Skills？**

推荐启动 claude 后用自然语言先装 Anthropic 官方的 **skill-creator**：

```
帮我直接安装这个 skill，地址：https://github.com/anthropics/skills/blob/main/skills/skill-creator
```

装完就能在 Claude Code 里快速创建 skill 了。比如创建一个 PDF 转 PPT 的 skill：

```
创建一个 skill，能自动将 PDF 转为 PPT
```

接下来不用管了，Claude Code 会创建这个 skill。文件夹生成完成后验证一下：

```
帮我把"产品需求.pdf"转为 PPT 格式
```

几分钟就搞定了。

## **Skill 的核心文件**

每个 Skill 的核心是 SKILL.md 文件。一个完整的 Skill 包含：

```
my-skill/├── SKILL.md          # 核心指令（必须）├── scripts/          # 可执行脚本（可选）├── references/       # 参考文档（可选）└── assets/           # 模板、配置文件（可选）
```

**SKILL.md 是核心**，告诉 AI 这个 Skill 是干什么的、什么时候用、怎么用。

**scripts/ 存放脚本**，AI 可以直接运行，脚本代码不占用上下文。

**references/ 存放文档**，按需加载，可以很详细。

## **写好 Skill 的要点**

**name 字段：**

- 只用小写字母、数字和连字符
    
- 长度限制 64 个字符以内
    

**description 字段：**

- 最多 1024 个字符
    
- 必须说明 Skills 功能和使用时机
    
- 包含具体的触发关键词
    
- 帮助 Claude 准确识别使用时机
    

**几个最佳实践：**

1. **保持功能专一**：每个 Skills 专注解决单一能力，比如 PDF 表单填写、Excel 数据分析、Git 提交消息生成

2. **描述要清晰**：包含具体的触发关键词，帮助 Claude 准确识别使用时机

3. **提供完整示例**：在 SKILL.md 里写清楚示例输入和预期输出

4. **版本管理**：用 Git 跟踪变更

5. **安全考虑**：别在脚本里硬编码 API 密钥、密码等敏感信息

**Skills 可以组合使用：**

多个 Skills 配合实现复杂工作流程：

- brand-guidelines + theme-factory = 一致的品牌化设计
    
- mcp-builder + webapp-testing = 完整的应用开发测试流程
    
- internal-comms + canvas-design = 专业的内部沟通材料
    

# **Skills vs MCP：应该选哪个？**

很多人搞不清楚 MCP 和 Skills 的区别，是不是有了 Skills 就不需要 MCP 了？

**一句话：如果 AI Agent 是操作系统，MCP 就是 USB 协议，Skills 就是应用程序。**

## **MCP 是什么？**

还记得十年前的充电线吗？苹果 Lightning、安卓 Micro USB、笔记本各种奇形怪状的电源头。出门一趟，包里五六根线。

AI 行业 2024 年之前也是这样。想让 Agent 读 GitHub 仓库？写一套对接代码。想让 ChatGPT 查数据库？再写一套。10 个 AI 应用要连 20 个工具，理论上需要 200 个定制集成。每家都在重复造轮子。

2024 年 11 月，Anthropic 开源了 MCP（Model Context Protocol）。它做的事情和 USB-C 统一充电接口一样：定义一套标准协议，让任何 AI 都能即插即用地连接任何工具。

有了 MCP，10 个 AI 应用 + 20 个工具 = 30 个 MCP 实现，而不是 200 个定制集成。

**但 MCP 有个问题：吃上下文。**

每个 MCP Server 连接时，必须把所有工具的定义（名称、描述、参数、示例）一次性塞进上下文。一个工具定义大概 500-800 tokens，一个 MCP Server 通常有 10-20 个工具。

真实数据：

- GitHub MCP Server：27 个工具，约 18,000 tokens
    
- Playwright MCP Server：21 个工具，约 13,600 tokens
    

有开发者配了 7 个 MCP Server，还没开始对话，上下文就被吃掉 67,000 tokens——占 AI 上下文窗口的 33%。你问 AI “2+2 等于几”，它回答 “4” 只要 5 个 token，但工具定义已经消耗了 15,000 tokens。简单问题的成本被放大了 3000 倍。

## **Skills 有什么不同？**

Skills 采用渐进式披露，分层加载：

- 启动时只加载名称和描述（约 100 tokens/Skill）
    
- AI 判断相关时才加载完整指令
    
- 需要时才加载参考资料
    

更重要的是，Skills 可以自带可执行脚本。脚本代码本身不会加载到上下文，只有执行结果会返回。

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrIPkjmJOh8m2NZbRDFEEgrhByUjnrVHsXrYN42xRTtrgaOyzTqP5jiaQx0ZRqaoJXgIt8VnkibGckrw/640?wx_fmt=png&from=appmsg)

**脚本执行模式避开了 MCP 的问题**。复杂流程封装成脚本，AI 只需要一次调用，中间过程不占用上下文。

## **怎么选？**

|   |   |   |
|---|---|---|
|**维度**|**MCP**|**Skills**|
|类比|USB 协议|应用程序|
|核心能力|连接外部系统|编码专业知识|
|上下文消耗|预加载，成本高|渐进式披露，按需加载|
|网络访问|支持|仅本地执行|
|分发方式|URL 接入，面向外部用户|文件复制，面向内部团队|
|适用场景|远程 API、实时数据、对外服务|本地流程、专业方法论、内部工具|

**需要 MCP 的场景：**

- 连接远程 CRM 系统获取客户数据
    
- 调用第三方 SaaS API（Slack、Notion、Jira）
    
- 查询云端数据库
    
- 访问需要认证的外部服务
    
- 做一个服务让外部用户都能用
    

**不需要 MCP 的场景：**

- 读写本地文件 → bash + Skill 脚本
    
- 处理 PDF/Word/Excel → Skill 脚本
    
- 运行代码分析 → Skill 脚本
    
- 执行 Git 操作 → Skill 脚本
    
- 生成图表和可视化 → Skill 脚本
    
- 优化自己或团队的工作流
    

**给开发者的建议：优先用 Skills 封装工作流程，复杂逻辑用脚本而非让 AI 一步步操作，只在必须连接远程系统时才用 MCP。**

但如果你只能选一个先学，选 Skills。它更轻量、更高效、更容易上手，能解决日常遇到的大部分问题。

# **Skills 的三种存储方式**

Claude Code 支持三种 Skills 存储方式，分别适用不同场景。

## **个人 Skills（Personal Skills）**

**存储位置：**`~/.claude/skills/` 目录

**适用场景：**

- 个人工作流程优化
    
- 实验性功能开发
    
- 个人生产力工具
    

比如你个人常用的 Git 提交规范、文档格式、代码模板，都可以放在这里。

## **项目 Skills（Project Skills）**

**存储位置：** 项目根目录下的 `.claude/skills/` 目录

**适用场景：**

- 团队协作
    
- 项目特定的专业知识
    
- 共享工具集
    

比如你们项目的 API 文档、数据结构定义、业务规则，都应该放在项目 Skills 里，这样团队成员都能用。

## **插件 Skills（Plugin Skills）**

**获取方式：** 通过 Claude Code 插件系统安装

**特点：** 安装后自动可用，便于分发和管理

**适用场景：**

- 通用的、跨项目的 Skills
    
- 需要经常更新的 Skills
    
- 团队间共享的 Skills
    

插件机制是推荐的方式，方便管理和分发。

# **实战案例：自动化周报生成**

让我举个完整的例子，看看 Skills 怎么解决实际问题。

假设你每周都要写周报，内容包括：

1. 本周完成的 Git 提交
    
2. Jira 上的任务进度
    
3. 代码质量数据
    
4. 下周计划
    

没有 Skills 之前，你每次都要：

1. 手动查 Git log
    
2. 登录 Jira 查任务
    
3. 跑代码质量工具
    
4. 整理成文档
    

有了 Skills，你可以这样做：

**第一步：创建 weekly-report Skill**

在 `.claude/skills/weekly-report/` 创建 SKILL.md：

```
# Weekly Report Generator生成周报，包含本周提交、任务进度、代码质量和下周计划。# 触发条件- 用户要求生成周报- 用户要求总结本周工作# 执行步骤1. 读取本周 Git 提交记录2. 查询 Jira 任务状态3. 运行代码质量检查4. 生成 Markdown 格式的周报
```

在 `scripts/` 目录创建 `generate.sh`：

```
#!/bin/bash# 获取本周 Git 提交git log --since="1 week ago" --pretty=format:"%h - %s" > /tmp/commits.txt# 查询 Jira（假设有 CLI 工具）jira-cli list --assignee $USER --status Done > /tmp/tasks.txt# 运行代码质量检查quality-check /src > /tmp/quality.txt# 输出结果cat /tmp/commits.txt /tmp/tasks.txt /tmp/quality.txt
```

**第二步：使用 Skill**

以后每次要写周报，只需：

```
用 weekly-report skill 生成本周的周报
```

AI 会自动运行脚本，收集数据，生成格式化的周报。整个过程几分钟搞定，而且格式统一、不会遗漏。

# **Skills 的未来**

Skills 改变了我们和 AI 协作的方式。它把一次性的提示，转变成持久、可组合的知识资产。

通过为 AI 建立一个可扩展的程序性记忆库，Skills 正在为下一代更强大、更自主、更能与人类专家无缝协作的 AI Agent 奠定基础。

Skills 把各种经验和方法打包成技能包，降低了跨行使用的成本，普通人也更加方便地创作自己的 Agent 了。

我觉得，**掌握 Skills，就是掌握了将组织智慧规模化的能力。**

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrIPkjmJOh8m2NZbRDFEEgrh93BA0sd7oUUeMTty9stoPILQoAtsvEZEfWxw6XutggWZCSKibAGUGow/640?wx_fmt=png&from=appmsg)

随着 Skills 生态成熟，MCP 的角色会收窄到"远程连接"这个核心场景——需要实时访问外部 API、需要认证的 SaaS 服务、需要跨网络的数据库连接。而本地文件操作、浏览器自动化、数据处理这些任务，Skills + 内置工具就能搞定，而且效率更高。

**未来可能是这样的格局：**

- 少数通用 MCP Server 处理远程连接（数据库、云 API、SaaS 集成）
    
- 大量 Skills 编码专业知识和本地工作流
    
- 两者在必要时协作，但 Skills 会承担绝大部分"教 AI 怎么做事"的工作
    

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrIPkjmJOh8m2NZbRDFEEgrhgxlEkULM5ge1PI2TpRe0w3EGVzuicq8iaeibqwFeWxibDFGtc2IJVuu11Q/640?wx_fmt=png&from=appmsg)

# **开始行动**

说了这么多，怎么开始？

1. **安装 Claude Code**：5 分钟搞定

2. **装几个 Skills 试试**：pptx、docx、git-commit 都不错

3. **观察自己的工作流程**：找重复劳动、痛点

4. **封装成 Skill**：用 skill-creator 快速生成

5. **持续优化**：根据使用反馈调整

不需要一次性做得很完美，先从简单的开始，逐步完善。有个能用的 Skill，比没有好一百倍。