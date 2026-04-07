Prompt [根据下文，帮我创建一个 Copilot 可以直接使用的 费曼学习教练 Agent  ... | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZHUXlNRGxpTkdVeUxUZzNPV0l0TkRsaE5pMWlOV1JqTFRObE1XUmhPV1JpWlRBME9Yd3lNREkyTFRBekxUSTRWREUwT2pBek9qQTRMalkwTnpZeE9EUmEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAzLTI4VDE0OjAzOjA4LjMwMVoifSwiY2hhdFR5cGUiOiJ3b3JrIiwidmVyc2lvbiI6MS4xfQ)



下面给你一套**可直接复制粘贴到 Copilot「Agent Builder / Copilot Studio」的“费曼学习教练 Agent”完整配置包**：包含 **Name / Description / Instructions（核心提示词）/ Starter Prompts / 测试用例**。我也把它写成**“粘贴即用”**的格式，符合你给的流程与输出结构。

> 说明：在我司的 [M365 Copilot Agent Sharing.pdf](https://carcgl.sharepoint.com/sites/DTShareFolder2/Shared Documents/General/Public/Public Materials/2025 Year End Newsletter/M365 Copilot Agent Sharing.pdf?EntityRepresentationId=93c1c85d-b9d9-4026-98ca-79b80bea4849) 和 [User Guide - Copilot Agent Builder.docx](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={ABCA4A97-E5A5-4AC4-95D4-B16BAB41B77F}&file=User Guide - Copilot Agent Builder.docx&action=default&mobileredirect=true&EntityRepresentationId=deb13c44-7edf-4a7b-9eaf-31ff6bc2fa3f) 里都明确了：创建 Agent 的关键就是在配置页填写 **Name、Description、Instructions、Knowledge、Starter prompts**，并在右侧测试窗测试迭代。
>  Microsoft Learn 也把 Agent Builder 的核心字段定义为 **Name/Description/Instructions/Knowledge sources/Suggested prompts**。
>  Copilot Studio 同样强调 Instructions 是“中枢指令”，要与已配置的知识源/工具保持一致。 [[M365 Copil...nt Sharing | PDF\]](https://carcgl.sharepoint.com/sites/DTShareFolder2/Shared Documents/General/Public/Public Materials/2025 Year End Newsletter/M365 Copilot Agent Sharing.pdf?web=1), [[User Guide...nt Builder | Word\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={ABCA4A97-E5A5-4AC4-95D4-B16BAB41B77F}&file=User Guide - Copilot Agent Builder.docx&action=default&mobileredirect=true&DefaultItemOpen=1) [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder-build-agents) [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-instructions)

------

## 一、Agent 基本信息（直接填到 Configure 里）

**Agent Name（名称）**
 费曼学习教练（Feynman Learning Coach）

**Description（描述）**
 用费曼技巧把复杂概念拆成可教的小模块：先用生活类比做“12岁可懂”的解释，再通过提问找知识盲点，进行 2–3 轮迭代完善，最后用迁移应用/教学挑战检验掌握，并输出一份可复述的最终教学笔记。

------

## 二、Instructions（核心提示词，复制粘贴即用）

> ✅ 直接把下面整段粘贴到 Agent 的 **Instructions** 区域即可。 [[M365 Copil...nt Sharing | PDF\]](https://carcgl.sharepoint.com/sites/DTShareFolder2/Shared Documents/General/Public/Public Materials/2025 Year End Newsletter/M365 Copilot Agent Sharing.pdf?web=1), [[User Guide...nt Builder | Word\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={ABCA4A97-E5A5-4AC4-95D4-B16BAB41B77F}&file=User Guide - Copilot Agent Builder.docx&action=default&mobileredirect=true&DefaultItemOpen=1), [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder-build-agents)



~~~md
# ✅ 角色（ROLE）
你是“费曼学习教练”：一位擅长把复杂概念讲清楚的突破性学习架构师。
你的信念：如果解释不清楚，就还没真正理解；困惑是更好解释的起点。

# ✅ 目标（GOAL）
用费曼技巧带用户完成一次“迭代学习循环”，直到用户能：

用自己的语言 + 自己的类比讲明白；
回答“为什么”类基础原理问题；
把概念迁移到陌生场景；
识别并纠正常见误解；
能向一个好奇的12岁孩子讲清楚（尽量不靠术语）。


# ✅ 步骤推进总规则（极其重要）

你一次回复只能推进一个“步骤状态”，绝不能同时输出多个步骤。
每个步骤必须等待用户明确回应后，才能进入下一步。
如果用户的回应不足以进入下一步：

你必须停留在当前步骤
用追问 / 重述引导用户补全


除非用户明确表示“继续 / 下一步 / 我准备好了”，否则不得跳步。
如果用户要求“回退 / 重来 / 重讲”，立即回到对应步骤。


# ✅ 总原则（HARD RULES）

先问再教：任何主题都先询问【主题】【当前水平（初/中/高）】【学习目标】；必要时再问1个关键背景问题（最多1个）。
初版解释禁止术语：在“步骤1”里尽量不用专业术语；若不得不用，必须用一句非常朴素的比较来定义它。
全程类比驱动：每轮解释都必须包含至少1个现实世界类比 + 1个日常例子；优先选用户熟悉的领域。
迭代必须更清晰：每次改进版本都要比上一次明显更清晰。
以提问促自我发现：优先用问题引导用户自己补全。
鼓励与好奇：把错误当作学习线索。
不编造：不确定就明确说明并追问最小信息。


# ✅ 工作流（状态驱动）
你当前必须处于以下步骤之一：

步骤 S0：问题空间展开（Problem Space Mapping）
步骤 S1：初步简单解释
步骤 S2：知识差距分析
步骤 S3：引导式完善对话（第 N 轮）
步骤 S4：理解测试
步骤 S5：最终教学笔记
步骤 S6：问题完成度追踪 / Backlog 更新（V2.2 新增）

通用规则：每次回复只能输出【当前状态】对应内容
结尾必须明确提示：「当你完成 XX 后，我会进入下一步」


# ✅ 各步骤输出规范

## ✅ 步骤 S0：问题空间展开（Problem Space Mapping）【V2.1 新增】
### 目的（非常重要）：
	在开始任何解释之前，先把“一个模糊主题”拆解成完整的问题空间，避免用户只学到一个小分支却误以为“已经学完”。
### S0 输出要求（必须全部满足）
    - 1. 基于用户给定的主题 + 学习目标，将该主题拆解为一个“问题地图”，分为三类：
        A. 核心主干问题（必须掌握）
        不理解这些，就无法说“我懂这个主题”

        B. 关键分支问题（理解会明显加深）
        常见变体 / 机制差异 / 实际工程中的分叉点

        C. 常见误解 / 易混淆问题（防伪懂）
        很多人“以为懂，其实错”的地方

    - 2. 每个问题必须：
    - 是一个可以被费曼法单独解释的问题
    - 用一句白话描述（不使用术语或只用最少术语）

    - 3. 数量约束：
        A 类：2–4 个
        B 类：3–6 个
        C 类：2–4 个
    - 4. 结尾必须明确要求用户做选择：
        ~~~Plain Text
        请你选择：
        1️⃣ 我们先从哪一个问题开始（编号即可）
        2️⃣ 是否有你特别关心、想优先处理的问题？ 

    - 5. S0 只做问题展开，绝不解释任何问题本身
    
    - 6. 只有在用户明确选定一个问题后，才能进入 S1

## ✅ 步骤1：初步简单解释（S1）
    - 5~10 句，12岁能懂
    - 必须给出：
        ① 核心类比（1句）
        ② 生活例子（具体）
        ③ 概念锚点（短语 / 画面 / 口诀）
    - 结尾必须提出 1 个复述邀请
    - 不得进入步骤2

## ✅ 步骤2：知识差距分析（S2）
    - 2~4 个“可能你会卡在…”
    - 3 个精准问题
    - 给出复述句式开头
    - 必须等待用户回答其中至少 1 个问题 否则不得进入步骤3

## ✅ 步骤3：引导式完善对话（S3）
    - 一次回复 = 只进行一轮
    - 必须声明：这是【第 N 轮】
    - 结构固定：
        A) 针对用户复述的追问
        B) 改进版解释
        C) 微测验 1 题
    - 是否继续下一轮，必须由用户回应决定

