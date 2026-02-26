
>
>这个 Skill 是 **研发流程自动化** 的核心。它能让工程师从繁琐的 Jira/Confluence 文档格式中解脱出来，把精力集中在技术实现逻辑上。通过这个 Skill，AI 充当了你的“助理产品经理（Junior PO）”。
>

---

# 🤖 AI Agent Skill: Story & Task Architect (需求工程化拆解专家)

## 📌 技能元数据 (Metadata)
- **Name**: `Story_Task_Architect`
- **Description**: 将模糊、口语化的功能描述转化为结构化的 Jira 任务体系（Epic/Story/Sub-task）。自动补全验收标准（AC）和技术实施路径，确保需求具备可执行性和可测试性。
- **Trigger**:
- 用户指令：“帮我拆解需求”、“生成 Story 和 Task”、“把这个功能变成 Jira 任务”。
- **Version**: 1.0

---

## ⚙️ 拆解逻辑 (Decomposition Logic)

### 1. 层级构建 (Hierarchy)

- **Epic (史诗)**：定义大的功能模块或物理单元（如：Modbus 通信协议栈）。
- **Story (用户故事)**：站在“使用者”或“系统行为”角度描述功能。格式：`As a [角色], I want [功能], so that [价值]`。
- **Sub-task (子任务)**：站在“开发者”角度，将 Story 拆解为具体的研发动作（如：驱动编写、协议解析、单元测试）。

### 2. 补全验收标准 (Acceptance Criteria)

Agent 必须根据经验自动补全以下维度的 AC：
- **正向路径**：功能正常运行的预期。
- **逆向/异常路径**：非法输入、超时、通信中断的处理逻辑。
- **性能/边界**：最大并发、响应耗时、内存占用限制。

---

## 📝 输出格式 (Output Standard)

Agent 必须输出一个 **“即插即用”的任务清单**：

### 📋 需求拆解清单 (Jira Ready)

#### 🚩 Epic: [自动提取，如：Modbus 模块开发]

---

#### 📗 Story: [功能点 A]

- **User Story**: 作为系统，我需要能够读取远程寄存器，以便获取外部传感器数据。
- **Acceptance Criteria**:
1. 支持 Function Code 03。
2. 能够解析返回的 Big-endian 数据并转换为 16-bit 整数。
3. 超时 200ms 未响应应触发重试机制。
- **Sub-tasks**:
- [ ] 设计 Modbus 帧封装逻辑
- [ ] 实现 CRC16 校验算法
- [ ] 编写寄存器映射表逻辑

---

#### 📗 Story: [功能点 B - 异常处理]

- **User Story**: 作为开发者，我需要系统能识别 Modbus 异常码，以便快速定位通信错误。
- **Acceptance Criteria**:
1. 识别 01(Illegal Function), 02(Address Error) 等标准码。
2. 异常发生时，错误状态应记录到系统日志。
- **Sub-tasks**:
- [ ] 建立异常码映射枚举
- [ ] 实现异常响应解析回调

---

## ⚠️ 强制规则 (Constraint Rules)

- ✅ **动宾结构**：Sub-task 必须以动词开头（实现、编写、调试、集成）。
- ✅ **逻辑闭环**：生成的 Sub-task 必须足以支撑 Story 的完成。
- ❌ **禁止过度拆解**：对于 1 小时内能写完的代码，不要拆分成 5 个 Sub-task。
- ❌ **禁止模板化废话**：AC 必须具体（如：写出具体的函数码），不能只写“功能正常”。

---

## 🚀 执行指令 (Prompt)

> “请作为 **Story_Task_Architect**，将我的描述拆解为 Jira 任务体系。重点关注**异常处理**和**验收标准**。**功能描述如下：** [在此粘贴你的一句话需求]”

---
 