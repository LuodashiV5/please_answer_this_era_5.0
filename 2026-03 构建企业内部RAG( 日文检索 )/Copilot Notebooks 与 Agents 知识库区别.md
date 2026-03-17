
# 《协议分析专用 Copilot Notebook 使用说明》

适用对象：嵌入式软件工程师 / FAE / 技术支持工程师

适用场景：通信协议、命令帧（Command / FrameFormat）分析与索引整理

目标：让不会或不熟悉 AI 的工程人员，也能稳定、高质量地使用 Copilot Notebook 完成协议分析工作

---

## 一、这是什么？（先读这一节）

Copilot Notebook 是一个项目级、可持续的 AI 协作工作区。

在本项目中，它被用于：

  

> 辅助工程师从大量协议文档中，精确定位：
> 
> - 功能 → Command
> - Command → Frame
> - Frame → 数据字段位置

⚠️ 重要说明：

- Notebook 不是自动知识库
- Notebook 不会自己扫描所有文档
- Notebook 的分析结果只基于你手动加入的文件

它的角色是：

> ✅ 一个“协议分析实验室”
> 
> ✅ 一个“功能 ↔ Command 映射的临时 RAG 空间”

---

## 二、你应该在什么时候用 Notebook？

✅ 适合使用 Notebook 的场景：

- 不知道某个功能用哪个 Command
- 文档多、分散、语言复杂（日文 / 英文）
- 需要人工确认 + AI 辅助推理的协议分析
- 希望沉淀一份“可复用的索引摘要”

❌ 不适合的场景：

- 想直接做全自动 FAQ
- 希望 AI 自己扫描整个 SharePoint

---

## 三、Notebook 的标准使用流程（按顺序来）

### Step 1：创建 Notebook

命名建议：

```PlainText
协议域_用途
例如：
VRF_Protocol_Analysis
RAC_Command_Index
```

---

### Step 2：加入参考文档（非常关键）

✅ 建议加入的内容：

- FrameFormat / Protocol PDF
- Command 说明文档
- 你自己整理的对照表（Word / Markdown）

❗ 原则：

- 宁少勿杂
- 一个 Notebook = 一个协议域 / DeviceGroup

---

### Step 3：粘贴【Notebook 角色定义】

在 Notebook 顶部，粘贴以下内容（不要修改）：

```PlainText
你是一个【嵌入式通信协议分析助理】。

你的唯一任务是：
- 基于当前 Notebook 中提供的协议 / FrameFormat / Command 文档
- 帮助工程师完成【功能 → Command → 数据位置】的精确映射
- 输出结果必须可用于：技术文档、FAE 支持、协议索引表

⚠️ 约束：
- 不允许引入 Notebook 以外的协议知识
- 不允许猜测、补全未明确写在文档中的字段
- 不确定的地方必须明确标注【文档中未明确说明】
```

---

### Step 4：粘贴【分析基本规则】

```PlainText
【分析基本规则】

1. 所有结论必须能追溯到具体文档
   - 明确指出：文档名 / Command 名 / Frame 名 / Byte 或 Word 位置

2. 默认分析顺序：
   功能需求
   → 所属 DeviceGroup（如 System / RAC / VRF / LC）
   → 对应 Command（如 cmd4C）
   → Frame 格式
   → 数据字段位置（Byte / Word / Bit）

3. 如果同一功能在多个 Command 中存在：
   - 明确区分使用场景
   - 不允许合并成“一个答案”

4. 如果文档语言为日文：
   - 允许翻译
   - 但字段名 / Command 名保持原文不变
```

---

## 四、日常最常用的 4 种提问方式（照抄即可）

### 模板 1：按“功能”查 Command（最常用）

```PlainText
请基于当前 Notebook 中的所有协议文档，分析以下功能：

【功能描述】
- 功能名称：设定温度
- 业务语义：设置目标温度（Set Temperature）
- 使用场景：远程控制 / 本地控制（如文档中有）

请输出：
1. 所属 DeviceGroup
2. 涉及的 Command 列表
3. 每个 Command 的用途差异
4. 对应的数据字段位置（Frame / Byte / Word / Bit）
5. 明确引用的文档来源

⚠️ 如果功能无法唯一定位到 Command，请说明原因。
```

---

### 模板 2：已知 Command，拆结构

```PlainText
请详细解析 Command：cmd4C

分析内容包括：
1. Command 的业务用途
2. 所属 DeviceGroup
3. Frame 结构概览
4. 每个数据字段的含义与位置
5. 哪些字段与【控制 / 设定】类功能相关

要求：
- 使用表格输出
- 不确定字段必须标注“文档未明确”
```

---

### 模板 3：站在 FAE / 使用者角度反推

```PlainText
假设我是一个不了解协议细节的使用者，只知道想实现以下功能：

【目标功能】
- 功能：设定温度
- 期望行为：写入目标值后，设备温度目标发生变化

请基于 Notebook 文档：
1. 推导应该使用的 Command
2. 给出选择理由
3. 说明如果选错 Command 可能出现的问题
```

---

### 模板 4：生成索引摘要（非常重要）

```PlainText
请基于当前 Notebook 中的所有分析结果，
生成一份【功能 ↔ Command ↔ 数据位置】索引摘要。

输出格式要求：
- 使用 Markdown
- 每一条功能独立成段
- 包含以下字段：
  - 功能名称
  - DeviceGroup
  - Command
  - 数据字段位置
  - 简要说明
  - 参考文档

⚠️ 该摘要将用于后续构建知识库，请确保准确、克制、可审计。
```

---

## 五、强烈建议的工作习惯（经验总结）

✅ 一个 Notebook：

- 只解决一个协议域 / DeviceGroup

✅ 一个问题：

- 只问一个功能或一个 Command

❌ 不要一次问：

- “帮我分析所有功能”

📌 原因：

  

> 慢一点，结构才不会塌

---

## 六、Notebook 产出应该怎么用？

Notebook 的最终产出不是聊天记录，而是：

✅ Markdown 索引文档

✅ Word / 表格形式的功能对照表

✅ 后续 Agent / RAG 的输入材料

你可以：

- 直接复制 Notebook 结论到正式文档
- 或作为“人工校验层”，再喂给自动化知识库

---

## 七、一句话总结（给第一次用的人）

  

> Notebook = 带着 AI 做协议分析
> 
> 它不是替你思考，而是防止你漏看、看错、记混

---

（完）