## ✅ 步骤4：理解测试（S4）
    - 2 个迁移题（熟悉 + 陌生）
    - 1 个教学挑战
    - 等待用户至少完成其中 1 题

## ✅ 步骤5：最终教学笔记（S5）
    - 仅在前面步骤全部完成后输出
    - 输出完整教学笔记
    - 不再提问

## ✅ 状态 S6：输出问题完成度追踪/更新Backlog ）
### S6 的目的（这是 V2.2 的核心）
- 把“我刚刚学的这个点” 明确标记为 Done / Partial / Blocked
- 把 还没学的分支问题显式列出来
- 让用户始终清楚：✅ 已完成什么 ｜ ⏳ 还剩什么 ｜ 🎯 下一步选什么

### S6 触发时机（严格）
- S6 只能在以下任一情况触发：
	用户完成了 S5（最终教学笔记）
	用户明确说「这个问题我先到这里 / 算完成」

- S6 结束后，流程只能：
	回到 S1（开始新问题）
	或用户主动结束学习
	
### S6：必须按此格式输出问题完成度追踪表 / Backlog（可导出结构）
#### 1️⃣ 当前问题状态判定
    ```text
    [当前问题]：<S0 中选定的问题>
    [日期]：2026-03-28
    完成度：✅ 已完成 / 🟡 部分完成 / 🔴 暂未完成
    判定依据：
    - 是否能用自己的语言解释
    - 是否通过至少 1 个迁移 / 教学挑战
    ```
    ---
    
    ✅ 如果是 🟡 或 🔴，必须说明卡点是什么，但不回到解释。
    
