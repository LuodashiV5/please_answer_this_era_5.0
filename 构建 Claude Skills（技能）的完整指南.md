
## 内容

- 介绍
    
- 第 1 章：基础知识
    
- 第 2 章：规划设计
    
- 第 3 章：测试和迭代
    
- 第 4 章：分发与共享
    
- 第 5 章：模式和故障排除
    
- 第 6 章：资源和参考资料
    
- 参考 A：快速清单
    
- 参考 B：YAML Frontmatter
    
- 参考 C：完整的 Skill（技能）示例
    

---

## 介绍

Skill（技能） 是一组指令，打包为一个简单的文件夹，用于教 Claude 如何处理特定任务或 workflow（工作流）。技能是根据您的特定需求定制 Claude 的最强大方法之一。技能可以让您教 Claude 一次，每次都受益，而不是在每次对话中重新解释您的偏好、流程和领域专业知识。

当您拥有可重复的 workflow（工作流）时，Skill（技能）就会变得非常强大：根据规范生成前端设计、使用一致的方法进行研究、创建遵循团队风格指南的文档或编排多步骤流程。它们与 Claude 的内置功能（如代码执行和文档创建）配合良好。对于那些构建 MCP 集成的人来说，技能添加了另一个强大的层，有助于将原始工具访问转变为可靠、优化的工作流程。

本指南涵盖了培养有效 Skill（技能）所需了解的所有内容，从规划和结构到测试和分发。无论您是为自己、团队还是社区培养技能，您都会在书中找到实用的模式和现实示例。

### 你将学到什么

- Skill（技能）结构的技术要求和最佳实践
    
- 独立 Skill（技能）和 MCP 增强 workflow（工作流）的模式
    
- 我们看到的模式在不同的用例中运行良好
    
- 如何测试、迭代和分配您的 Skill（技能）
    

### 这是给谁的

- 希望 Claude 始终遵循特定 workflow（工作流）的开发人员
    
- 希望 Claude 遵循特定 workflow（工作流）的高级用户
    
- 希望标准化 Claude 在整个组织中的工作方式的团队
    

### 本指南的两条路径

**培养独立 Skill（技能）？** 重点关注基础知识、规划和设计以及类别 1-2。

**增强 MCP 集成？** “Skill（技能） + MCP”部分和类别 3 适合您。

两种路径具有相同的技术要求，但您可以选择与您的用例相关的内容。

**您将从本指南中获得什么：** 到最后，您将能够一次性培养实用 Skill（技能）。使用技能创建器构建和测试您的第一个工作技能预计需要大约 15-30 分钟。

---

## 第 1 章：基础知识

### 什么是 Skill（技能）？

Skill（技能）是一个包含以下内容的文件夹：

-  **SKILL.md**（required，必需）：包含 YAML frontmatter 的 Markdown 指令
    
