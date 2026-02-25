# Agent Skills全面介绍:如何为Agent构建可复用的能力模块



大家好，我是新壮，今天给大家分享的是 **Agent Skills**。

在大模型与 Agent 快速发展的背景下，我们正在从“AI能聊天”迈向“AI能干活“的阶段。在这个过程中，一个核心问题反复出现：

> **如何将人的技能经验让 Agent 复用， 并尽可能保持稳定可靠？**

Agent Skills，正是为了解决这个问题而提出的一种轻量级能力封装与分发机制。

------

## 引言：从 Prompt 到 Skill 的必然演进

早期的 Agent 系统，大多依赖长 Prompt 或系统提示词来"教会"模型做事。这种方式存在明显问题：

- **能力复用困难**：每次对话都重复加载指令，无法跨场景复用
- **治理成本高**：能力边界模糊，修改规则需要翻遍历史 Prompt
- **上下文浪费**：所有规则都在启动时加载，即使当前任务用不上

**实际场景举例**：
想象一个 Agent 需要处理三种任务：代码审查、PDF 解析、数据分析。用传统 Prompt 方式：

- 每次对话都重复加载三套规则（上下文浪费）
- 修改"代码审查规则"需要翻遍历史 Prompt（难以维护）
- 新成员加入 Agent 项目，无法快速理解能力边界（协作困难）

随着 Agent 开始承担真实任务（代码、文档、数据、流程自动化），我们迫切需要一种**工程化的能力单元**。

这正是 **Agent Skills** 试图解决的核心问题。

------

## Agent Skills 是什么？

**Agent Skills** 是一种用于扩展 AI Agent 能力的 **轻量级、开放格式规范**。

它的核心思想非常简单：

> **把 Agent 的能力封装为独立的、可管理的模块，而不是散落在 Prompt 中的指令。**

在 Agent Skills 中，每个 Skill 包含指令、元数据和可选资源（脚本、模板），使用一个文件夹组织在一起，其中最关键的是一个名为 **`SKILL.md`** 的文件。

Skill 可以用于：

- 描述某一类任务何时应该被使用
- 告诉 Agent 具体应该如何执行
- 附带脚本、模板或参考资料

这使得 Agent 的能力第一次具备了 **可读、可维护、可版本化** 的工程属性。

------

## 为什么要用 Agent Skills？

Skills 是可重用的、基于文件系统的资源，为 Agent 提供特定领域的专业知识：工作流、上下文和最佳实践，将通用代理转变为专家。与提示词不同（提示词是对话级别的一次性任务指令），Skills 按需加载，无需在多个对话中重复提供相同的指导。

从工程与架构角度看，Agent Skills 带来几个关键价值：

### 1. 能力解耦（Capability Decoupling）

Skill 将“模型能力”与“任务知识”解耦，使 Agent 本身保持通用，而能力通过外部 Skill 注入。

### 2. 渐进式上下文加载（Progressive Disclosure）

Agent 不再在启动时加载所有 Prompt，而是采用**三级加载机制**：

#### 第 1 级：元数据（始终加载）

**内容类型**：YAML Frontmatter 中的 `name` 和 `description`

**加载时机**：Agent 启动时

**令牌成本**：每个 Skill 约 100 tokens

```markdown
- 1 ---
- 2 name: pdf-processing
- 3 description: 从 PDF 文件中提取文本和表格、填充表单、合并文档。在处理 PDF 文件或用户提及 PDF、表单或文档提取时使用。
- 4 ---
```

这一级是**轻量级发现机制**——Agent 只知道每个 Skill 的存在以及何时使用它，可以安装大量 Skills 而不会产生显著的上下文成本。

#### 第 2 级：指令（触发时加载）

**内容类型**：SKILL.md 的主体

**加载时机**：当用户请求与 Skill description 匹配时

**令牌成本**：不到 5k tokens

```markdown
- 1 # PDF 处理
- 2 ## 快速入门
- 3 使用 pdfplumber 从 PDF 中提取文本：
- 4 ```python
- 5 import pdfplumber
- 6 with pdfplumber.open("document.pdf") as pdf:
- 7    text = pdf.pages[0].extract_text()
- 8
```

Agent 通过 bash 从文件系统读取 SKILL.md。只有这样，此内容才会进入上下文窗口。

#### 第 3 级：资源和代码（按需加载）

**内容类型**：指令、代码和资源
**加载时机**：仅在被引用时
**令牌成本**：实际上无限制（不消耗上下文）

```markdown
pdf-skill/
├── SKILL.md        # 主要指令
├── FORMS.md        # 表单填充指南
├── REFERENCE.md    # 详细 API 参考
└── scripts/    
	└── fill_form.py # 实用脚本
