> 大家好，我是民酱。Agent Skill 工程系列发出后，想到还有点儿东西没补充："Skills 很好，但我连 Claude Code 都还没用过，该从哪里开始？"  
> 所以我补了这个第零章——聊聊让我们走到 Skills 面前的那个东西：Vibe Coding 和 Agentic Engineering。

2025 年 2 月，Andrej Karpathy 发了一条推文，创造了一个新词。一年后的今天，这个词已经进了柯林斯词典，成了 2025 年度词汇，也彻底改变了我们写代码的方式。

这一章，我把 Vibe Coding 的来龙去脉讲清楚，重点拆解两个最主流的终端工具——Claude Code 和 Codex CLI 的使用方式，分享一些实战中的通用经验，最后解释一个关键问题：为什么 Vibe Coding 用到深处，你一定会需要 Agent Skills？

速览：

1. 什么是 Vibe Coding——从 Karpathy 的推文到柯林斯词典年度词汇
    
2. 两大核心工具——Claude Code 与 Codex CLI 的安装与使用
    
3. 实战经验——入门、进阶与四个必踩的坑
    
4. 从 Vibe Coding 到 Agent Skills——为什么需要这个系列
    
5. 附录：OpenClaw 简介
6. 
## 第一部分：什么是 Vibe Coding？

### 一条推文引发的范式转变

2025 年 2 月 2 日，前 OpenAI 联合创始人、前特斯拉 AI 负责人 Andrej Karpathy 在 X（原 Twitter）上写道：

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."
> 
> "有一种新的编程方式，我称之为 '氛围编程'——你完全沉浸在感觉里，拥抱指数级增长，忘记代码的存在。"

这条推文迅速引爆了开发者社区。一年之内：

- 柯林斯词典 将 "vibe coding" 列为 2025 年度词汇
    
- Y Combinator 披露其 2025 冬季批次中，25% 的创业公司代码库超过 95% 由 AI 生成
    
- GitHub 报告显示 AI 辅助完成的代码提交占比突破 40%
    

### 核心定义

Vibe Coding = 用自然语言描述需求 → AI 生成代码 → 不逐行审查，凭结果判断。

它和传统 AI 辅助编程的关键区别在于：你接受自己不完全理解 AI 写的代码。

|特征|传统编程|AI 辅助编程|Vibe Coding|
|---|---|---|---|
|代码来源|人手写|AI 建议 + 人审查|AI 生成 + 人验收|
|理解程度|逐行理解|理解后采纳|接受不完全理解|
|交互方式|键盘输入代码|Tab 补全 + 人工修改|对话描述需求|
|质量保证|人工 Code Review|人工审查 + AI 辅助|运行结果 + 追问修正|
|典型工具|IDE + 搜索引擎|Copilot 自动补全|Claude Code / Codex CLI|

### Simon Willison 的重要澄清

一个经常被混淆的点：不是所有用 AI 写代码都叫 Vibe Coding。

开发者 Simon Willison（Django 联合创始人）明确区分了两种模式：

|模式|定义|适用场景|
|---|---|---|
|Vibe Coding|不审查代码，凭感觉和结果判断|原型、内部工具、个人项目、一次性脚本|
|AI 辅助编程|用 AI 生成代码，但逐行审查和理解|生产系统、团队协作、长期维护的项目|

核心洞察：Vibe Coding 不是偷懒，是一种有意识的取舍。 当你写一个周末 hackathon 项目时，逐行审查 AI 代码是浪费时间；当你维护一个千人使用的系统时，Vibe Coding 就是在埋雷。知道什么时候用、什么时候不用，才是关键。

### 演进路径：从 Vibe Coding 到 Agentic Engineering

     Karpathy 自己在 2026 年初也更新了看法：

> "Vibe coding is passé."  
> "氛围编程已经过时了。"

他提出了新的方向——Agentic Engineering（代理工程）：

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/Dia2nuTg1COF5FIZXbAX0qzFZD6eH6Im0QE2X5NqscTCLib3Mwmiccc5xh58Nsia4US2dHsvOwWfjFO5etg7uAfPZEibKB1VmOL9065cP8lmUeVM/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

