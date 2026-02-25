用 Cursor 写代码，默认体验已经不错。但如果你发现 Agent 老是犯同一个错、不了解你的项目规范、没法调用内部工具——这时候就需要动用 Cursor 的四套扩展机制了。这篇文章把 Rules、Skills、Subagents、MCP 逐个拆开讲，附带配置示例和踩坑经验，帮你在 5 分钟内搞清楚该用哪个。

# 先搞清楚：这四个东西分别解决什么问题

一句话概括：

| 机制            | 解决的核心问题                 | 类比             |
| ------------- | ----------------------- | -------------- |
| **Rules**     | Agent 不懂你的项目规范，每次都要重复交代 | 贴在工位上的备忘录      |
| **Skills**    | 某类任务需要一套完整的领域知识和脚本      | 可复用的 SOP 手册    |
| **Subagents** | 复杂任务需要拆分、并行、隔离上下文       | 请了几个专项实习生      |
| MCP           | Agent 需要连接外部工具和数据源      | 给 Agent 装了一堆插件 |

它们不是互斥关系，经常搭配使用。比如你可以写一条 Rule 告诉 Agent "部署时调用 deploy skill"，而 deploy skill 内部可能会启动一个 subagent 来跑 shell 命令，同时通过 MCP 连接 GitHub 查 PR 状态。

---

# Rules：让 Agent 记住你的规矩

## 是什么
Rules 就是写给 Agent 的持久化指令。LLM 本身跨对话不保留记忆，Rules 在每次对话开始时注入到模型上下文的最前面，相当于一份"永久置顶的 system prompt"。
Rules 解决不了的问题：它不会影响 Cursor Tab（自动补全），只作用于 Agent 聊天。
## 四种规则类型

| 类型                        | 生效条件               | 典型用途         |
| ------------------------- | ------------------ | ------------ |
| `Always Apply`            | 每次对话都注入            | 编码规范、项目架构约定  |
| `Apply Intelligently`     | Agent 根据描述自动判断是否相关 | 特定框架的最佳实践    |
| `Apply to Specific Files` | 匹配文件 glob 模式时注入    |              |
| `*.tsx`                   | 文件用 Tailwind、      |              |
| `api/`                    | 目录用 zod 校验         |              |
| `Apply Manually`          | 聊天中                |              |
| `@规则名`                    | 手动引用               | 偶尔需要的模板或检查清单 |

## 怎么配

在项目根目录.cursor/rules/下创建.md或.mdc文件。.mdc文件支持 frontmatter 元数据：

```
---
description: "React 组件开发规范"
globs: "src/components/* */*.tsx"
alwaysApply: false
---

- 样式用 Tailwind，不要写内联 style
- 组件用命名导出，不要 default export
- Props 接口定义放在组件上方
@component-template.tsx

```

这条规则只在你编辑src/components/下的.tsx文件时才会被 Agent 看到。@component-template.tsx会把模板文件的内容也拉进上下文。

如果你想更简单，直接在项目根目录放一个AGENTS.md，纯 Markdown，不需要 frontmatter：

```
项目规范  
- TypeScript 写所有新文件
- 数据库列名用 snake_case
- 业务逻辑放 service 层，不要写在 controller 里
```

AGENTS.md还支持子目录嵌套——frontend/AGENTS.md只在处理前端文件时生效，子目录的指令优先级更高。

## 踩坑提醒

- 规则没生效？

Apply Intelligently类型必须写description，否则 Agent 不知道什么时候该用。 **别把 style guide 整篇塞进去**。Agent 本身就懂常见代码风格，你只需要写"跟通用实践不一样"的部分。规则控制在 500 行以内，太长了拆成多个。 **别复制代码到规则里**。用@文件名引用，这样代码改了规则不用跟着改。

---

# Skills：可复用的领域知识包

## 是什么

Skill 是一个文件夹，里面装着SKILL.md（指令文档）+ 可选的脚本和参考资料。Agent 在判断任务相关时会自动加载对应 Skill，也可以在聊天中用/skill-name手动触发。

跟 Rules 的区别：Rules 是被动注入的上下文，Skill 是按需加载的"能力包"，可以包含可执行脚本，而且资源是渐进式加载的——Agent 只在需要时才去读references/和scripts/里的内容，不会一开始就占满上下文窗口。

## 文件结构

