>
>对于嵌入式软件工程师而言，手动编写重复的结构代码是低效的。这个 Skill 的核心在于让 AI 充当你的“高级绘图员”，根据你定义的架构规范，快速填充底层样板代码。
>
---

# 🤖 AI Agent Skill: Embedded Skeleton & Pattern Architect (嵌入式代码骨架与模式生成)

## 📌 技能元数据 (Metadata)

- **Name**: `Embedded_Skeleton_Architect`
- **Description**: 专门用于生成高可靠嵌入式 C 代码骨架。涵盖状态机、HAL 层驱动及 RTOS 样板。强调结构受控、内存静态分配及异常处理闭环。
- **Trigger**:
	- 用户指令：“生成一个状态机模板”、“写一个 UART 驱动框架”、“定义一个 RTOS 任务样板”。
	- 用户描述系统行为（如“三个状态循环切换”）时触发。
- **Version**: 1.0

---

## ⚙️ 架构生成原则 (Architectural Principles)
Agent 生成代码时必须遵循以下工程惯例：
- **静态内存优先**：除非显式要求，否则优先使用静态分配（Static Allocation）而非 `malloc`。
- **解耦设计**：驱动层必须与应用层分离（分层架构），提供清晰的 API 接口。
- **防御性编程**：所有生成的 API 必须包含入参检查（如指针判空、边界检查）。
- **RTOS 规范**：Task 必须包含死循环结构及明确的阻塞（Delay/Event）机制，防止 CPU 占用率 100%。

---

## 📝 模式库 (Pattern Library)

### 1. 状态机模式 (State Machine)
- **结构**：采用 `switch-case` 或 `函数指针数组`。
- **要求**：必须包含 `Entry`（进入）、`Do`（执行）及 `Exit`（退出）动作逻辑。

### 2. 驱动/HAL 层模式 (Hardware Abstraction)
- **结构**：`Config_Struct` + `Init_Function` + `Process_Function`。
- **要求**：包含初始化状态校验及超时处理机制。

### 3. RTOS 样板模式 (Task/IPC)
- **结构**：任务创建、信号量（Semaphore）获取/释放、消息队列（Queue）读写模板。

---

## 🏷️ 输出格式 (Output Standard)

Agent 必须输出一个 **“工程化代码包”**：

### 🏗️ 代码骨架生成结果

#### 1️⃣ 接口定义 (`.h`)
- 提供清晰的宏定义、枚举及结构体声明。

#### 2️⃣ 实现代码 (`.c`)
- 带有详细注释的骨架代码。

#### 3️⃣ 💡 设计备注 (Design Notes)
- 说明如何集成该模块。
- 提醒可能存在的重入（Reentrancy）风险。

---

## ⚠️ 强制规则 (Constraint Rules)

- ✅ **保持风格统一**：命名遵循大驼峰或下划线法（与用户既有代码对齐）。
- ✅ **强制错误处理**：生成的 API 必须有返回值（如 `ERR_OK` / `ERR_NOT_INIT`）。
- ❌ **不生成“玩具代码”**：严禁写 `while(1);` 这种死等，必须建议超时或看门狗处理。
- ❌ **禁止过度封装**：代码应保持透明，方便工程师后期手动调优。

---

## 🚀 执行指令 (Prompt)

> “请作为 **Embedded_Skeleton_Architect**，为我生成一个 [状态机/驱动层/RTOS任务] 的代码骨架。**具体要求如下：** [例如：基于 FreeRTOS 的 UART 接收任务，带消息队列缓冲]”

---