这条演进路径恰好对应了本系列的主线。 Vibe Coding 是入口（本章），Agent Skills 是从 Vibe Coding 走向 Agentic Engineering 的桥梁（第 1-3 章）。

---

## 第二部分：两大核心工具——Claude Code & Codex CLI

Vibe Coding 的体验好不好，很大程度取决于你用什么工具。目前终端侧最主流的两个选择是 Anthropic 的 Claude Code 和 OpenAI 的 Codex CLI。

### Claude Code

定位： Anthropic 官方推出的终端 AI 编程助手。不是 IDE 插件，而是直接在终端里运行的 Agent——它能读写文件、执行命令、操作 Git、调用 MCP 服务。

安装：

# 安装（需要 Node.js 18+）  
npm install -g @anthropic-ai/claude-code  
# 进入项目目录，启动  
cd your-project  
claude

首次启动会引导你登录 Anthropic 账号（或输入 API Key）。

核心能力：

|能力|说明|
|---|---|
|代码生成与修改|理解整个项目结构，跨文件修改|
|终端命令执行|运行测试、安装依赖、构建项目|
|Git 操作|创建分支、提交、创建 PR|
|MCP 集成|连接外部服务（数据库、API、第三方工具）|
|Agent Skills|原生支持 SKILL.md 标准|
|上下文管理|/compact 压缩上下文、自动摘要|

典型使用流程：

你：帮我给这个 FastAPI 项目添加用户认证功能，用 JWT  
  
Claude Code：  
1. 分析项目结构（读取 main.py、requirements.txt 等）  
2. 安装依赖（python-jose、passlib、bcrypt）  
3. 创建 auth/ 模块（models、schemas、utils、router）  
4. 修改 main.py 注册路由  
5. 添加测试文件  
6. 运行测试确认通过  
  
你：看起来不错，帮我提交一下  
  
Claude Code：  
git add auth/ tests/ main.py requirements.txt  
git commit -m "feat: add JWT authentication"

关键命令速查：

|命令|作用|
|---|---|
|`claude`|启动交互模式|
|`claude "做某事"`|单次执行（非交互）|
|`/compact`|压缩上下文（对话太长时用）|
|`/clear`|清空上下文重新开始|
|`/cost`|查看当前会话消耗的 Token|
|`/doctor`|检查环境和配置|
|`Shift+Tab`|切换 Plan Mode（先规划再执行）|

> 更多使用技巧，参考Ahthropic官方之旅：https://code.claude.com/docs/zh-CN/overview

### Codex CLI（用的不是很多，不做过多介绍）

定位： OpenAI 官方推出的终端编码 Agent。核心特色是沙盒隔离执行——所有代码修改在安全沙盒中运行，确认后才写入本地文件。

安装：

# 安装（需要 Node.js 22+）  
npm install -g @openai/codex  
# 设置 API Key  
export OPENAI_API_KEY="your-key"  
# 启动  
codex

核心能力：

|能力|说明|
|---|---|
|代码生成与修改|理解项目结构，跨文件操作|
|沙盒执行|所有操作在隔离环境中运行，安全性更高|
|多模型支持|支持 OpenAI 全系列模型（o3、o4-mini、GPT-4.1 等）|
|多权限模式|suggest / auto-edit / full-auto 三级权限|
|Agent Skills|支持 SKILL.md 标准|

三级权限模式：

|模式|行为|适用场景|
|---|---|---|
|suggest（默认）|只建议，不修改文件|审查和学习|
|auto-edit|自动编辑文件，执行命令需确认|日常开发|
|full-auto|自动编辑+自动执行|信任的批量操作|

# 指定权限模式  
codex --approval-mode full-auto "把所有 var 替换成 const"

> 更多使用技巧，参考OpenAI官方之旅：https://openai.com/business/guides-and-resources/how-openai-uses-codex/

### Claude Code vs Codex CLI 对比