```json
.cursor/skills/
└── deploy-app/
    ├── SKILL.md          
主文件：frontmatter + 指令
    ├── scripts/
    │   ├── deploy.sh     # Agent 可以直接跑的脚本
    │   └── validate.py
    ├── references/
    │   └── REFERENCE.md  # 按需加载的补充文档
    └── assets/
        └── config-template.json
```

Skill 的加载位置有多个，项目级放.cursor/skills/，用户级放~/.cursor/skills/，也兼容.claude/skills/和.codex/skills/。

## SKILL.md 写法

```
---
name: deploy-app
description: 将应用部署到预发布或生产环境。当用户提到部署、发布、上线时使用。
---

Deploy App

## 使用时机
- 用户要求部署到 staging 或 production
- 用户提到"发版""上线"

## 步骤

1. 先跑校验：python scripts/validate.py
2. 确认通过后执行：scripts/deploy.sh &lt;environment&gt;
3. environment 只能是 staging 或 production

## 注意

- 部署前确认当前分支是 main
- 如果 validate 失败，先修复再部署，不要跳过
```

description写得好不好直接决定 Agent 能不能在合适的时候自动调用这个 Skill。写明"什么场景下用"比写"这个 Skill 做什么"更有效。

如果你不想让 Agent 自动调用，只想当斜杠命令用，加一行disable-model-invocation: true。

## 从 Rules 迁移到 Skills

Cursor 2.4+ 内置了/migrate-to-skills命令，在聊天里输入就能自动把Apply Intelligently类型的动态规则和斜杠命令转成 Skill。Always Apply和带globs的规则不会被迁移——它们的触发方式跟 Skill 不同。

---

# Subagents：让 Agent 开多线程干活

## 是什么

Subagent 是 Agent 可以派出去的"子进程"。每个 Subagent 有独立的上下文窗口，干完活把结果返回给主 Agent。

核心价值有三个：
**1.上下文隔离**：搜代码库、跑命令产生的大量中间输出留在子进程里，主对话不会被噪音撑爆 
**2.并行执行**：同时派多个 Subagent 分头干活，比串行快得多 
**3.模型灵活**：子任务可以用更快更便宜的模型，没必要所有事都用最贵的

## 三个内置 Subagent

不需要配置，Agent 自动调用：

| 名称          | 干什么          | 为什么要隔离                                         |
| ----------- | ------------ | ---------------------------------------------- |
| **Explore** | 搜索和分析代码库     | 一次搜索可能产生几千行结果，用快模型并行跑 10 次搜索，比主 Agent 串行跑一次还便宜 |
| **Bash**    | 跑一串 Shell 命令 | 命令输出动辄几百行日志，塞在主对话里会挤掉有用上下文                     |
| **Browser** | 通过 MCP 控制浏览器 | DOM 快照和截图噪音太多，子进程过滤后只返回关键信息                    |

## 自定义 Subagent

在.cursor/agents/下创建 Markdown 文件：

```json
name: verifier
description: 验证已完成的工作。任务标记完成后使用，确认实现确实可用。
model: fast
---
你是一个严格的验证者。不要轻信表面声明。
被调用时：
1. 识别声称已完成的内容
2. 运行相关测试验证功能是否正常
3. 检查边界情况
4. 报告：哪些通过了，哪些还有问题
   
```

几个关键字段：

-model：fast（便宜快速）、inherit（跟主 Agent 一样）、或指定模型 ID -readonly: true：只读模式，防止 Subagent 乱改文件 -is_background: true：后台运行，不阻塞主 Agent

触发方式有两种： 
-自动：Agent 根据任务复杂度和 Subagent 的 description 判断是否委派。description 里写 "use proactively" 可以鼓励自动触发。 
-手动：聊天里输入/verifier 确认登录流程已完成。

## 什么时候该用 Subagent，什么时候该用 Skill

|用 Subagent|用 Skill|
|---|---|
|任务耗时长，需要隔离上下文|一次性完成的单一任务|
|需要并行跑多个工作流|不需要独立上下文窗口|
|多步骤任务需要专业知识|想要快速、可重复的操作|

别为了 "生成 changelog" 这种简单任务创建 Subagent，用 Skill 就够了。Subagent 的优势在隔离和并行，简单任务反而比主 Agent 慢（因为它要从零开始收集上下文）。

---

# MCP：给 Agent 装外部插件

## 是什么

MCP（Model Context Protocol）是一套开放协议，让 Cursor 能连接外部工具和数据源。你可以理解为给 Agent 装插件——GitHub、Notion、Figma、数据库、监控平台，都能通过 MCP 接进来。

MCP 不是 Cursor 独有的标准，其他支持 MCP 的工具也能用同一套 Server。

## 三种传输方式

| 方式                | 运行位置  | 适合场景              |
| ----------------- | ----- | ----------------- |
| `stdio`           | 本地    | 单人开发，Cursor 直接管进程 |
| `SSE`             | 本地或远程 | 多人共享，部署为服务        |
| `Streamable HTTP` | 本地或远程 | 多人共享，支持 OAuth     |

## 配置方法

在.cursor/mcp.json（项目级）或~/.cursor/mcp.json（全局）里添加：

本地 stdio 服务器（以 GitHub 为例）：

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

远程 SSE 服务器：

```json
{
  "mcpServers": {
    "notion": {
      "url": "https://mcp.notion.com/mcp"
    }
  }
}
```

配置里支持变量插值：${env:NAME}读环境变量，${workspaceFolder}读项目根目录，${userHome}读用户主目录。

**别把 API Key 硬编码在配置里**。用${env:XXX}引用环境变量，或者用envFile指定.env文件路径（仅 stdio 类型支持）。

## 实际使用

配置好之后，MCP 工具会自动出现在 Agent 的可用工具列表里。Agent 聊天时会根据需要自动调用，你也可以在对话中直接说"用 GitHub MCP 查一下这个 PR 的评论"。

默认每次调用工具前会弹确认框，如果你信任这个 MCP Server，可以开启 Auto-run 跳过确认。

MCP Server 还能返回图片（截图、图表），Agent 会把图片加到对话里分析。

## 常见问题速查

**-MCP Server 崩了怎么办？**Cursor 会在聊天里显示错误，其他 Server 不受影响。查日志：Cmd+Shift+U→ Output 面板 → 选 "MCP Logs"。 
**-想临时关掉某个 Server？**Settings → Features → Model Context Protocol，点开关就行，不用删配置。 
-更新 npm 类型的 Server？先移除 →npm cache clean --force→ 重新添加。

---

# 四者怎么配合：一个实际场景

假设你的团队有这样的需求：React + TypeScript 项目，用 Tailwind，部署到 AWS，代码托管在 GitHub。

你可以这样组合：

1.**Rules**（.cursor/rules/）： -react-style.mdc：组件规范、命名约定、Tailwind 用法（globs: "src/**/*.tsx"） -api-validation.mdc：API 层必须用 zod 做入参校验（globs: "src/api/**"）

2.**Skills**（.cursor/skills/）： -deploy-aws/：包含部署脚本和校验脚本，Agent 听到"部署"就自动调用 -create-component/：包含组件模板，/create-component手动触发

3.**Subagents**（.cursor/agents/）： -verifier.md：每次功能开发完自动跑测试验证 -security-auditor.md：碰到支付、认证相关代码时自动审查

4.**MCP**（.cursor/mcp.json）： - GitHub Server：查 PR、看 Issue、读代码评论 - Sentry Server：查生产错误日志和堆栈

---

# 选型速查表

| 你的需求                   | 用什么                                                                 |
| ---------------------- | ------------------------------------------------------------------- |
| Agent 每次都忘记我的编码规范      | Rules（Always Apply）                                                 |
| 特定目录下的文件有特殊约定          | Rules（Apply to Specific Files + globs）                              |
| 想封装一套可复用的操作流程（含脚本）     | Skills                                                              |
| 任务复杂、要并行、怕中间输出撑爆上下文    | Subagents                                                           |
| 需要连接外部 API / 工具 / 数据库  | MCP                                                                 |
| 想让 Agent 在特定场景自动调用某套流程 | Skills（写好 description）或 Subagents（description 里加 "use proactively"） |
| 一个简单的一次性操作             | 什么都不用配，直接跟 Agent 说就行                                                |


# 最后几条建议

-从 Rules 开始。发现 Agent 反复犯同一个错，加一条 Rule 就够了。别上来就搞 Skill + Subagent 全家桶。 
-Rules 提交到 Git。团队共享，有人发现 Agent 出错就更新规则，整个团队受益。 -Skill 的 description 决定生死。写不好，Agent 就不会自动调用。写明"什么场景用"比"这个 Skill 做什么"更关键。 
-Subagent 别贪多。从 2-3 个聚焦的 Subagent 开始，只有出现明确的新场景再加。50 个模糊的 Subagent 等于没有。 
-MCP Server 注意安全。只装可信来源的 Server，API Key 给最小权限，处理敏感数据优先用 stdio 本地跑。