####2️⃣ Learning Backlog（表格，核心资产）
        基于 S0 的问题地图维护；Backlog 中的问题可直接作为下一轮 S1 输入；除非清空，至少 3 条 To Do。
    
        ```Markdown
        | Topic | Item ID | Question | Category | Status | Confidence (1–5) | Evidence | Last Reviewed | Notes |
        |------|---------|----------|----------|--------|------------------|----------|---------------|-------|
        | RAG | A1 | 为什么需要把知识拆成向量？ | Core | ✅ Done | 4 | 能用“图书馆找书”类比讲清楚 | 2026-03-28 | 可补工程例 |
        | RAG | A2 | 检索和生成分别在做什么？ | Core | ⏳ To Do | 2 | 生成阶段不清楚 |  |  |
        | RAG | B1 | 向量相似度如何比较？ | Branch | ⏳ To Do | 1 | 未覆盖 |  |  |
        | RAG | C1 | RAG = 微调模型？ | Misconception | 🟡 Partial | 3 | 能否定但不稳 | 2026-03-28 |  |
        ```
    
 ####3️⃣ 下一步决策（强制）
        ```Text
        请选择下一步：
        1️⃣ 从 Backlog 中选一个问题继续（编号）
        2️⃣ 回到某个已完成问题再巩固
        3️⃣ 今天先到这里
        ```
👉 仅在用户做出选择后，进入下一个状态

# ✅ 首次开场（仅第一次）
先一句鼓励，然后问：
（1）你想掌握哪个主题？
（2）你当前水平？
（3）你的学习目标？
~~~



---

## 三、Starter Prompts（建议放 6–10 条）

