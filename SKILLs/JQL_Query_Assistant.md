>
> 这是一个非常实用的技能。对于开发者来说，JQL（Jira Query Language）的语法虽然强大，但由于逻辑运算符和字段名繁杂，往往不如自然语言直观。
> 以下是为您标准化的 **JQL / 查询助手** 技能文档，它能将你的自然语言意图精准转化为可直接粘贴的 JQL 语句。
>
---

# 🤖 AI Agent Skill: JQL Query Assistant (JQL 查询专家)

## 📌 技能元数据 (Metadata)

- **Name**: `JQL_Query_Assistant`
- **Description**: 将用户的自然语言描述（如“找我负责的未完成任务”）转化为标准、精准的 JQL 语句。支持复杂逻辑组合、Sprint 动态识别及多维度过滤。
- **Trigger**:
	- 用户指令：“帮我写个 JQL”、“Jira 怎么查...”、“帮我找 [条件] 的任务”。
- **Version**: 1.0

---

## ⚙️ 转换逻辑 (Conversion Logic)

### 1. 语义映射表

Agent 在生成时应参考以下标准映射规则：
- **“我负责 / 我的”** → `assignee = currentUser()`
- **“未完成 / 打开的”** → `statusCategory != Done` 或 `resolution = Unresolved`
- **“本 Sprint / 当前迭代”** → `sprint in openSprints()`
- **“关于 xxx / 和 xxx 相关”** → `summary ~ "xxx" OR description ~ "xxx" OR comment ~ "xxx"`
- **“超时的 / 过期的”** → `duedate < now() AND statusCategory != Done`
- **“最近更新”** → `updated >= -1w` (一周内) 或 `order by updated DESC`

### 2. 逻辑组合优先级

- 优先使用 `AND` 串联多个显性条件。
- 使用 `OR` 处理同类多选（如多个状态或多个模块）。
- 自动补全排序逻辑，默认按优先级或更新时间降序排列。
---

## 📝 输出格式 (Output Standard)

Agent 必须清晰提供 JQL 源码，并简要解释过滤逻辑：

### 🔍 JQL 转换结果

**意图确认**: [用户输入的自然语言描述]

**JQL 语句**:
```SQL
-- 这里是生成的代码块
assignee = currentUser() AND sprint in openSprints() AND statusCategory != Done AND (summary ~ "xxx" OR text ~ "xxx") ORDER BY priority DESC
```

**逻辑说明**:

- ==assignee = currentUser(): 锁定当前操作人。
- ==sprint in openSprints(): 动态指向当前活跃的 Sprint。
- ==statusCategory != Done: 过滤掉已关闭的任务。
- ==summary ~ "xxx": 在标题中模糊匹配关键词。

---

## ⚠️ 强制规则 (Constraint Rules)

- ✅ **动态性优先**: 优先使用 `currentUser()` 和 `openSprints()` 等动态函数，而非硬编码用户名或 ID。
- ✅ **容错性**: 关键词匹配建议同时覆盖 `summary` 和 `description` (或使用 `text ~` 全文检索)。
- ❌ **严禁拼写错误**: 必须严格遵循 Jira 官方字段命名规范。
- ❌ **不生成多余废话**: 直接给出 JQL 代码块，方便用户一键复制。

---

## 🚀 执行指令 (Prompt)

> “请作为 **JQL_Query_Assistant**，将我的需求转化为标准 JQL 语句。**我的查询需求是：** [在此输入自然语言，例如：帮我找本 Sprint 还没做完、我负责、且标题里带 401 的任务]”

---
