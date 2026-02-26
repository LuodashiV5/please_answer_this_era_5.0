
# 🤖 AI Agent Skill: Weekly Report Transformer (周报自动化提炼专家)

## 📌 技能元数据 (Metadata)

- **Name**: `weekly_report_transformer`
- **Description**: 将 Git 提交记录与 JIRA 任务更新自动提炼为正式、简洁的工程师周报。支持多成员别名识别、任务状态自动映射及空项智能折叠。
- **Trigger**:
    - 用户指令：“生成本周周报”、“根据 Git 和 JIRA 写周报”。
    - 用户粘贴包含 Git Log 或 JIRA 导出文本的内容时触发。
        
- **Version**: 3.0 (支持智能折叠与多别名匹配)
- 用途:这份周报生成 Skill 的逻辑非常严谨，尤其是**别名归一化**和基于 **Git/JIRA 状态的逻辑映射**，极大地降低了人工筛选的成本
---

## ⚙️ 实体识别与归一化 (Entity Normalization)

在解析原材料时，凡命中以下任一别名，必须统一归并到对应人员名下：
- **罗炽学 (Chixue)**: Chixue / 罗工 /x.or@qq.com/ Law / chixue.luo@carrier.com / 炽学
- **刘清 (Qing)**: Qing / 刘清 / 刘青 / Liu, Qing / Qing.Liu4@carrier.com

---

## 🛠️ 数据提炼规则 (Extraction Logic)

严格基于原材料，禁止虚构时间或数量：

### 1. Git 记录映射

- **本周完成**: 提取 `Merge` / `PR` / `Master Branch Commit`。
- **描述要求**: 将新增功能、重构或 Bugfix 归纳为一行结果导向的描述；忽略仅包含格式化或注释的提交。

### 2. JIRA 任务映射

- **本周完成**: 状态为 `Done` / `Resolved` 的事项。
- **下周计划**: 状态为 `In Progress` / `To Do` 的事项。
- **风险&阻塞**: 明确提及 `Blocked`、依赖说明或无法推进的事项。

---

## 📝 输出格式与渲染规范 (Output Standard)

### 1. 写作规范

- ✅ **正式工程化**: 语气严谨，一事项一行要点，结果导向。
- ❌ **禁止冗余**: 不写过程细节、情绪或推测；不复述原始日志原文。

### 2. 智能折叠规则 (V3 增强)

若该部分不存在有效条目，必须使用以下 Markdown 结构进行折叠：

- **风险 & 阻塞项 (无时)**:
```Markdown
    ### 风险 & 阻塞项
    <details>
    <summary>无（点击展开）</summary>
    无
    </details>
```
    
- **需领导支持事项 (无时)**:
```Markdown
    ### 需领导支持事项
    <details>
    <summary>无（点击展开）</summary>
    无
    </details>
```
    
- _注：若环境不支持 `<details>`，则降级为单行 `- 无`。_

---

## 🚀 执行指令 (Prompt)

> “请严格按照 `weekly_report.skill.md` 逻辑，解析以下 Git 和 JIRA 数据，分别为 **罗炽学** 和 **刘清** 生成本周周报。**输入数据如下：** [在此粘贴数据]”

---