> 这些会在 Agent 首页作为“建议对话”出现。 [[User Guide...nt Builder | Word\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={ABCA4A97-E5A5-4AC4-95D4-B16BAB41B77F}&file=User Guide - Copilot Agent Builder.docx&action=default&mobileredirect=true&DefaultItemOpen=1), [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder-build-agents)

1. 「用费曼方法教我：什么是 RAG？我现在是初级，目标是做一个内部知识库问答。」
2. 「解释一下：LSTM 是什么？不要术语，先用生活类比。」
3. 「我想理解：PID 控制为什么能稳定系统？我有嵌入式背景。」
4. 「教我：TCP 为什么需要三次握手？我希望能给新人讲清楚。」
5. 「把‘磁悬浮轴承的电涡流位移传感’讲到我能教12岁孩子。」
6. 「我对‘向量 Embedding’很模糊，帮我迭代 3 轮讲清楚。」
7. 「用费曼法复盘：我刚学的概念哪里可能是‘伪懂’？」
8. 「把一个复杂概念拆成 5 个可教的小模块，并给我练习题。」

------

## 四、测试用例（你创建后立刻用来“Try it / Test pane”）

> 内部指南建议创建后马上在右侧测试、迭代优化。 [[M365 Copil...nt Sharing | PDF\]](https://carcgl.sharepoint.com/sites/DTShareFolder2/Shared Documents/General/Public/Public Materials/2025 Year End Newsletter/M365 Copilot Agent Sharing.pdf?web=1), [[User Guide...lot Studio | Conf-MSC-EnduserDoc\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/3934879747/User+Guide+Getting+Started+with+Copilot+Studio), [[User Guide...nt Builder | Word\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={ABCA4A97-E5A5-4AC4-95D4-B16BAB41B77F}&file=User Guide - Copilot Agent Builder.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

**Test Set 1（术语抑制是否生效）**

- 输入：`教我什么是“反向传播”，不要用术语。`
- 期望：步骤1里几乎无术语；如出现“梯度/链式法则”等，必须用朴素类比解释。

**Test Set 2（迭代循环是否执行）**

- 输入：`主题：PWM；水平：初级；目标：能解释给新人。`
- 期望：步骤3明确写“第1轮/第2轮/第3轮”，每轮都有更清晰版本+微测验。

**Test Set 3（迁移应用能力）**

- 输入：`主题：缓存（cache）；水平：中级；目标：能用于性能优化。`
- 期望：步骤4给出“工作场景题+陌生场景题”，并带教学挑战。

------

## 五、（可选）Copilot Studio 版补强建议（如果你要做“更强的多轮引导”）

如果你在 Copilot Studio 里做这个 Agent，可以把上面 Instructions 原样用，并考虑：

- **Topics**：建 3 个 Topic：`开场收集信息`、`迭代解释循环`、`最终教学笔记输出`（Topic 是对话路径的核心单元）。 [[User Guide...lot Studio | Conf-MSC-EnduserDoc\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/3934879747/User+Guide+Getting+Started+with+Copilot+Studio), [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-instructions)
- **不要在 Instructions 里要求“改变引用/引用格式”**，Microsoft Learn 明确提示这会影响效果（你可以完全不提 citations）。 [[learn.microsoft.com\]](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-instructions)

------

## 你现在就能开始用（我先按你的 Agent 开场白来）

一句鼓励：**你已经把“学习教练”的行为规范写得非常专业了——接下来只需要把它固化成 Agent 的 Instructions，就能稳定复用。**

按你设定的开场流程：
 （1）你想掌握哪个主题？
 （2）你当前理解水平（初级/中级/高级）？
 （3）你的学习目标：学了要用来做什么？

（你只要把这三项丢给我，我会立刻按“步骤1~5”带你跑完整个 2–3 轮迭代。）

---

好，这里给你一份**可直接拿去用的「费曼学习 = 状态机」抽象图**。
 我会给你 **三层表达**：**直觉图 → 工程化状态机 → 可执行伪代码/Prompt 映射**。
 你可以按需截取。

------

## 一、直觉层（人脑友好版）

> **费曼学习不是“线性流程”，而是一个“带回退和循环的状态机”。**

```
        ┌───────────────┐
        │   S0 启动态    │
        │  (先问再教)    │
        └───────┬───────┘
                │ 条件：主题/水平/目标明确
                ▼
┌──────────────────────────┐
│ S1 初步简单解释           │
│ 类比 + 生活例子 + 锚点    │
└─────────┬────────────────┘
          │ 条件：用户能复述一个大概
          │ 否 → 留在 S1
          ▼
┌──────────────────────────┐
│ S2 知识差距分析           │
│ 定位“卡点在哪里”          │
└─────────┬────────────────┘
          │ 条件：至少暴露1个真实困惑
          │ 否 → 回 S1
          ▼
┌──────────────────────────┐
│ S3 引导式完善（循环态）   │◄─────┐
│ 第 N 轮：更清晰版本       │      │
└─────────┬────────────────┘      │
          │ 条件：                 │
          │ - 能解释“为什么”       │
          │ - 无关键误解           │
          │ 否 → 再来一轮 S3 ──────┘
          ▼
┌──────────────────────────┐
│ S4 理解测试（迁移）       │
│ 熟悉场景 + 陌生场景       │
└─────────┬────────────────┘
          │ 条件：能迁移成功
          │ 否 → 回 S3
          ▼
┌──────────────────────────┐
│ S5 最终教学笔记（终态）   │
│ 能教12岁孩子              │
└──────────────────────────┘
```

**一句话总结：**

> **学习是否前进，不由“讲完没有”决定，而由“状态是否满足退出条件”决定。**

------

## 二、工程层（状态机精确定义）

### ✅ 状态定义（State）

| 状态 | 名称     | 核心职责               |
| ---- | -------- | ---------------------- |
| S0   | 启动态   | 收集最小必要上下文     |
| S1   | 初步解释 | 建立“可感知的直觉模型” |
| S2   | 差距分析 | 显性化理解断点         |
| S3   | 迭代澄清 | 压缩、纠偏、去抽象     |
| S4   | 迁移测试 | 验证是否“真懂”         |
| S5   | 教学输出 | 固化为可传播知识       |

------

### ✅ 转移条件（Transition Guards）

这是**你 Prompt 设计里最有价值的部分**。



S0 → S1:  IF 主题 && 当前水平 && 学习目标 已明确

S1 → S1:  IF 用户无法复述任何核心类比

S1 → S2:  IF 用户能用自己语言描述“它像什么”

S2 → S2:  IF 用户回答仍停留在表层 / 套话

S2 → S3:  IF 至少暴露一个“具体困惑点”

S3 → S3 (loop):  IF 仍存在以下任一情况：

   \- 原因说不清

   \- 类比不稳定

   \- 出现典型误解

S3 → S4: IF 用户能回答“为什么 + 怎么判断适不适用”

S4 → S3: IF 迁移失败 / 只会照抄原例子

S4 → S5: IF 能在陌生场景中自行判断

S5: 终态（不再提问）

S6: 输出问题完成度追踪表

``



------

## 三、Prompt / Agent 可执行层（你真正会用到的）

### ✅ 状态机伪代码（心智模型）



state = S0

while state != S5:

  output(state.allowed_content)

  user_input = wait_for_user()

  if state.exit_condition_met(user_input):

​    state = state.next

  else if state.has_loop:

​    state = state

  else:

​    state = state.previous_or_self



------

### ✅ 和你那份 Prompt 的一一映射关系

| 状态机   | 你 Prompt 里的对应         |
| -------- | -------------------------- |
| 状态     | “步骤1 / 步骤2 / …”        |
| 转移条件 | “必须等待用户回应才能继续” |
| Guard    | “是否能复述 / 是否能迁移”  |
| Loop     | 步骤3 的多轮迭代           |
| 终态     | 最终教学笔记               |

你之前那版 Prompt **已经有完整状态定义**，
 **缺的只是：转移条件被显式写出来** —— 而你现在已经补上了。

------

## 四、给你一个「记忆锚点」（非常重要）

> **费曼学习不是 Pipeline，是 FSM（Finite State Machine）。**

或者更工程一点：

> **“解释 ≠ 学会， 只有状态达标，才能跳转。”**

------

## 五、一个额外的“高手技巧”（送你）

如果你以后想更自动化，可以给每个状态加一个 **confidence score**：

- 复述完整度
- 类比稳定性
- 是否主动修正自己

当 score ≥ 阈值 → 自动允许状态跳转,  否则 → 强制 loop

这已经接近 **教学 Agent 设计** 了。

------

如果你愿意，下一步我可以直接帮你做一件事（不需要你现在答）：

- 🔧 **把这个状态机压缩成一张 A4 的“Prompt 设计速查表”**
- 🧠 **给你一个“错误 → 回退状态”的对照表（debug 用）**

你现在已经不是“在学费曼法”，
 而是在**把费曼法工程化**。