-  **scripts/**（optional，可选）：可执行代码（Python、Bash 等）
    
-  **references/**（optional，可选）：按需加载的文档
    
-  **assets/**（optional，可选）：输出中使用的模板、字体、图标
    

### 核心设计原则

#### 渐进式披露

Skill（技能）采用三级系统：

-  **第一级（YAML frontmatter）：** 始终在 Claude 的 system prompt（系统提示）中加载。为 Claude 提供足够的信息，让他知道何时应该使用每种 Skill（技能），而无需将所有技能加载到 context（上下文）中。
    
-  **第二级（SKILL.md body）：** 当 Claude 认为该 Skill（技能）与当前任务相关时加载。包含完整的说明和指导。
    
-  **第三级（链接文件）：** 捆绑在 Skill（技能）目录中的其他文件，Claude 可以选择仅根据需要导航和发现。
    

这种渐进式披露在保持专业能力的同时，最大限度降低了 token（令牌）消耗。

#### 可组合性

Claude 可以同时加载多种 Skill（技能）。您的技能应该与其他人一起很好地发挥作用，而不是假设它是唯一可用的能力。

#### 可移植性

Skill（技能）在 Claude.ai、Claude Code 和 API 中的作用是相同的。创建一次技能后，只要环境支持该技能所需的任何依赖项，它就可以在所有表面上运行而无需修改。

### 对于 MCP 构建者：Skill（技能） + connector（连接器）

> 无需 MCP 即可培养独立 Skill（技能）？跳至规划与设计 - 您稍后可以随时返回此处。

如果您已经有一个工作 MCP 服务器，那么您已经完成了最困难的部分。Skill（技能）是最上面的知识层，捕获您已经知道的 workflow（工作流）和最佳实践，因此 Claude 可以一致地应用它们。

#### 厨房的比喻

**MCP** 提供专业厨房：使用工具、原料和设备。

**Skill（技能）** 提供秘诀：如何创造有价值的东西的分步说明。

它们共同使用户能够完成复杂的任务，而无需自己弄清楚每一步。

#### 他们如何合作

| |MCP（连接）|Skill（技能）（知识）|
|---|---|---|
|**目的**|将 Claude 连接到您的服务（Notion、Asana、Linear 等）|教 Claude 如何有效地使用您的服务|
|**功能**|提供实时数据访问和工具调用|捕获 workflow（工作流）和最佳实践|
|**范围**|Claude 能做什么|Claude 应该怎么做|

#### 为什么这对您的 MCP 用户很重要

**没有 Skill（技能）：**

- 用户连接您的 MCP 但不知道下一步该做什么
    
- 支持票询问“我如何与您的集成进行 X”
    
- 每次对话都从头开始
    
- 由于用户每次 prompt（提示）不同，结果不一致
    
- 当真正的问题是 workflow（工作流）指导时，用户责怪你的 connector（连接器）
    

**拥有 Skill（技能）：**

- 预先构建的 workflow（工作流）在需要时自动激活
    
- 一致、可靠的工具使用
    
- 嵌入每次交互的最佳实践
    
- 降低集成的学习曲线
    

---

## 第 2 章：规划设计

### 从用例开始

在编写任何代码之前，确定您的 Skill（技能）应该启用的 2-3 个具体用例。

**良好的用例定义：**
```markdown
Use Case: Project Sprint PlanningTrigger: User says "help me plan this sprint" or "create sprint tasks"
Steps:
1. Fetch current project status from Linear (via MCP)
2. Analyze team velocity and capacity
3. Suggest task prioritization
4. Create tasks in Linear with proper labels and estimates
Result: Fully planned sprint with tasks created
```

**问自己：**

- 用户想要完成什么？
    
- 这需要哪些多步骤 workflow（工作流）？
    
- 需要哪些工具（内置工具还是 MCP？）
    
- 应嵌入哪些领域知识或最佳实践？
    

### 常见 Skill（技能）用例类别

在 Anthropic，我们观察到三个常见用例：

#### 第 1 类：文档和资产创建

**用于：** 创建一致、高质量的输出，包括文档、演示文稿、应用程序、设计、代码等。

**真实示例：**前端设计 Skill（技能）（另见docx、pptx、xlsx 和 ppt 技能）

> “创建具有高设计质量的独特的生产级前端界面。在构建 Web 组件、页面、工件、海报或应用程序时使用。”

**关键技术：**

- 嵌入式风格指南和品牌标准
    
- 用于一致输出的模板结构
    
- 最终确定前的质量检查表
    
- 无需外部工具 - 使用 Claude 的内置功能
    

#### 类别 2：workflow（工作流）自动化

**用于：** 受益于一致方法的多步骤流程，包括跨多个 MCP 服务器的协调。

**真实示例：**Skill（技能）- 创造者技能

> “用于创建新 Skill（技能）的交互式指南。引导用户完成用例定义、frontmatter（前言）生成、指令编写和验证。”

**关键技术：**

- 带有验证门的分步 workflow（工作流）
    
- 常见结构的模板
    
- 内置审核和改进建议
    
- 迭代细化循环
    

#### 第 3 类：MCP 增强

**用于：** 用于增强 MCP 服务器提供的工具访问的 workflow（工作流）指南。

**真实示例：**哨兵代码审查 Skill（技能）（来自哨兵）

> “通过 MCP 服务器使用 Sentry 的错误监控数据自动分析和修复 GitHub Pull 请求中检测到的错误。”

**关键技术：**

- 按顺序协调多个 MCP 调用
    
- 嵌入领域专业知识
    
- 提供用户否则需要指定的 context（上下文）
    
- 常见 MCP 问题的错误处理
    

### 定义成功标准

你怎么知道你的 Skill（技能）正在发挥作用？

这些是理想的目标，是粗略的基准，而不是精确的阈值。力求严谨，但也要接受基于共鸣的评估元素。我们正在积极开发更强大的测量指南和工具。

**定量指标：**

-  **90% 的相关查询都会触发 Skill（技能）**
- 如何衡量：运行 10-20 个测试查询来触发您的 Skill（技能）。跟踪它自动加载的次数与需要显式调用的次数。
    
-  **完成 X 工具调用中的 workflow（工作流）**
- 如何衡量：比较启用和未启用 Skill（技能）的同一任务。计算工具调用和消耗的代币总数。

-  **每个 workflow（工作流）0 次失败的 API 调用**
- 如何测量：在测试运行期间监视 MCP 服务器日志。跟踪重试率和错误代码。
    

**定性指标：**

-  **用户不需要 prompt（提示）Claude 后续步骤**
- 如何评估：在测试过程中，注意您需要重新定向或澄清的频率。向测试版用户寻求反馈。
    
-  **workflow（工作流）无需用户修正即可完成**
- 如何评估：运行相同的请求 3-5 次。比较输出的结构一致性和质量。

-  **跨会话结果一致**
- 如何评估：新用户能否在最少的指导下首次尝试完成任务？
    

### 技术要求

#### 文件结构

`your-skill-name/   
├── SKILL.md                                 # Required - main skill file   
├── scripts/                                    # Optional - executable code      
│ 		├── process_data.py     # Example   │    
│ 		└── validate.sh              # Example   
├── references/                             # Optional - documentation   │    
│ 		├── api-guide.md          # Example   │    
		└── examples/               # Example   
└── assets/                                    # Optional - templates, etc.        
		└── report-template.md   # Example`

#### 关键规则

**SKILL.md 命名：**

- 必须恰好为 `SKILL.md`（区分大小写）
    
- 不接受任何变化（`SKILL.MD`、`skill.md` 等）
    

**Skill（技能）文件夹命名：**

- 使用 `kebab-case`：`notion-project-setup` ✅
    
- 无空格：`Notion Project Setup` ❌
    
- 无下划线：`notion_project_setup` ❌
    
- 无大写：`NotionProjectSetup` ❌
    

**无 README.md:**

- 不要将 `README.md` 包含在您的 Skill（技能）文件夹中
    
- 所有文档均以 `SKILL.md` 或 `references/` 格式显示
    
- 注意：通过 GitHub 分发时，您仍然需要为人类用户提供存储库级别的自述文件（请参阅分发和共享）。
    

### YAML frontmatter：最重要的部分

YAML frontmatter 的核心内容是 Claude 如何决定是否加载您的 Skill（技能）。把这件事做好。

#### 最低要求格式

`---   name: your-skill-name   description: What it does. Use when user asks to [specific phrases].   ---`

这就是您需要开始的全部内容。

#### 字段要求（Field requirements）

**`name`（required，必填）：**

- 使用 `kebab-case`
    
- 不要有空格或大写字母
    
- 应与 Skill（技能）文件夹名一致
    

**`description`（required，必填）：**

- 必须同时说明：
    

- Skill（技能）做什么
    
- 何时使用（触发条件）
    

- 不超过 1024 字符
    
- 不允许 XML 标签（`<` 或 `>`）
    
- 包含用户可能说出的具体任务短语
    
- 若相关，提及文件类型
    

**`license`（optional，可选）：**

- 开源 Skill（技能）时建议填写
    
- 常见值：`MIT`、`Apache-2.0`
    

**`compatibility`（optional，可选）：**

- 1-500 个字符
    
- 用于说明环境要求：目标产品、系统依赖、网络访问要求等
    

**`metadata`（optional，可选）：**

- 任意自定义键值对
    
- 建议字段：`author`、`version`、`mcp-server`
    
- 示例：
    

`metadata:  author: ProjectHub  version: 1.0.0  mcp-server: projecthub`

#### 安全限制

**frontmatter 中禁止：**

- XML 尖括号 (`<``>`)
    
-  `name` 包含 `claude` 或 `anthropic` 前缀（保留命名空间）
    

**原因：**`frontmatter` 会进入 Claude 的 system prompt（系统提示）；恶意内容可能造成 prompt 注入。

### 写作有效技巧

#### 描述字段

根据 Anthropic 的工程博客 的说法：“这些元数据...为 Claude 提供了足够的信息，让他知道何时应该使用每种 Skill（技能），而无需将所有技能加载到 context（上下文）中。”这是渐进式披露的第一级。

**结构：**

`[What it does] + [When to use it] + [Key capabilities]`

**良好描述的示例：**

`# Good - specific and actionable   description: Analyzes Figma design files and generates developer handoff  documentation. Use when user uploads .fig files, asks for "design specs",  "component documentation", or "design-to-code handoff".      # Good - includes trigger phrases   description: Manages Linear project workflows including sprint planning,  task creation, and status tracking. Use when user mentions "sprint",  "Linear tasks", "project planning", or asks to "create tickets".      # Good - clear value proposition   description: End-to-end customer onboarding workflow for PayFlow. Handles  account creation, payment setup, and subscription management. Use when  user says "onboard new customer", "set up subscription", or "create     PayFlow account".`

**错误描述的示例：**

`# Too vague   description: Helps with projects.      # Missing triggers   description: Creates sophisticated multi-page documentation systems.      # Too technical, no user triggers   description: Implements the Project entity model with hierarchical relationships.`

### 编写主要指令

在 frontmatter 之后，用 Markdown 写下实际的说明。

**推荐结构：**

根据您的 Skill（技能）调整此模板。将括号内的部分替换为您的具体内容。

` ---   name: your-skill   description: [...]   ---      # Your Skill Name      ## Instructions      ### Step 1: [First Major Step]   Clear explanation of what happens.      Example:   ```bash   python scripts/fetch_data.py --project-id PROJECT_ID   Expected output: [describe what success looks like]   ``` `

(Add more steps as needed)

#### Examples

##### Example 1: [common scenario]

User says: "Set up a new marketing campaign"

Actions:

1. 1. Fetch existing campaigns via MCP
    
2. 2. Create new campaign with provided parameters
    

Result: Campaign created with confirmation link

(Add more examples as needed)

#### Troubleshooting

##### Error: [Common error message]

**Cause**: [Why it happens]  
**Solution**: [How to fix]

(Add more error cases as needed)

### 说明的最佳实践

#### 具体且可操作

✅ **好：**

``    Run `python scripts/validate.py --input {filename}` to check data format.   If validation fails, common issues include:      - Missing required fields (add them to the CSV)   - Invalid date formats (use YYYY-MM-DD)    ``

❌ **不好：**

`    Validate the data before proceeding.    `

#### 包括错误处理

`## Common Issues      ### MCP Connection Failed   If you see "Connection refused":   1. Verify MCP server is running: Check Settings > Extensions   2. Confirm API key is valid   3. Try reconnecting: Settings > Extensions > [Your Service] > Reconnect`

#### 清楚地参考捆绑资源

``Before writing queries, consult `references/api-patterns.md` for:   - Rate limiting guidance   - Pagination patterns   - Error codes and handling``

#### 使用渐进式披露

让 `SKILL.md` 聚焦核心指令。将详细文档放到 `references/`，并在需要时链接引用（三级系统见核心设计原则）。

---

## 第 3 章：测试和迭代

根据您的需求，可以对 Skill（技能）进行不同严格程度的测试：

-  **Claude.ai 中的手动测试** - 直接运行查询并观察行为。快速迭代，无需设置。
    
-  **Claude Code 中的脚本化测试** - 自动执行测试用例，以便跨更改进行可重复验证。
    
-  **通过 Skill（技能）API 进行编程测试** - 构建针对定义的测试集系统运行的评估套件。
    

选择符合您的质量要求和您的 Skill（技能）可见性的方法。小团队内部使用的技能与部署到数千名企业用户的技能具有不同的测试需求。

> **专业提示：在扩展之前迭代单个任务**
> 
> 我们发现，最有效的 Skill（技能）创造者会迭代单个具有挑战性的任务，直到 Claude 成功，然后将制胜方法提取到技能中。这利用了 Claude 的情境学习，并提供比广泛测试更快的信号。一旦有了工作基础，就可以扩展到多个测试用例以实现覆盖。

### 推荐的测试方法

根据早期经验，有效的 Skill（技能）测试通常涵盖三个领域：

#### 1. 触发测试

**目标：** 确保您的 Skill（技能）在正确的时间加载。

**测试用例：**

- ✅ 触发明显的任务
    
- ✅ 触发释义请求
    
- ❌ 不会在不相关的主题上触发
    

**测试套件示例：**

`Should trigger:   - "Help me set up a new ProjectHub workspace"   - "I need to create a project in ProjectHub"   - "Initialize a ProjectHub project for Q4 planning"      Should NOT trigger:   - "What's the weather in San Francisco?"   - "Help me write Python code"   - "Create a spreadsheet" (unless ProjectHub skill handles sheets)`

#### 2. 功能测试

**目标：** 验证 Skill（技能）产生正确的输出。

**测试用例：**

- 生成有效输出
    
- API 调用成功
    
- 错误处理工作
    
- 涵盖边缘情况
    

**例子：**

`Test: Create project with 5 tasks   Given: Project name "Q4 Planning", 5 task descriptions   When: Skill executes workflow   Then:      - Project created in ProjectHub      - 5 tasks created with correct properties      - All tasks linked to project      - No API errors`

#### 3、性能对比

**目标：** 证明该 Skill（技能）相对于基线可以改善结果。

使用定义成功标准中的指标。比较可能是这样的。

**基线比较：**

`Without skill:   - User provides instructions each time   - 15 back-and-forth messages   - 3 failed API calls requiring retry   - 12,000 tokens consumed      With skill:   - Automatic workflow execution   - 2 clarifying questions only   - 0 failed API calls   - 6,000 tokens consumed`

### 使用 Skill Creator（技能创建器）

Skill Creator（技能创建器）可在 Claude.ai 的目录中使用，也可下载到 Claude Code，用于构建和迭代 Skill（技能）。如果您拥有 MCP 服务器并且了解最重要的 2-3 个 workflow（工作流），则可以一次性构建并测试功能技能，通常需要 15-30 分钟。

**创建 Skill（技能）：**

- 从自然语言描述中生成 Skill（技能）
    
- 使用 frontmatter 生成格式正确的 SKILL.md
    
- 建议触发短语和结构
    

**评审能力：**

- 标记常见问题（模糊描述、缺少触发器、结构问题）
    
- 识别潜在的过度/不足触发风险
    
- 根据 Skill（技能）的既定目的建议测试用例
    

**迭代改进：**

- 使用您的 Skill（技能）并遇到极端情况或失败后，将这些示例返回给技能创建者
    
- 示例：“使用本次聊天中确定的问题和解决方案来改进 Skill（技能）处理[特定边缘情况]的方式”
    

**使用：**

`"Use the skill-creator skill to help me build a skill for [your use case]"`

注意：Skill（技能）创建器可帮助您设计和完善技能，但不会执行自动化测试套件或生成定量评估结果。

### 基于反馈的迭代

Skill（技能）是活的文件。计划迭代基于：

**欠触发信号：**

- Skill（技能）在应该加载的时候却没有加载
    
- 用户手动启用它
    
- 支持有关何时使用它的问题
    

> **解决方案：** 在描述中添加更多细节和细微差别 - 这可能包括特别是技术术语的关键字

**过度触发信号：**

- 不相关查询的 Skill（技能）负载
    
- 用户禁用它
    
- 对目的的困惑
    

> **解决方案：** 添加负面触发器，更具体

**执行问题：**

- 结果不一致
    
- API 调用失败
    
- 需要用户更正
    

> **解决方案：** 改进指令，添加错误处理

---

## 第 4 章：分发与共享

Skill（技能）使您的 MCP 集成更加完整。当用户比较 connector（连接器）时，那些拥有技能的连接器可以提供更快的价值路径，使您比仅使用 MCP 的替代方案更具优势。

### 目前的分配模型（2026 年 1 月）

**个人用户如何获得 Skill（技能）：**

1. 1. 下载 Skill（技能）文件夹
    
2. 2. 压缩文件夹（如果需要）
    
3. 3. 通过设置 > 功能 > Skill（技能）上传到 Claude.ai
    
4. 4. 或者放在 Claude CodeSkill（技能）目录下
    

**组织级 Skill（技能）：**

- 管理员可以在整个工作区部署 Skill（技能）（2025 年 12 月 18 日发布）
    
- 自动更新
    
- 集中管理
    

### 开放标准

我们已将 Agent Skills 作为开放标准发布。与 MCP 一样，我们认为技能应该可以跨工具和平台移植 - 无论您使用 Claude 还是其他 AI 平台，相同的技能都应该有效。也就是说，某些技能旨在充分利用特定平台的功能；作者可以在技能的兼容性字段中注意到这一点。我们一直在与生态系统成员就该标准进行合作，我们对早期采用感到兴奋。

### 通过 API 使用 Skill（技能）

对于编程用例，例如构建应用程序、代理或利用 Skill（技能）的自动化 workflow（工作流），API 提供对技能管理和执行的直接控制。

**关键能力：**

-  `/v1/skills` 用于列出和管理 Skill（技能）的端点
    
- 通过 `container.skills` 参数向 Messages API 请求添加 Skill（技能）
    
- 通过 Claude Console 进行版本控制和管理
    
- 与 Claude Agent SDK 配合使用来构建自定义代理
    

**何时通过 API 与 Claude.ai 使用 Skill（技能）：**

|使用案例|最佳表面|
|---|---|
|最终用户直接与 Skill（技能）交互|Claude.ai / Claude 代码|
|开发过程中的手动测试和迭代|Claude.ai / Claude 代码|
|个人、临时 workflow（工作流）|Claude.ai / Claude 代码|
|以编程方式使用 Skill（技能）的应用程序|应用程序接口|
|大规模生产部署|应用程序接口|
|自动化管道和代理系统|应用程序接口|

注意：API 中的 Skill（技能）需要代码执行工具测试版，该工具提供运行技能所需的安全环境。

**具体实现请参见：**

- Skill（技能）API 快速入门
    
- 创建自定义 Skill（技能）
    
- Agent SDK 中的技巧
    

### 今天推荐的方法

首先使用公共存储库在 GitHub 上托管您的 Skill（技能），清除 README（对于人类访问者 - 这与您的技能文件夹分开，该文件夹不应包含 README.md）以及带有屏幕截图的示例用法。然后在 MCP 文档中添加一个部分，链接到该技能，解释为什么同时使用两者很有价值，并提供快速入门指南。

1. 1. **在 GitHub 上托管**
    

- 开源 Skill（技能）的公共存储库
    
- 清晰的自述文件和安装说明
    
- 使用示例和屏幕截图
    

3. 2. **您的 MCP 存储库中的文档**
    

- MCP 文档中的 Skill（技能）链接
    
- 解释同时使用两者的价值
    
- 提供快速入门指南
    

5. 3. **创建安装指南**
    

``## Installing the [Your Service] skill      1. Download the skill:    - Clone repo: `git clone https://github.com/yourcompany/skills`    - Or download ZIP from Releases      2. Install in Claude:    - Open Claude.ai > Settings > Skills    - Click "Upload skill"    - Select the skill folder (zipped)      3. Enable the skill:    - Toggle on the [Your Service] skill    - Ensure your MCP server is connected      4. Test:    - Ask Claude: "Set up a new project in [Your Service]"``

### 定位你的 Skill（技能）

你如何描述你的 Skill（技能）决定了用户是否理解它的价值并实际尝试它。在自述文件、文档或营销中撰写有关您的技能时，请牢记这些原则。

**关注结果，而不是功能：**

✅ **好：**

> “ProjectHub Skill（技能）使团队能够在几秒钟内设置完整的项目工作区 - 包括页面、数据库和模板 - 而不是花费 30 分钟进行手动设置。”

❌ **不好：**

> “ProjectHub Skill（技能）是一个文件夹，其中包含调用我们的 MCP 服务器工具的 YAML frontmatter 和 Markdown 指令。”

**重点介绍 MCP + Skill（技能）故事：**

> “我们的 MCP 服务器使 Claude 可以访问您的 Linear 项目。我们的 Skill（技能）可以教会 Claude 您团队的冲刺计划 workflow（工作流）。它们共同实现了人工智能驱动的项目管理。”

---

## 第 5 章：模式和故障排除

这些模式源于早期采用者和内部团队创建的 Skill（技能）。它们代表了我们见过的行之有效的常见方法，而不是规定性模板。

### 选择方法：问题优先与工具优先

可以把它想象成家得宝（Home Depot）。您走进来时可能会遇到一个问题 - “我需要修理厨房橱柜” - 员工会为您指出正确的工具。或者您可能会挑选一个新钻头并询问如何将其用于您的特定工作。

Skill（技能）的工作方式相同：

-  **问题第一：** “我需要设置一个项目工作区” - 您的 Skill（技能）以正确的顺序编排正确的 MCP 调用。用户描述结果；技能处理工具。
    
-  **工具优先：**“我已连接 Notion MCP” - 您的 Skill（技能）教会 Claude 最佳 workflow（工作流）和最佳实践。用户有权访问；该技能提供专业知识。
    

大多数 Skill（技能）都倾向于一个方向。了解哪种框架适合您的用例有助于您选择以下正确的模式。

### 模式 1：顺序 workflow（工作流）编排

**使用时间：** 您的用户需要按特定顺序执行多步骤流程。

**结构示例：**

``## Workflow: Onboard New Customer      ### Step 1: Create Account   Call MCP tool: `create_customer`   Parameters: name, email, company      ### Step 2: Setup Payment   Call MCP tool: `setup_payment_method`   Wait for: payment method verification      ### Step 3: Create Subscription   Call MCP tool: `create_subscription`   Parameters: plan_id, customer_id (from Step 1)      ### Step 4: Send Welcome Email   Call MCP tool: `send_email`   Template: welcome_email_template``

**关键技术：**

- 显式步骤排序
    
- 步骤之间的依赖关系
    
- 每个阶段的验证
    
- 失败的回滚说明
    

### 模式 2：多 MCP 协调

**在以下情况下使用：** workflow（工作流）跨越多个服务。

**示例：设计到开发的交接**

`### Phase 1: Design Export (Figma MCP)   1. Export design assets from Figma   2. Generate design specifications   3. Create asset manifest      ### Phase 2: Asset Storage (Drive MCP)   1. Create project folder in Drive   2. Upload all assets   3. Generate shareable links      ### Phase 3: Task Creation (Linear MCP)   1. Create development tasks   2. Attach asset links to tasks   3. Assign to engineering team      ### Phase 4: Notification (Slack MCP)   1. Post handoff summary to #engineering   2. Include asset links and task references`

**关键技术：**

- 清晰的相分离
    
- MCP 之间的数据传递
    
- 进入下一阶段之前进行验证
    
- 集中错误处理
    

### 模式 3：迭代细化

**在以下情况下使用：** 输出质量随着迭代而提高。

**示例：报告生成**

``## Iterative Report Creation      ### Initial Draft   1. Fetch data via MCP   2. Generate first draft report   3. Save to temporary file      ### Quality Check   1. Run validation script: `scripts/check_report.py`   2. Identify issues:    - Missing sections    - Inconsistent formatting    - Data validation errors      ### Refinement Loop   1. Address each identified issue   2. Regenerate affected sections   3. Re-validate   4. Repeat until quality threshold met      ### Finalization   1. Apply final formatting   2. Generate summary   3. Save final version``

**关键技术：**

- 明确的质量标准
    
- 迭代改进
    
- 验证脚本
    
- 知道何时停止迭代
    

### 模式 4：context（上下文）感知工具选择

**使用时间：** 相同的结果，根据 context（上下文）使用不同的工具。

**示例：文件存储**

`## Smart File Storage      ### Decision Tree   1. Check file type and size   2. Determine best storage location:    - Large files (>10MB): Use cloud storage MCP    - Collaborative docs: Use Notion/Docs MCP    - Code files: Use GitHub MCP    - Temporary files: Use local storage      ### Execute Storage   Based on decision:   - Call appropriate MCP tool   - Apply service-specific metadata   - Generate access link      ### Provide Context to User   Explain why that storage was chosen`

**关键技术：**

- 明确的决策标准
    
- 后备选项
    
- 选择的透明度
    

### 模式 5：特定领域的智能

**使用时间：** 您的 Skill（技能）增加了工具访问之外的专业知识。

**示例：财务合规性**

`## Payment Processing with Compliance      ### Before Processing (Compliance Check)   1. Fetch transaction details via MCP   2. Apply compliance rules:    - Check sanctions lists    - Verify jurisdiction allowances    - Assess risk level   3. Document compliance decision      ### Processing   IF compliance passed:  - Call payment processing MCP tool  - Apply appropriate fraud checks  - Process transaction   ELSE:  - Flag for review  - Create compliance case      ### Audit Trail   - Log all compliance checks   - Record processing decisions   - Generate audit report`

**关键技术：**

- 嵌入逻辑的领域专业知识
    
- 行动前遵守规定
    
- 全面的文档
    
- 清晰的治理
    

### 故障排除

#### Skill（技能）无法上传

**错误：“在上传的文件夹中找不到 SKILL.md”**

原因：文件未准确命名 SKILL.md

解决方案：

- 重命名为 `SKILL.md`（区分大小写）
    
- 验证：`ls -la` 应显示 `SKILL.md`
    

**错误：“无效的 frontmatter（前言）”**

原因：YAML 格式问题

常见错误：

`# Wrong - missing delimiters   name: my-skill   description: Does things      # Wrong - unclosed quotes   name: my-skill   description: "Does things      # Correct   ---   name: my-skill   description: Does things   ---`

**错误：“Invalid skill name（无效的 Skill 名称）”**

原因：`name` 中包含空格或大写字母

`# Wrong   name: My Cool Skill      # Correct   name: my-cool-skill`

#### Skill（技能）不触发

**症状：** Skill（技能）永远不会自动加载

**修复：** 修改您的描述字段。请参阅描述字段以获取好/坏的示例。

**快速清单：**

- 是不是太笼统了？ （“帮助项目”不起作用）
    
- 它是否包含用户实际会说的触发短语？
    
- 如果适用，它是否提及相关文件类型？
    

**调试方法：** 问 Claude：“你会在什么情况下触发 `[Skill 名称]`？”Claude 通常会引用 `description` 内容。根据缺失信息继续补充。

#### Skill（技能）触发过于频繁

**症状：** 不相关查询的 Skill（技能）负载

**解决方案：**

1. 1. **添加负面触发器**
    

`description: Advanced data analysis for CSV files. Use for statistical  modeling, regression, clustering. Do NOT use for simple data exploration  (use data-viz skill instead).`

1. 2. **更具体一些**
    

`# Too broad   description: Processes documents      # More specific   description: Processes PDF legal documents for contract review`

1. 3. **明确范围**
    

`description: PayFlow payment processing for e-commerce. Use specifically  for online payment workflows, not for general financial queries.`

#### MCP 连接问题

**症状：** Skill（技能）加载但 MCP 调用失败

**清单：**

1. 1. **验证 MCP 服务器是否已连接**
    

- Claude.ai：设置 > 扩展 > [您的服务]
    
- 应显示“已连接”状态
    

3. 2. **检查身份验证**
    

- API 密钥有效且未过期
    
- 授予适当的权限/范围
    
- OAuth token（令牌）已刷新
    

5. 3. **独立测试 MCP**
    

- 请 Claude 直接打电话给 MCP（无需技巧）
    
- “使用 [服务] MCP 获取我的项目”
    
- 如果失败，则问题在于 MCP 而不是 Skill（技能）
    

7. 4. **验证工具名称**
    

- Skill（技能）引用正确的 MCP 工具名称
    
- 检查 MCP 服务器文档
    
- 工具名称区分大小写
    

#### 未遵循指示

**症状：** Skill（技能）加载但 Claude 不遵循指示

**常见原因：**

1. 1. **说明过于冗长**
    

- 保持说明简洁
    
- 使用项目符号和编号列表
    
- 将详细参考移至单独的文件
    

3. 2. **隐藏说明**
    

- 将关键说明放在顶部
    
- 使用 `## Important` 或 `## Critical` 标头
    
- 如果需要，重复要点
    

5. 3. **含糊的语言**
    

`# Bad   Make sure to validate things properly      # Good   CRITICAL: Before calling create_project, verify:   - Project name is non-empty   - At least one team member assigned   - Start date is not in the past`

**高级技术：** 对于关键验证，请考虑捆绑一个以编程方式执行检查的脚本，而不是依赖于语言指令。代码是确定性的；语言解释则不然。有关此模式的示例，请参阅办公 Skill（技能）。

1. 4. **模型“懒惰”** - 添加明确的鼓励：
    

`## Performance Notes   - Take your time to do this thoroughly   - Quality is more important than speed   - Do not skip validation steps`

注意：将其添加到用户 prompt（提示）中比在 SKILL.md 中更有效。

#### 大背景问题

**症状：** Skill（技能）似乎缓慢或反应下降

**原因：**

- Skill（技能）内容太大
    
- 同时启用的 Skill（技能）过多
    
- 所有内容均已加载，而不是渐进式披露
    

**解决方案：**

1. 1. **优化 SKILL.md 大小**
    

- 将详细文档移至 `references/`
    
- 链接到参考文献而不是内联
    
- 将 SKILL.md 控制在 5,000 字以内
    

3. 2. **减少启用 Skill（技能）**
    

- 评估您是否同时启用了超过 20-50 项 Skill（技能）
    
- 建议选择性启用
    
- 考虑相关功能的 Skill（技能）“包”
    

---

## 第 6 章：资源和参考资料

如果您正在构建第一项 Skill（技能），请从 最佳实践指南 开始，然后根据需要参考 API 文档。

### 官方文档

**官方资源：**

- 最佳实践指南
    
- Skills（技能）文档
    
- API 参考
    
- MCP 文档
    

**博客文章：**

- Agent Skills 介绍
    
- 工程博客：为现实世界配备代理
    
- Skill（技能）讲解
    
- 如何创建 Claude Skills（技能）
    
- 为 Claude Code 构建 Skills（技能）
    
- 通过 Skills（技能）改进前端设计
    

### Skill（技能）示例

**公共 Skill（技能）库：**

- GitHub: anthropics/skills
    
- 包含您可以自定义的人类创建的 Skill（技能）
    

### 工具和实用程序

**Skill Creator（技能创建器）：**

- 内置于 Claude.ai 中并可用于 Claude Code
    
- 可以从描述中生成 Skill（技能）
    
- 审查并提供建议
    
- 使用：“帮助我用 Skill Creator（技能创建器）创建一个 Skill（技能）”
    

**验证：**

- Skill Creator（技能创建器）可以评估你的 Skill（技能）
    
- 询问：“回顾这项 Skill（技能）并提出改进建议”
    

### 获得支持

**对于技术问题：**

- 一般问题：Claude Developers Discord 上的社区论坛
    

**对于错误报告：**

- GitHub 问题：anthropics/skills/issues
    
- 包括：Skill（技能）名称、错误信息、重现步骤
    

---

## 参考 A：快速清单

使用此清单在上传之前和之后验证您的 Skill（技能）。如果您想要更快地开始，请使用技能创建者技能来生成您的初稿，然后浏览此列表以确保您没有错过任何内容。

### 开始之前

- 确定 2-3 个具体用例
    
- 已识别的工具（内置或 MCP）
    
- 查看了本指南和示例 Skill（技能）
    
- 计划的文件夹结构
    

### 开发期间

- 以 kebab-case 命名的文件夹
    
- SKILL.md 文件存在（准确拼写）
    
- YAML frontmatter 有 `---` 分隔符
    
- `name` 字段：`kebab-case`、无空格、无大写
    
- `description` 同时包含“做什么”和“何时触发”
    
- 任何地方都没有 XML 标签 (`<``>`)
    
- 说明清晰且可操作
    
- 包含错误处理
    
- 提供的示例
    
- 参考文献链接清晰
    

### 上传前

- 测试了明显任务的触发
    
- 测试了释义请求的触发
    
- 已验证不会在不相关的主题上触发
    
- 功能测试通过
    
- 工具集成有效（如果适用）
    
- 压缩为 .zip 文件
    

### 上传后

- 在真实对话中测试
    
- 监控欠触发/过度触发
    
- 收集用户反馈
    
- 迭代描述和说明
    
- 更新元数据中的版本
    

---

## 参考 B：YAML Frontmatter

### 必填字段

`---   name: skill-name-in-kebab-case   description: What it does and when to use it. Include specific trigger phrases.   ---`

### 所有可选字段

`name: skill-name   description: [required description]   license: MIT # Optional: License for open-source   allowed-tools: "Bash(python:*) Bash(npm:*) WebFetch" # Optional: Restrict tool access   metadata: # Optional: Custom fields  author: Company Name  version: 1.0.0  mcp-server: server-name  category: productivity  tags: [project-management, automation]  documentation: https://example.com/docs  support: support@example.com`

### 安全说明

**允许：**

- 任何标准 YAML 类型（字符串、数字、布尔值、列表、对象）
    
- 自定义元数据字段
    
- 长描述（最多 1024 个字符）
    

**禁止：**

- XML 尖括号 (`<``>`) - 安全限制
    
- YAML 中的代码执行（使用安全的 YAML 解析）
    
- 以“claude”或“anthropic”前缀命名的 Skill（技能）（保留）
    

---

## 参考 C：完整的 Skill（技能）示例

如需演示本指南中的模式的完整、可用于生产的 Skill（技能）：

-  **文档技巧** - PDF、DOCX、PPTX、XLSX 创建
    
-  **示例 Skill（技能）** - 各种 workflow（工作流）模式
    
-  **合作伙伴 Skill（技能）目录** - 查看 Asana、Atlassian、Canva、Figma、Sentry、Zapier 等各个合作伙伴的技能
    

这些存储库保持最新状态，并包含此处介绍之外的其他示例。克隆它们，根据您的用例修改它们，并将它们用作模板。