|维度|Claude Code|Codex CLI|
|---|---|---|
|厂商|Anthropic|OpenAI|
|最强模型|Claude-Opus-4.6|GPT-5.3-Codex|
|安装|npm install -g @anthropic-ai/claude-code|npm install -g @openai/codex|
|MCP 支持|原生深度集成|支持|
|Skills 支持|原生 SKILL.md + 插件系统|支持 SKILL.md 标准|
|权限模式|按工具逐一授权|suggest / auto-edit / full-auto|
|计费方式|Anthropic 订阅 或 API Key|OpenAI API Key|

怎么选？

你的情况是什么？  
│  
├─ 重视安全性，希望所有操作可控可回滚  
│  └─ → Codex CLI（沙盒隔离）  
│  
├─ 需要连接外部服务（数据库、API、第三方工具）  
│  └─ → Claude Code（MCP 生态）  
│  
├─ 想用 Agent Skills 系统  
│  └─ → Claude Code（原生最强）或 Codex CLI（基础支持）  
│  
├─ 团队已有 OpenAI 订阅/API  
│  └─ → Codex CLI（成本最优）  
│  
├─ 团队已有 Anthropic 订阅  
│  └─ → Claude Code（成本最优）  
│  
└─ 都想试试？  
   └─ → 两个都装，根据任务切换  
      Vibe Coding 的精髓就是用最顺手的工具

### 其他值得关注的工具

除了终端工具，IDE 侧也有强大的 Vibe Coding 方案：

|工具|定位|一句话|
|---|---|---|
|Cursor|AI 原生 IDE|最流行的 Vibe Coding IDE，Composer 模式体验丝滑|
|Windsurf|AI IDE（原 Codeium）|Cascade 模式 + 原生 Skills 支持|
|Kiro|AWS Agentic IDE|Spec 驱动开发，Steering + Skills|
|GitHub Copilot|VS Code 内置|Agent Mode 支持 SKILL.md 标准|

这些工具之间不是互斥的——很多开发者会同时用 Cursor 做日常开发 + Claude Code 做复杂任务和 Git 操作。（除此之外，像Trae、Qoder等一些列国内大厂开发的IDE也都非常不错）

---

## 第三部分：Vibe Coding 实战经验

### 入门阶段：前三天最重要的事

1. 从小项目开始

不要一上来就拿公司核心项目开刀。找一个周末项目、一个内部工具、一个自动化脚本——先建立信任感。

2. 先写 CLAUDE.md（或 AGENTS.md）

这是最容易被忽略、但收益最大的一步。在项目根目录创建一个 CLAUDE.md，告诉 AI 你的项目是什么：

# CLAUDE.md  
## 项目简介这是一个基于 FastAPI 的用户管理系统。  
## 技术栈  
- Python 3.11 + FastAPI  
- PostgreSQL + SQLAlchemy  
- JWT 认证  
## 代码规范  
- 变量名用 snake_case  
- API 路由前缀统一 /api/v1/  
- 所有接口需要类型注解  
  
## 运行方式  
uvicorn app.main:app --reload --port 8000

这个文件会被 Claude Code / Codex 自动读取，相当于一次性告诉 AI"你在哪、你在干嘛"。

3. 学会管理上下文

对话越长，AI 的表现越差——这不是工具的 bug，是上下文窗口的物理限制。

|场景|操作|
|---|---|
|对话超过 20 轮|用 /compact 压缩上下文|
|要切换完全不同的任务|用 /clear 清空重开|
|发现 AI 开始"胡说"|大概率是上下文污染，/clear 后重试|

### 进阶阶段：提升效率的关键技巧

1. 善用 Plan Mode

对于复杂任务，不要直接让 AI 动手。先切换到 Plan Mode（Claude Code 中按 Shift+Tab），让 AI 先出方案，确认后再执行。

你（Plan Mode）：我想把项目从 REST API 重构成 GraphQL，请先出个方案  
  
Claude Code：  
## 重构方案  
1. 安装依赖：strawberry-graphql  
2. 创建 schema/ 目录，定义类型  
3. 迁移 5 个现有端点  
4. 保留 REST 兼容层  
5. 更新测试  
预估影响 12 个文件。  
  
你：方案 OK，开始执行

6. 分步提交，而不是一次性大改