```

- **指令**：其他 markdown 文件（FORMS.md、REFERENCE.md）
- **代码**：可执行脚本（fill_form.py、validate.py）——脚本代码本身不进入上下文，只有输出消耗 tokens
- **资源**：数据库架构、API 文档、模板、示例

**三级加载对比表**：

| 级别            | 加载时间       | 令牌成本          | 内容                              |
| --------------- | -------------- | ----------------- | --------------------------------- |
| 第 1 级：元数据 | 始终（启动时） | ~100 tokens/Skill | YAML 中的 `name` 和 `description` |
| 第 2 级：指令   | 触发 Skill 时  | < 5k tokens       | SKILL.md 主体                     |
| 第 3 级+：资源  | 按需           | 无限制            | 捆绑文件，不加载到上下文          |

渐进式披露确保任何给定时间只有相关内容占据上下文窗口。

### 3. 可审计、可治理

**Skill 就像一份重复任务的操作手册**
新人照着做就行，错了能改，好了能复制，还能持续优化。

| 优势       | 实际效果                                  |
| ---------- | ----------------------------------------- |
| **可见**   | 打开文件就能看到完整规则，不再是黑盒      |
| **可讨论** | 团队成员可以 review、提出改进建议         |
| **可追溯** | Git 记录每次修改，谁改了什么一目了然      |
| **可复用** | 好的 Skill 可以分享给团队，避免重复造轮子 |
| **可下架** | 发现问题随时停用或回滚，风险可控          |

------

## 结构定义：一个 Skill 长什么样？

一个最基础的 Skill 目录结构如下：

```markdown
my-skill/
├── SKILL.md        # 必需：元数据 + 使用说明
├── scripts/        # 可选：可执行代码
├── references/     # 可选：参考文档
└── assets/         # 可选：模板、资源文件
```

其中，**`SKILL.md` 是核心**。

------

## SKILL.md 的定义方式

每个 Skill 都必须包含一个 `SKILL.md` 文件，它由两部分组成：

### 1. YAML Frontmatter（必需）

```markdown
---
name: pdf-processing
description: Extract text and tables from PDF files, fill forms, merge documents.
---
```

字段要求非常克制，但有明确的限制：

#### `name` 字段

- **最大长度**：64 个字符
- **允许字符**：小写字母、数字和连字符
- **禁止内容**：
    - 不能包含 XML 标签
    - 不能包含保留字：「anthropic」、「claude」
- **作用**：Skill 的唯一标识符

#### `description` 字段

- **最大长度**：1024 个字符
- **禁止内容**：不能包含 XML 标签
- **必须非空**：必须提供有效描述
- **最佳实践**：应包括两方面的信息
    1. **功能**：这个 Skill 能做什么
    2. **触发条件**：Claude 什么时候应该使用它

**示例**：

```markdown
---
name: pdf-processing
description: 从 PDF 文件中提取文本和表格、填充表单、合并文档。在处理 PDF 文件或用户提及 PDF、表单或文档提取时使用。
---
```

### 2. Markdown 指令正文（自由格式）

```markdown
# PDF Processing
## When to use this skill
Use this skill when the user needs to work with PDF files...
## How to extract text1. 
1. Use pdfplumber for text extraction...
2. ...
```

正文部分没有结构限制，你可以：

- 写操作步骤
- 写决策规则
- 写注意事项
- 写调用脚本的方式

这使 Skill 既是 **说明书**，也是 **执行指南**。

------

## 具体示例：PDF Processing Skill

以官方示例为例，这个 Skill 明确回答了三个问题：

1. **什么时候用？**
     → 用户需要处理 PDF（提取文本、填表、合并）
2. **怎么做？**
     → 明确工具（如 pdfplumber）、步骤、注意事项
3. **能扩展吗？**
     → 可以继续加入 scripts/ 来执行真实代码

对 Agent 来说，这不再是"猜 Prompt 意图"，而是**读取一份可执行说明文档**。

------

## 安全考虑

**⚠️ 重要：只从受信任的来源使用 Skills**

Skills 通过指令和代码为 Claude 提供新功能，虽然这使它们功能强大，但也意味着恶意 Skill 可能指导 Claude 以与 Skill 声称的目的不匹配的方式调用工具或执行代码。

### 关键安全原则

**1. 彻底审计**
在安装或使用任何 Skill 之前，检查所有捆绑文件：

- `SKILL.md` - 主要指令
- `scripts/` - 可执行代码
- 其他资源文件

**寻找异常模式**：

- 意外的网络调用
- 可疑的文件访问模式
- 与 Skill 声称目的不匹配的操作

**2. 外部来源有风险**
从外部 URL 获取数据的 Skills 特别有风险，因为：

- 获取的内容可能包含恶意指令
- 即使是可信的 Skills，如果其外部依赖项随时间变化也可能被破坏

**3. 工具滥用风险**
恶意 Skills 可以：

- 以有害方式调用工具（文件操作、bash 命令、代码执行）
- 窃取敏感数据
- 向外部系统泄露信息

**4. 像安装软件一样对待**

- 只从受信任的来源使用 Skills
- 在将 Skills 集成到具有敏感数据访问权限的生产系统时要特别小心
- 定期审查和更新已安装的 Skills

------

## 在 Claude Code 中的配置说明

在 **Claude Code** 中，Agent Skills 是一等公民，支持两种级别的 Skills 管理：

### Skills 目录位置

#### 1. 用户级 Skills（全局）

```markdown
~/.claude/skills/
```

- **作用域**：所有项目共享
- **适用场景**：通用能力、个人常用工具
- **示例**：代码审查规范、文档生成模板、Git 工作流

#### 2. 项目级 Skills（本地）

```markdown
<project-root>/.claude/skills/
```

- **作用域**：当前项目专属
- **适用场景**：项目特定逻辑、团队规范、业务知识
- **示例**：数据库架构、API 约定、业务规则

### 工作流程

典型工作方式是：

1. **创建 Skill**：在任一 skills 目录下创建 Skill 文件夹
2. **启动索引**：Agent 启动时自动扫描两个位置
3. **发现机制**：只索引 `name` 和 `description`
4. **按需加载**：任务匹配时自动读取完整 `SKILL.md`
5. **严格执行**：Agent 按照 Skill 中的指令执行任务

**优先级规则**：

- 项目级 Skills 覆盖用户级 Skills（同名时）
- 两者可以共存，互不干扰

### 工程视角

从工程角度看，Skill 是 **Tool + Policy + Playbook** 的组合模式：

| 组成部分     | 对应 Skill 元素               | 作用               |
| ------------ | ----------------------------- | ------------------ |
| **Tool**     | `scripts/` 目录               | 可执行的代码能力   |
| **Policy**   | `description` + "When to use" | 使用规则和触发条件 |
| **Playbook** | SKILL.md 正文指令             | 具体操作步骤和流程 |

这意味着 **Skill = Agent 的"可插拔能力模块"**，具备：

- **可组合性**：多个 Skill 协同工作
- **可替换性**：随时升级或替换单个 Skill
- **可测试性**：独立验证 Skill 的正确性

------

### 写一个非常简单的欢迎语回复技能

新建一个文件夹

新建.claude/skills/hi/SKILL.md

内容包括

```markdown
---
name: hi
description: 欢迎用户
---
回复欢迎语和今日时间 "hi ,新壮，`TZ='Asia/Shanghai' date '+%Y-%m-%d %H:%M:%S %Z'`"
```

在终端中使用斜杠命令触发



![Image](https://mmbiz.qpic.cn/mmbiz_png/XEy2K0iaHcg4Kkia4ibIA2oe8Z7oBDyaEux1HXENWrLic8Ws1vComOWicrsmFxYn655PZGmblRkA78ECx1uh4l30AQg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

回车之后

![Image](https://mmbiz.qpic.cn/mmbiz_png/XEy2K0iaHcg4Kkia4ibIA2oe8Z7oBDyaEuxcibNibibibjVdmfuWPr3ibWEGyunH6pBhAU6cOPaR5TIhty0qlslhiaIXaPg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

可以看到我们的自定义技能已经生效了。

你还可以使用 skill-creater 这个元技能来创建新的技能，通过 Anthropic 官方 skills 仓库可以安装。



Anthropic skill 链接: https://github.com/anthropics/skills

## 什么时候该用 Skill，而不是 Prompt？

| 场景                   | 推荐   | 原因                       |
| ---------------------- | ------ | -------------------------- |
| 一次性任务、快速实验   | Prompt | 无需长期维护，快速验证即可 |
| 需要团队协作、代码审查 | Skill  | 能力可视化，可审计、可讨论 |
| 跨项目复用、长期维护   | Skill  | 一次编写，多处复用         |
| 频繁迭代、需要版本控制 | Skill  | Git 管理，变更可追溯       |
| 简单指令、无需扩展     | Prompt | 够用即可，避免过度工程化   |

和 MCP，SubAgentd的对比



![Image](https://mmbiz.qpic.cn/mmbiz_png/XEy2K0iaHcg4Kkia4ibIA2oe8Z7oBDyaEuxATotE1nwf7laiaB6oUeCXUKyJED91y6sfcaMUIvqndHCKKSOIQ6O5UA/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

**核心原则**：

> **能写成 Skill 的，就不要留在 Prompt 里。**

------

## 总结：Skill 是 Agent 的"最小能力单元"

用一个更精确的三元关系来总结：

- **模型** 提供推理与理解能力
- **Agent** 定义任务编排与工具调用框架
- **Skill** 封装可复用的领域知识与执行规则

Agent Skills 用极简的文件规范，解决了 Agent 工程化中最棘手的几个问题：

- 能力管理
- 上下文控制
- 复用与治理

它不是“又一个框架”，而是一个**可以长期演进的能力协议**。

------

## 参考链接

- Agent Skills 官方说明
    https://agentskills.io/what-are-skills
- Agent Skills 规范
    https://agentskills.io/specification
- 示例 Skills（GitHub）
    https://github.com/anthropics/skills
- Claude Agent Skills 最佳实践
    https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices