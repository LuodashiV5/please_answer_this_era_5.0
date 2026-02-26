>
>通过强制执行标准化的文件结构与注释模板，可以确保团队协作时代码风格的高度统一，并为自动化文档生成（如 Doxygen）打下基础。
>这份文档你可以直接保存为 `Embedded_Source_Guard.skill.md`，它包含了完整的**元数据**、**执行逻辑**、以及你要求的**像素级复刻模板**。
>
---

# 🤖 AI Skill: Embedded Source Guard (嵌入式源码规范专家)

## 📌 技能元数据 (Metadata)

- **Name**: `Embedded_Source_Guard`
- **Description**: 严格执行嵌入式 C/H 文件的结构化规范。确保文件头、函数注释、声明区标识及文件尾严格遵循预设的星号（`*`）长度（148字符）和格式。
- **Trigger**:
	- 用户指令：“新建文件”、“重构文件结构”、“加函数注释”。
	- 生成任何 C/H 代码时作为基础约束自动加载。
- **Version**: 1.0

---

## ⚙️ 核心逻辑与约束 (Core Constraints)

1. **像素级复刻**：严禁修改注释框的长度和星号（`*`）数量。所有长横线总宽度固定为 **148 个字符**。
2. **左对齐原则**：注释大框必须从第 0 列开始（Column 0），严禁随代码缩进而缩短。
3. **文件属性约束**：
	- **`.c` 文件**：严禁使用 `#pragma once`。
	- **`.h` 文件**：必须在 `File Header` 结束后立即添加 `#pragma once`。
4. **自动填充项**：生成时应自动识别当前文件名（File Name）及当前系统日期（Date）。

---

## 📑 标准模板库 (Templates)

### 1. 文件头 (File Header)

### 2. 函数实现注释 (Function Implementation)

### 3. H 文件专用声明区 (H-File Declarations)

- **原型/结构体声明前**：
- **公共 API 函数声明前**：

### 4. 文件尾 (End of File)

---

## 🛠️ 执行示例 (Example)

**输入**：
> "帮我为 `bms_driver.c` 写一个初始化函数 `BMS_Init`"

**输出要求**：
1. 首先输出 **1. 文件头**（File Name 填入 bms_driver.c）。
2. 中间不含 `#pragma once`。
3. 在 `BMS_Init` 实现前输出 **2. 函数实现注释**。
4. 代码末尾输出 **4. 文件尾**。

---

## ⚠️ 负向约束 (Negative Constraints)

- ❌ 禁止缩减星号数量。
- ❌ 禁止合并 `File Info` 中的空行。
- ❌ 禁止在 `.c` 文件中漏掉 `end of file`。

---

这就是 **`Embedded_Source_Guard`** 的完整独立版本。它像一个“模具”，确保 AI 吐出的每一行注释都符合你工程中严苛的视觉要求。