差的方式："帮我重构整个项目"  
好的方式：  
  → "先帮我把 auth 模块提取出来"  
  → "OK，提交一下。然后把 user 模块也提取出来"  
  → "OK，提交。现在把路由整理一下"

每一步都有 git commit，出问题可以回滚。这不是 AI 的限制，而是工程的基本功。

3. 让 AI 帮你写测试

Vibe Coding 最大的风险是"不知道代码对不对"。最简单的对策：让 AI 写完功能后，紧接着写测试。

你：帮我写完 auth 模块后，顺便写一套单元测试

测试通过 ≠ 代码正确，但测试不通过 ≈ 代码一定有问题。

4. 给需求文档，而不是给老代码

这是一条血泪经验：与其让 AI 改你的老代码，不如把老需求文档给它，让它重新实现。

差的方式："帮我重构这个 500 行的 utils.py" 

好的方式："这是我们的需求文档，请基于当前技术栈重新实现这个模块"

为什么？老代码里往往有大量历史包袱——层层嵌套的 if-else、早已不需要的兼容逻辑、几代人叠加的补丁。AI 在这种"修补"模式下很容易迷失在嵌套里，改一处崩三处。

但如果你给的是需求文档（这个模块要做什么、输入输出是什么、边界条件有哪些），AI 会从零开始写一个干净的实现——大概率比原来的代码结构更清晰。

> 说句大实话：现在模型的解耦能力，可能比写出那些嵌套代码的人类更强。与其让 AI 在泥潭里修修补补，不如直接让它在干净地基上重建。

### 四个必踩的坑（以及为什么需要 Agent Skills）

用 Vibe Coding 时间长了，你一定会遇到这四个问题：

坑 1："AI 越写越偏"

对话到第 30 轮，AI 开始忘记你之前说的规范，生成的代码风格越来越不一致。

> 根因：上下文窗口污染。早期对话被压缩或丢失，AI 只看到最近的上下文。

坑 2："重复犯同一个错"

每次新对话，AI 都会犯一样的错误。你纠正过一百遍了，但它不记得。

> 根因：缺少持久化记忆。对话结束就遗忘。CLAUDE.md 能缓解但不够。

坑 3："简单任务完美，复杂业务翻车"

写一个 CRUD 接口完美无缺，但涉及到你的业务逻辑（比如特定的审批流程、数据校验规则），AI 就开始瞎编。

> 根因：缺少领域知识注入。AI 有通用编程知识，但没有你的业务知识。

坑 4："每次都要重新解释"

"我们的 API 要用这种返回格式""我们的测试要用 pytest 不要 unittest""部署到生产要先跑这三个检查"——每个新对话都要重复一遍。

> 根因：没有可复用的指令集。知识停留在脑子里，没有显性化。

这四个坑，恰好对应 Agent Skills 解决的四个问题。

|Vibe Coding 的坑|Agent Skills 的解法|
|---|---|
|上下文污染|渐进式披露<br><br>——三层加载，不会撑爆上下文|
|缺少记忆|持久化 Skill 文件<br><br>——一次定义，永久可用|
|缺少领域知识|嵌入专业知识<br><br>——把业务逻辑写进 Skill|
|重复解释|标准化复用<br><br>——一次编写，跨项目跨工具复用|

这就是为什么 Vibe Coding 用到深处，你一定会走向 Agent Skills。

---

## 第四部分：从 Vibe Coding 到 Agent Skills——为什么需要这个系列

### 演进全景

|阶段|核心能力|局限性|代表工具|
|---|---|---|---|
|Vibe Coding|自然语言→代码|不可复用、无记忆|Claude Code、Codex、Cursor|
|Prompt Engineering|精心设计提示词|难以共享、无版本管理|各种 Prompt 模板|
|Agent Skills|模块化能力封装|需要学习构建方法|SKILL.md 标准|
|Agentic Engineering|AI 自主规划执行|生态仍在建设中|Skills + MCP + Agent SDK|

---

## 附录：OpenClaw 简介

### 什么是 OpenClaw？

OpenClaw 是一个开源个人 AI Agent，由奥地利开发者 Peter Steinberger 独立开发。它不是编码工具，而是一个通用任务自动化 Agent——通过 WhatsApp、Telegram、Slack、Discord 等消息平台与你交互，帮你处理日常任务。

可以理解为：如果 Claude Code 是你的 AI 编程搭档，OpenClaw 就是你的 AI 生活助手。

### 为什么值得关注？

- 10 天开发，175k+ GitHub Stars——GitHub 历史上增长最快的开源项目之一
    
- Vibe 理念的延伸——把"用自然语言驱动 AI 完成任务"从编码扩展到日常生活
    
- 本地优先——Gateway 运行在本地，数据完全自主
    
- Skills 系统——OpenClaw 有自己的技能系统（ClawHub），与 Agent Skills 理念一脉相承
    

### 与 Vibe Coding 的关系

OpenClaw 代表了 Vibe Coding 理念从编码领域向通用任务自动化的自然延伸。当你习惯了用自然语言让 Claude Code 写代码，你也会自然地想用同样的方式让 AI 帮你处理邮件、管理日程、分析数据——这正是 OpenClaw 在做的事情。

### 个人体感

实际用下来，OpenClaw 更适合做生活助手——管日程、查信息、处理消息，这些它做得很顺滑。但如果你指望它像 Claude Code 一样帮你写代码、改架构，体验会差不少，会浪费大量的 tokens 在修改代码上面。

不过我觉得 OpenClaw 真正有意思的地方在于：它本身就是一个专属于你的好的产品经理或项目经理。

什么意思？好的产品经理不只是传话筒，而是能把模糊的想法变成清晰的需求。OpenClaw 做的正是这件事——当你通过飞书随口说一句"我想让用户能批量导出数据"，它不会原封不动地转发，而是会追问你：导出什么格式？有没有权限限制？数据量大的时候怎么处理？最终输出一份结构化的需求描述。

这份需求描述，天然就是 Claude Code 能直接消费的高质量输入。不需要开发者再做一轮"翻译"。

传统链路：业务方（模糊想法）→ 产品经理（人工整理）→ 开发者（理解+实现） 新链路：  业务方（模糊想法）→ OpenClaw（AI 产品经理：追问、拆解、结构化）→ Claude Code（直接实现）

> 一句话：OpenClaw 的价值不是"把文档丢给 Claude Code"，而是它本身就能把模糊需求变成清晰需求。 当你的 AI 产品经理足够好的时候，AI 工程师自然就能接住。（OpenClaw+Claude Code这样的AgentTeam使用方式，我们后面再聊）

---

参考资料

1. Andrej Karpathy - Vibe Coding 原始推文（2025.02）  
    x.com/karpathy/status/1886192184808149383
    
2. Vibe Coding is passé - Karpathy 的 Agentic Engineering 更新  
    thenewstack.io/vibe-coding-is-passe
    
3. Collins Dictionary - 2025 Word of the Year: Vibe Coding  
    collinsdictionary.com/word-of-the-year
    
4. Simon Willison - Not all AI-assisted programming is vibe coding  
    simonwillison.net/2025/Mar/19/vibe-coding
    
5. Claude Code 官方文档  
    docs.anthropic.com/en/docs/claude-code
    
6. Codex CLI 官方文档  
    github.com/openai/codex
    
7. What is Vibe Coding - Google Cloud  
    cloud.google.com/discover/what-is-vibe-coding
    
8. What is Vibe Coding - IBM  
    ibm.com/think/topics/vibe-coding
    
9. Vibe Coding vs Agentic Coding - Rocket.new  
    rocket.new/blog/vibe-coding-vs-agentic-coding
    
10. OpenClaw GitHub  
    github.com/openclaw/openclaw
    
11. From Clawdbot to OpenClaw - CNBC  
    cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook
    
12. Vibe Coding 彻底火了 - 博客园  
    cnblogs.com/txw1958/p/18791536
    
13. DataWhaleChina vibe-vibe - 第一个系统性 Vibe Coding 教程  
    github.com/datawhalechina/vibe-vibe
    

作者注： 本文前面几篇的补充，涉及比较基础的开发工具的使用。Agent Skills 系列后续章节将深入探讨如何用 Skills 系统性解决 Vibe Coding 中遇到的瓶颈。