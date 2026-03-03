# 程序员提升生产力的AI技能应用



> 
>
> # Prompt [就你对我司情况的了解，作为程序员，我想问下，日常工作中，用哪些ai... | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZHTTVOVFE0TVRZMkxUYzNOelF0TkRrNU1DMWlaRGRoTFRBME1HSXdZamN6TmpWallYd3lNREkyTFRBeUxUSTJWREF6T2pBNE9qSTBMamM1TnprMk1UWmEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAyLTI2VDAzOjA4OjI0Ljk5OVoifSwiY2hhdFR5cGUiOiJ3b3JrIiwidmVyc2lvbiI6MS4xfQ)
>
> 

就你对我司情况的了解，作为程序员，我想问下，日常工作中，用哪些ai skill可以帮我加强生产力，节省时间，节省脑力? confluence, github, jira, 周报，实验室测试，代码，工作汇报，帮我穷举

Response

这个问题问得**非常对点，而且非常“工程师视角”** 👍
 我结合**你本人角色（嵌入式/软件、偏底层 + 项目驱动）**，以及**我司目前已经在用/允许用的 AI 能力（以 M365 Copilot、GitHub Copilot 为主）**，给你做一个**“穷举式 + 可落地”的 AI Skill 地图**。

> 先说结论一句话：
>  **AI 的最大价值不是“写代码”，而是帮你把「机械脑力」和「上下文切换」干掉。**

------

## 一、总体分类：程序员日常的 7 大耗脑场景

我先把你每天在消耗脑力的地方拆开，再逐一“穷举 AI 能力”。

1. **需求 & 知识获取**（Confluence / Jira / 文档）
2. **设计 & 思考**（方案、架构、取舍）
3. **编码本身**（代码、调试、重构）
4. **实验室 / 测试 / 日志**（你非常典型）
5. **协作 & 流程**（PR / Review / Jira / Scrum）
6. **输出型工作**（周报 / 汇报 / PPT）
7. **长期积累**（知识库、复盘、复用）

下面我一类一类帮你“掏空”。

------

## 二、Confluence / 知识库：把「读文档」变成「问问题」

### 1️⃣ AI Skill：**Confluence 内容速读 & 精炼**

**你原来的痛点**

- Confluence 一篇篇点
- 不知道哪个是“有效信息”
- 新项目 / 新模块上手慢

**AI 能干什么**

- 一句话让 AI：
  - 「总结这篇页面的 *背景 / 决策 / 结论*」
  - 「这篇文档里**跟嵌入式/软件相关的结论是什么**」
- 把 10 页内容压成：
  - ✅ *What*
  - ✅ *Why*
  - ✅ *Decision*
  - ✅ *Open Question*

👉 本质：**减少阅读上下文切换**

------

### 2️⃣ AI Skill：**跨 Confluence 的“知识问答”**

> 这是 M365 Copilot / Copilot Agent 的强项
>  （我司已有培训 & 录像 [[1\]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)[[2\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)）

你可以直接问：

- “XXX 项目 **为什么选了这个方案**？”
- “关于 **CAN / Modbus / FreeRTOS**，我们内部有没有既有规范？”

✅ 不用你记“文档在哪”

------

### 3️⃣ AI Skill：**文档生成（反向）**

你写完代码后：

- AI 帮你：
  - 从代码 → **自动生成 Confluence 草稿**
  - 结构化为：
    - 背景
    - 设计点
    - 限制
    - TODO

👉 非常适合你这种**技术强、但不想写文档**的人。

------

## 三、Jira：把「管理负担」降到最低

### 4️⃣ AI Skill：**需求拆解 / Story 生成**

你给 AI：

> “这是一个 Modbus 通信模块，支持读写寄存器，异常码处理”

AI 可直接输出：

- Epic
- Story
- Sub-task
- Acceptance Criteria

你只负责**校验 & 调整**

👉 本质：**减少“格式性思考”**

------

### 5️⃣ AI Skill：**Jira 语言翻译器**

很多 Jira 写得：

- 太抽象（PO）
- 太业务（非技术）

AI 可以：

- 把业务语言 → 技术语言
- 把模糊描述 → 可实现条件

------

### 6️⃣ AI Skill：**JQL / 查询助手**

不用记 JQL：

- “帮我找 **本 Sprint 未完成 + 我负责 + 和通信相关** 的任务”

------

## 四、代码：不是替你写，是替你“少想”

### 7️⃣ AI Skill：**代码骨架 / 模式生成**（GitHub Copilot）

你这种工程师，AI 的正确用法是：

- 状态机模板
- 驱动层 / HAL 层模板
- RTOS Task / Queue / Semaphore 样板

✅ 你仍然掌控结构
 ❌ 不再敲重复代码

------

### 8️⃣ AI Skill：**代码解释 & 快速回忆**

典型场景：

- 半年前写的代码
- 同事的 legacy 代码

AI 能：

- 用人话解释函数意图
- 总结模块职责
- 找出“看起来不合理的点”

------

### 9️⃣ AI Skill：**重构建议（不是自动改）**

- “这个函数职责是不是太多？”
- “这个 switch-case 有没有模式问题？”

⚠️ 你决定，AI 提醒

------

## 五、实验室 / 测试：这是你**最大 ROI 区域**

### 🔥10️⃣ AI Skill：**日志 / 波形 / Dump 分析**

你给：

- 串口 log
- 错误码
- 状态跳转记录

AI 可以：

- 找“异常模式”
- 推断可能原因
- 建议下一步验证手段

👉 本质：**减少盲猜**

------

### 🔥11️⃣ AI Skill：**测试用例生成**

从：

- 需求
- 接口定义

直接生成：

- 正常路径
- 边界条件
- 异常场景

✅ 非常适合实验室回归测试

------

### 12️⃣ AI Skill：**实验记录结构化**

你随便写：

> “今天测了 UART 在 115200 下偶发丢包…”

AI 帮你整理成：

- Test Objective
- Setup
- Result
- Conclusion

------

## 六、协作 / Review：让你少“盯人”

### 13️⃣ AI Skill：**PR Review 辅助**

AI 不负责“对错”，但很擅长：

- 风格问题
- 潜在 bug 模式
- 边界遗漏

👉 相当于一个**不疲劳的初级 Reviewer**

------

### 14️⃣ AI Skill：**会议 / 讨论记录总结**

你不需要再：

- 记会议
- 翻聊天记录

AI 直接给你：

- 决策点
- TODO
- 谁负责

------

## 七、周报 / 汇报 / PPT：把“痛苦活”自动化

### 15️⃣ AI Skill：**周报生成器（你已经在想这事）**

你只给：

- Jira 列表
- 几条实验结论

AI 自动输出：

- Key Tasks
- Achievements
- Learnings
- Next Steps

👉 这是我见过**程序员满意度最高的用法**

------

### 16️⃣ AI Skill：**技术 → 管理语言翻译**

你写：

> “解决了 Modbus 寄存器映射 bug”

AI 输出：

> “提升了通信稳定性，降低系统集成风险”

✅ 对你晋升/影响力极其重要

------

### 17️⃣ AI Skill：**PPT 自动生成**

你给：

- 周报 / Confluence
- 或 Jira Sprint

AI：

- 自动拆页
- 自动生成结构

------

## 八、进阶：你这种人，**非常适合 AI Agent**

结合我司已做的 **Copilot Agent 培训** [[1\]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)：

你可以做：

- “嵌入式知识 Agent”
  - FreeRTOS
  - 通信协议
  - 内部规范
- **“周报 Agent”**
- **“实验室日志 Agent”**

👉 一次配置，长期收益。

------

## 九、给你一个“实操优先级建议”（很关键）

如果你**只做 5 个**，我建议：

1. ✅ **周报 / 汇报 AI**
2. ✅ **实验日志 & Log 分析**
3. ✅ **代码解释 + 回忆**
4. ✅ **Jira Story / Sub-task 生成**
5. ✅ **Confluence 内容问答**

这 5 个能立刻：

- 节省时间
- 降低疲劳
- 提升“技术影响力”

------

## 最后一句实话（给你这种级别的人）

> **AI 不是让你变快一点点，而是让你：
>  把“高级工程师”该花的时间，
>  从“琐碎执行”挪回“判断 & 决策”。**

如果你愿意，下一步我可以直接帮你：

- 设计一个**“你个人的 AI 使用清单（每天用哪些 Prompt）”**
- 或给你一套**周报 / 实验室 / Jira 的现成 Prompt 模板**

你更想从哪一个开始？


References

[1] [Fw: 【DT Lunch & Learn】MS Copilot助力高效工作 - Part 3：Copilot Agent](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

[2] [Copilot](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)







> 
>
> # Prompt [可否帮我写一两个skill.md  | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZHTTROREZtT1dabExUUTJZV1l0TkdVeVpDMDRNMll6TFRZek1XTTVZV1U0TVRZMlpYd3lNREkyTFRBeUxUSTJWREF6T2pBNU9qSTFMamMxT1RNNU1qUmEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAyLTI2VDAzOjA5OjI1LjkzOVoifSwiY2hhdFR5cGUiOiJ3b3JrIiwidmVyc2lvbiI6MS4xfQ)
>
> 



当然可以。我给你直接写 **2 份可落地的 `skill.md`**（你拿去放到 repo 里就能用），分别覆盖你最高 ROI 的两块：**周报/汇报** 和 **实验室测试/日志分析**。内容会参考我司内部 Copilot/Agent 培训材料中提到的能力与场景（Copilot 在 Teams/Outlook/Word/PPT/Excel、以及 Copilot Agent 的构建思路与数据源/Prompt/测试优化/权限共享等）[[1\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1)[[2\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 2：Word & Excel & PPT/MS Copilot Training - Part2.pdf?web=1)[[3\]](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

------

## skill.md ①：Weekly Report / Work Update（周报生成）

```md
# Skill: Weekly Report Generator (周报/工作汇报生成器)

## 1) Purpose / 目的
把你一周的「碎片工作」自动整理成对外可读的周报/工作汇报：
- 节省时间：从“回忆+组织语言”变成“校对+补充”
- 节省脑力：减少上下文切换与表达负担
- 输出更稳：结构统一、表达更偏管理语言

> 适用载体：邮件/Teams消息/Confluence周报页/PowerPoint汇报稿  
> 适用工具：M365 Copilot（Teams/Outlook/Word/PPT）[1](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared%20Documents/Carrier%20China%20All%20Member/01%20Help%20Desk%20SOP%20%e7%94%a8%e6%88%b7%e6%97%a5%e5%b8%b8%e5%8a%9e%e5%85%ac%e6%93%8d%e4%bd%9c%e6%89%8b%e5%86%8c/Copilot%20-%20MS%20Copilot%e5%8a%a9%e5%8a%9b%e9%ab%98%e6%95%88%e5%b7%a5%e4%bd%9c%20-%20Part%201%ef%bc%9aChat%20&%20Outlook%20&%20Teams/Carrier%20Copilot%e4%b8%ad%e6%96%87%e5%9f%b9%e8%ae%ad.pdf?web=1)[2](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared%20Documents/Carrier%20China%20All%20Member/01%20Help%20Desk%20SOP%20%e7%94%a8%e6%88%b7%e6%97%a5%e5%b8%b8%e5%8a%9e%e5%85%ac%e6%93%8d%e4%bd%9c%e6%89%8b%e5%86%8c/Copilot%20-%20MS%20Copilot%e5%8a%a9%e5%8a%9b%e9%ab%98%e6%95%88%e5%b7%a5%e4%bd%9c%20-%20Part%202%ef%bc%9aWord%20&%20Excel%20&%20PPT/MS%20Copilot%20Training%20-%20Part2.pdf?web=1)

---

## 2) Inputs / 输入（越结构化越好）
推荐你每周只维护一个“原始素材区”（可放在 OneNote / Notes / Confluence 草稿）：

### A. Jira 维度（可选）
- Epic/Story Key + 标题 + 状态（Done/In Progress/Blocked）
- 本周新增的风险/阻塞（Blocker）及 owner

### B. 技术工作维度（强烈建议）
- 本周完成：2~5 条（按模块/功能）
- Debug/缺陷：Top 3（现象、定位结论、修复点）
- 测试/实验室：关键结论（用数据/现象说话）
- 下周计划：3~6 条（可交付 + 依赖）

### C. 影响力语言（可选但很加分）
- 价值/影响：稳定性、风险降低、效率提升、可复用资产沉淀

---

## 3) Output / 输出格式（默认模板）
### 3.1 Key Tasks (1-5)
- [模块/项目] 做了什么（动词开头）→ 产出/里程碑

### 3.2 Achievements & Key Learning
- Achievement：结果、数据、对系统/团队的正向影响
- Learning：踩坑/经验、后续规避措施

### 3.3 Risks / Blockers
- 风险描述 + 影响 + 需要的支持（谁/什么/何时）

### 3.4 Next Week Plan
- 任务 + 预期交付 + 依赖

---

## 4) Prompts / 提示词（可直接复制）
### Prompt 1：把碎片整理成周报（中文）
你是我的技术项目周报助手。请把以下素材整理为周报，输出结构为：
1) Key Tasks (1-5条) 
2) Achievements & key learning 
3) Risks/Blockers 
4) Next week plan
要求：
- 用简洁专业的中文
- 每条尽量包含“动作+结果/产出+影响”
- 对管理者可读（不要堆技术细节，但要保留关键事实）
素材如下：
<<<
（粘贴 Jira 列表/实验结论/会议结论/零散笔记）
>>>

### Prompt 2：技术语言 → 管理语言（中文）
请把下面的技术描述改写成对管理者更友好的表达：
- 保留事实（做了什么、结果是什么）
- 强调价值（稳定性、风险降低、交付保障、效率）
技术描述：
<<<
（粘贴原文）
>>>

### Prompt 3：周报 → PPT 汇报大纲（中文）
请把以下周报内容转换为 5-7 页汇报 PPT 的大纲：
- 每页给：标题 + 3-5 个要点
- 最后一页输出：风险与需要支持事项
周报：
<<<
（粘贴周报）
>>>

> Copilot 支持在 Word/PPT 中快速生成与重写内容，并可通过引用文档生成 PPT 结构与内容[2](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared%20Documents/Carrier%20China%20All%20Member/01%20Help%20Desk%20SOP%20%e7%94%a8%e6%88%b7%e6%97%a5%e5%b8%b8%e5%8a%9e%e5%85%ac%e6%93%8d%e4%bd%9c%e6%89%8b%e5%86%8c/Copilot%20-%20MS%20Copilot%e5%8a%a9%e5%8a%9b%e9%ab%98%e6%95%88%e5%b7%a5%e4%bd%9c%20-%20Part%202%ef%bc%9aWord%20&%20Excel%20&%20PPT/MS%20Copilot%20Training%20-%20Part2.pdf?web=1)

---

## 5) Quality Checklist / 自检清单（30秒）
- [ ] Key Tasks 是否 <= 5 条且“像交付”？
- [ ] Achievements 是否写了影响（质量/风险/效率/复用）？
- [ ] Risks 是否明确“需要谁支持什么”？
- [ ] Next Week Plan 是否可验收（有产出）？

---

## 6) Common Pitfalls / 常见坑
- 只写过程不写结果 → 加一句“产出/影响”
- 过度技术细节 → 把细节放附件/链接，正文保留结论
- 风险不落地 → 补“需要的资源/决策点”
```

------

## skill.md ②：Lab Test & Log Analyzer（实验室测试 / 日志分析）

```md
# Skill: Lab Test & Log Analyzer (实验室测试/日志分析助手)

## 1) Purpose / 目的
把实验室测试与日志分析从“人工扫 log + 猜原因”变成“结构化定位 + 下一步验证建议”：
- 快速从大量日志中提炼关键事件序列
- 归纳异常模式（触发条件/频率/关联状态）
- 输出可复用的测试记录与结论（便于复盘/周报/缺陷单）

> 适用工具：M365 Copilot（Teams/Word/OneDrive文件）、Copilot Agent（绑定知识库/规范/历史案例）[3](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)[1](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared%20Documents/Carrier%20China%20All%20Member/01%20Help%20Desk%20SOP%20%e7%94%a8%e6%88%b7%e6%97%a5%e5%b8%b8%e5%8a%9e%e5%85%ac%e6%93%8d%e4%bd%9c%e6%89%8b%e5%86%8c/Copilot%20-%20MS%20Copilot%e5%8a%a9%e5%8a%9b%e9%ab%98%e6%95%88%e5%b7%a5%e4%bd%9c%20-%20Part%201%ef%bc%9aChat%20&%20Outlook%20&%20Teams/Carrier%20Copilot%e4%b8%ad%e6%96%87%e5%9f%b9%e8%ae%ad.pdf?web=1)

---

## 2) Inputs / 输入（推荐最小集合）
### A. 环境与版本
- 板卡/固件版本/配置项（关键宏、RTOS、驱动版本）
- 外设连接（UART/SPI/I2C/CAN 等）与速率
- 复现条件（操作步骤、触发概率、是否与温度/供电相关）

### B. 日志与数据
- 串口 log（最好带时间戳）
- 错误码/断言/异常栈（如有）
- 波形/抓包摘要（可选：关键现象描述即可）

---

## 3) Outputs / 输出（默认模板）
### 3.1 Observations（现象）
- 发生了什么：错误码/断言/重启/卡死/丢包
- 触发条件：操作、负载、时序、环境
- 频率：偶发/稳定复现（如果不确定就写“不确定”）

### 3.2 Event Timeline（事件序列）
- 按时间顺序列出关键 log 点（只保留 5-15 条关键行）

### 3.3 Hypotheses（可能原因假设）
- 假设1：依据（对应 log/状态/资源）
- 假设2：依据
> 注意：假设要可证伪

### 3.4 Next Experiments（下一步验证）
- 1~3 个最小验证动作（加日志点、开关某宏、降速率、替换线缆、隔离变量…）
- 每个动作说明“预期观测到什么 → 证明/否定哪个假设”

### 3.5 Conclusion（阶段性结论）
- 当前最可能原因（若证据不足则写“未定”）
- 风险与影响
- 建议修复方向/Owner（可选）

---

## 4) Prompts / 提示词（可直接复制）
### Prompt 1：日志提炼为事件序列（中文）
你是嵌入式系统调试助手。请从以下日志中提炼“关键事件序列（Timeline）”，只保留能解释问题的关键行：
- 输出：1) 现象摘要 2) 事件序列（按时间）3) 可疑点（3-5条）
日志如下：
<<<
（粘贴log）
>>>

### Prompt 2：基于日志提出可证伪假设 + 下一步验证（中文）
请根据以下现象与日志，给出：
1) 可能原因假设（至少3个，按可能性排序）
2) 每个假设的证据（引用日志片段或现象）
3) 下一步最小验证动作（1-3个），并说明预期结果如何证明/否定假设
现象与日志：
<<<
（粘贴现象描述 + log）
>>>

### Prompt 3：自动生成测试记录（中文）
请把以下测试过程整理为正式的实验记录（可贴入Confluence）：
- Test Objective
- Setup（环境/版本/接线/配置）
- Steps（步骤）
- Result（结果）
- Conclusion（结论）
素材如下：
<<<
（粘贴你的碎片记录）
>>>

---

## 5) Optional: Agent Knowledge / 可选知识库（适合做 Copilot Agent）
建议绑定：
- 历史缺陷单（同类问题）
- 通信协议/驱动规范（UART/SPI/I2C/CAN/Modbus）
- 你的常用 debug 手册与 checklist
> Agent 构建通常包括：基础信息、数据源、Prompt、测试优化、权限共享等要素[3](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)

---

## 6) Quality Checklist / 自检清单
- [ ] Timeline 是否能“讲清楚发生了什么”？
- [ ] 假设是否可证伪（能通过实验排除）？
- [ ] 下一步验证是否“变量隔离”（一次只改一个因素）？
- [ ] 结论是否明确“证据强度”（确定/倾向/未定）？
```

------

### 你接下来怎么用（不需要任何额外工具）

- 你把上面两段分别存成：
  - `weekly_report.skill.md`
  - `lab_log_analyzer.skill.md`
- 然后每周/每次实验直接复制对应 Prompt，把素材贴进去，让 Copilot/你常用 AI 执行即可。[[1\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1)[[2\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 2：Word & Excel & PPT/MS Copilot Training - Part2.pdf?web=1)

------

如果你还想再加 **第 3 份**，我建议是你提到的 **“Confluence + Jira 联动的 Story/AC 生成 skill.md”**（专门解决 PO 写得抽象、你要自己翻译成可实现任务的问题）。你要我也一并补上吗？


References

[1] [Carrier Copilot中文培训](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1)

[2] [MS Copilot Training - Part2](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 2：Word & Excel & PPT/MS Copilot Training - Part2.pdf?web=1)

[3] [Fw: 【DT Lunch & Learn】MS Copilot助力高效工作 - Part 3：Copilot Agent](https://outlook.office365.com/owa/?ItemID=AAMkADQ3YzgwYjc3LTY2ODQtNGU5OS04MDQwLWU1YWVlODY1MzQ0NABGAAAAAABVU7lW5LKeTJScFNpPTLLeBwBD%2b9nE8ReJSa%2bIoMhtgcoFAAAAAAEJAABudD51YxXKRLmA%2bLIaq3rTAAVEf%2bfOAAA%3d&exvsurl=1&viewmodel=ReadMessageItem)



>
>
># prompt:帮我写Confluence+Jira联动的skill.md
>
>

Prompt [帮我写Confluence+Jira联动的skill.md | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZEbGhOelZqTVRFM0xUaG1ObUV0TkRabFlTMWlNREJqTFRRd09EVTNOV05rTXpjeVpud3lNREkyTFRBeUxUSTJWREF6T2pFMU9qSXlMakV4TkRJM05WbyUzZCIsInNjZW5hcmlvIjoic2hhcmVMaW5rVmlhUmljaENvcHkiLCJwcm9wZXJ0aWVzIjp7InByb21wdFNvdXJjZSI6InVzZXIiLCJjbGlja1RpbWVzdGFtcCI6IjIwMjYtMDItMjZUMDM6MTU6MjIuMzMwWiJ9LCJjaGF0VHlwZSI6IndvcmsiLCJ2ZXJzaW9uIjoxLjF9)

帮我写Confluence+Jira联动的skill.md

Response

好，这一份我直接给你**工程师可长期复用的版本**。
 下面是**完整可直接落地的 `skill.md`**，专门解决你说的痛点：

> **PO 写得抽象 / Confluence 一堆描述 → Jira 里我要能开发、能测试、能验收**

并且设计思路是**完全贴合我司当前的 Confluence + Jira + Copilot 使用方式**（内部已有 Copilot 中文培训、Confluence/Jira 培训与最佳实践可支撑这一工作流）[[1\]](https://carcgl.sharepoint.com/sites/Insight/asia/carrier_china/Documents/2025/Carrier Copilot中文培训.pdf?web=1)[[2\]](https://carcgl.sharepoint.com/sites/rlcchinasccoe/_layouts/15/Doc.aspx?sourcedoc={FBA029C7-837F-4C95-8B07-83C5EFF444A9}&file=7.Confluence training by Karthik.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[3\]](https://carcgl.sharepoint.com/sites/EnterpriseEnablement/SitePages/Jira.aspx?web=1)

------

# `skill.md`：Confluence ↔ Jira 联动（需求 → 可执行任务）

```md
# Skill: Confluence → Jira Story Decomposition
# （需求理解 & 可执行任务生成）

## 1) Purpose / 目的
把 Confluence 中偏“业务/概念/方案”的需求描述，自动转化为 Jira 中 **可开发、可测试、可验收** 的工作项：
- 减少 PO / 需求文档的二次“人工翻译”
- 避免遗漏边界条件、异常路径
- 统一 Story / Sub-task / AC（验收标准）质量
- 降低返工和反复澄清成本

> 适用工具：
> - Confluence（需求来源、设计文档）
> - Jira（Epic / Story / Sub-task / Bug）
> - M365 Copilot / Copilot Chat（文本理解与结构化）  
> Copilot 在文档理解、结构化重写、任务拆解方面已在内部培训中覆盖 [1](https://carcgl.sharepoint.com/sites/Insight/asia/carrier_china/Documents/2025/Carrier%20Copilot%e4%b8%ad%e6%96%87%e5%9f%b9%e8%ae%ad.pdf?web=1)

---

## 2) Typical Pain Points / 典型痛点
- Confluence 描述：
  - “支持 XXX 功能”
  - “系统需要考虑异常情况”
- Jira Story：
  - 无法直接开发
  - 测试无从下手
  - 验收标准模糊
- 工程师需要：
  - 自己补需求
  - 自己拆任务
  - 自己兜风险

👉 本 Skill 的目标：**把这些隐性工作显性化并自动化**

---

## 3) Inputs / 输入（来自 Confluence）
从 Confluence 页面中准备以下最小信息即可：

### A. 背景 & 目标
- 为什么要做（业务 / 项目目标）
- 要解决什么问题

### B. 功能性描述
- 正常流程（Happy Path）
- 涉及的模块 / 接口 / 外部系统（如果有）

### C. 已知约束（若有）
- 性能 / 资源限制
- 兼容性 / 历史包袱
- 不做什么（Out of Scope）

---

## 4) Outputs / 输出（写入 Jira）
### 4.1 Epic（如果需要）
- 面向业务/系统层面的目标
- 通常 1~2 行即可

### 4.2 User Story（工程师视角）
每条 Story 必须满足：
- 单一职责
- 可开发
- 可测试
- 可验收

### 4.3 Sub-tasks（建议拆分）
- Design / Analysis
- Implementation
- Unit Test
- Integration / Lab Test
- Documentation（如必须）

### 4.4 Acceptance Criteria（最关键）
- 正常路径
- 边界条件
- 异常路径
- 验证方式（如何判断 Done）

---

## 5) Core Prompt / 核心提示词（直接复制用）

### Prompt 1：Confluence → Jira Story（中文）
你是一个资深嵌入式/软件工程师和敏捷实践者。
请将以下 Confluence 需求内容，拆解为 Jira 中可执行的工作项。

输出结构：
1) Epic（如适合）
2) User Stories（每条包含：As a / I want / So that）
3) 每条 Story 对应的 Sub-tasks
4) 每条 Story 的 Acceptance Criteria（可测试、可验收）

要求：
- 避免抽象表述
- 明确边界条件和异常情况
- 假设代码需要在真实产品环境中运行
- 若需求信息不足，请显式列出“待澄清问题”

Confluence 内容如下：
<<<
（粘贴 Confluence 页面或段落）
>>>

---

### Prompt 2：识别需求风险 & 缺失点（中文）
请从以下需求描述中，识别：
1) 模糊点 / 歧义点
2) 潜在技术风险
3) 测试和验收可能遗漏的场景
4) 建议向 PO / 系统工程师澄清的问题清单

需求内容：
<<<
（粘贴 Confluence 内容）
>>>

---

### Prompt 3：Story → 测试导向 AC（中文）
请将以下 Jira Story 转换为 **测试友好型 Acceptance Criteria**：
- 使用 Given / When / Then
- 覆盖正常、边界、异常场景
- 每条 AC 必须可验证

Jira Story：
<<<
（粘贴 Story）
>>>

---

## 6) Recommended Workflow / 推荐工作流
1. PO / 系统工程师在 Confluence 写需求
2. 工程师复制关键段落 → Copilot 执行 Prompt 1
3. 生成 Story / Sub-task / AC
4. 用 Prompt 2 快速扫一遍风险 & 澄清点
5. 将结果粘贴回 Jira
6. Sprint Planning 时直接评估 & 估点

👉 把“需求翻译”前移、一次性完成

---

## 7) Quality Checklist / 自检清单（2 分钟）
- [ ] 每条 Story 是否可在 1 个 Sprint 内完成？
- [ ] 是否明确“不满足条件时系统怎么表现”？
- [ ] AC 是否可以直接交给测试/实验室验证？
- [ ] 是否还有“只能靠口头解释”的地方？

---

## 8) Common Anti‑Patterns / 常见反模式
❌ “支持 XXX 功能”  
✅ “在 XXX 条件下，系统应当 XXX，并在失败时返回 XXX 错误码”

❌ “考虑异常情况”  
✅ 明确列出 3~5 个异常输入/状态

❌ Story = Task  
✅ Story 描述“行为 + 价值”，Task 描述“怎么做”

---

## 9) Extension / 进阶用法（可选）
- 将历史 **高质量 Story + AC** 作为 Copilot Agent 的知识源
- 用于：
  - 新需求自动对齐既有标准
  - 新人快速写出可用 Jira Story

> Copilot Agent 已在内部作为“知识问答/自动化助手”进行过系统培训与示例展示 [1](https://carcgl.sharepoint.com/sites/Insight/asia/carrier_china/Documents/2025/Carrier%20Copilot%e4%b8%ad%e6%96%87%e5%9f%b9%e8%ae%ad.pdf?web=1)
```

------

## 为什么这一份对你特别有用（说人话）

- 你本来就在 **帮 PO 把话翻译成人话**
- 现在只是：
  - ✅ 把翻译步骤标准化
  - ✅ 把“容易漏的点”显性化
  - ✅ 把脑力从“补需求”挪回“做判断”

------

### 下一步我可以继续帮你做的（不加概念，只加价值）

- ✅ `Bug.story.skill.md`（现象 → 复现 → Root Cause → Fix → Regression）
- ✅ `SprintPlanning.skill.md`（一页 Confluence → 一整个 Sprint）
- ✅ 把你这 **3~4 个 skill.md 整合成一个「工程师 AI Playbook」**

你想下一份从 **Bug / 缺陷** 还是 **Sprint Planning** 开始？


References

[1] [Carrier Copilot中文培训](https://carcgl.sharepoint.com/sites/Insight/asia/carrier_china/Documents/2025/Carrier Copilot中文培训.pdf?web=1)

[2] [7.Confluence training by Karthik](https://carcgl.sharepoint.com/sites/rlcchinasccoe/_layouts/15/Doc.aspx?sourcedoc={FBA029C7-837F-4C95-8B07-83C5EFF444A9}&file=7.Confluence training by Karthik.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

[3] [Jira Support](https://carcgl.sharepoint.com/sites/EnterpriseEnablement/SitePages/Jira.aspx?web=1)





> 
>
> # Prompt:这些写好的 SKILL.md, 可以给 copilot 使用不？ 是不是 使用 agent 
>
> 



这是个**非常关键、而且你已经走在正确方向上的问题** 👍 我直接给你**结论 + 架构图式解释**，避免概念混乱。

------

# 一句话结论（先给你定心丸）

> ✅ **可以给 Copilot 用**, **但分两种用法**, **不是“自动就会用”**, **做成 Agent 是“升维用法”，不是必须**

------

## 先把 3 个层次说清楚（这是关键）

### ✅ Level 1：**直接 Copilot Chat 使用（最简单、现在就能用）**

**不需要 Agent**

你现在写好的 `SKILL.md`：

```text
weekly_report.skill.md
lab_log_analyzer.skill.md
confluence_jira.skill.md
```

👉 **本质是：Prompt Playbook**

### ✅ 怎么用

你在 **Copilot Chat / Teams Copilot / Web Copilot** 里：

> “请按照下面这个 skill 执行任务：（粘贴 skill.md 中的 Prompt 区块 + 素材）”

✅ Copilot **完全可以理解并执行**,不需要任何配置, ❌ 但：**你每次要手动粘贴**

> 这正好符合目前 Copilot 官方培训中反复强调的： **Prompt = 能力放大器**，不需要额外系统即可用 [[1\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 2：Word & Excel & PPT/MS Copilot Training - Part2.pdf?web=1)[[2\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={2B09EE9E-4D46-402C-B670-855959E00ADC}&file=Copilot FAQs.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

## ✅ Level 2：**把 SKILL.md 当“知识库”给 Copilot 用**

**仍然不需要 Agent（但更顺）**

### 你可以做什么

把这些 `SKILL.md` 放到：

- ✅ OneDrive 文件夹
- ✅ SharePoint 文档库
- ✅ Confluence 页面（更推荐）

然后直接问 Copilot：

> “请**按照 Confluence→Jira 的 skill**，帮我把这个需求拆成 Story”

✅ Copilot 会：

- 读你的 skill.md
- 按你的规范执行
- 不再每次粘 Prompt

这是 **M365 Copilot 官方支持的知识使用方式**： Copilot 会在你有权限的文档中进行理解和引用 [[3\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)[[4\]](https://carcgl.sharepoint.com/sites/CyberSecurity/SitePages/Microsoft-Copilot.aspx?web=1)

⚠️ 但注意：Copilot **不会“强制总是用它”**, 它是**“被动参考”**

------

## ✅ Level 3（进阶）：**做成 Copilot Agent（你问的重点）**

**是的，这才叫“让它变成技能”**

### ✅ 什么情况下你才“需要 Agent”？

当你满足下面 ≥2 条：

- ✅ 你**反复做同一类事情**
- ✅ 你希望**不写 Prompt**
- ✅ 你希望**别人也能用**
- ✅ 你希望**输入一句话就按规范输出**

👉 那就**非常适合 Agent**

------

## Copilot Agent 是什么（说人话）

> **Agent = 固化 Prompt + 固化规范 + 固化知识源**

在 Copilot Studio 里，一个 Agent 通常由：

1. **Knowledge（知识）**
   - 你的 `SKILL.md`
   - 历史高质量 Story / 周报 / 测试记录
   - Confluence / SharePoint 页面 [[5\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4217045058/End+User+Guide+Knowledge+Source+Integration)
2. **Instruction / System Prompt**
   - “你是一个嵌入式软件工程助手”
   - “严格按照 skill.md 执行”
   - “输出必须符合 Jira/Confluence 模板”
3. **Topic（可选，但专业）**
   - “生成 Jira Story”
   - “分析实验室日志”
   - “生成周报”

> Topic 本质就是：**技能入口** [[6\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4176511024/End+User+Guide+Understanding+and+Using+Topics+in+Microsoft+Copilot+Studio)

------

## 用你的例子说（非常贴你）

### 🎯 Agent 名字

> **SW Delivery Assistant**

### 🎯 它会什么

- “根据 Confluence 拆 Jira Story”
- “生成测试导向的 AC”
- “把实验记录变成周报”

### 🎯 你怎么用

你只说一句：

> “帮我按标准拆这个需求”

✅ 它自动：

- 用 `confluence_jira.skill.md`
- 用你绑定的知识库
- 输出标准 Story + AC

------

## ⚠️ 重要但容易踩坑的点（你一定要看）

### ❌ 误区 1：以为“不做 Agent 就没用”

**错**

> **80% 的收益来自 skill.md 本身**

Agent 是 **放大器**，不是前提。

------

### ❌ 误区 2：一开始就做复杂 Agent

**错**

> 正确路径是：skill.md → 用顺 → 稳定 → 再 Agent 化

------

### ✅ 推荐你的“现实最优解”

结合你现在的阶段，我建议：

| 阶段   | 做法                                 |
| ------ | ------------------------------------ |
| 现在   | ✅ 直接用 skill.md + Copilot Chat     |
| 1~2 周 | ✅ 放到 Confluence，当“规范+Prompt库” |
| 稳定后 | ✅ 做 **1 个 Agent**（不是 10 个）    |

------

## 最后一句给你（工程师视角）

> **你写的不是 prompt， 是“工程经验的 API”。**

Agent 只是把这个 API **产品化**。

------

如果你愿意，下一步我可以直接帮你：

- ✅ **把某一个 skill.md 拆成 Agent 的 Instruction + Knowledge 结构**
- ✅ 或直接给你一个 **“Copilot Agent 配置草图（不用点 UI）”**

你更想先 **Agent 化哪一个？**
 （周报 / 实验室 / Confluence→Jira）


References

[1] [MS Copilot Training - Part2](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 2：Word & Excel & PPT/MS Copilot Training - Part2.pdf?web=1)

[2] [Copilot FAQs](https://carcgl.sharepoint.com/sites/DigitalTeamSite/_layouts/15/Doc.aspx?sourcedoc={2B09EE9E-4D46-402C-B670-855959E00ADC}&file=Copilot FAQs.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

[3] [Copilot](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)

[4] [Microsoft Copilot - Carrier Approved AI Companion](https://carcgl.sharepoint.com/sites/CyberSecurity/SitePages/Microsoft-Copilot.aspx?web=1)

[5] [End User Guide: Knowledge Source Integration](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4217045058/End+User+Guide+Knowledge+Source+Integration)

[6] [End User Guide: Understanding and Using Topics in Microsoft Copilot Studio](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4176511024/End+User+Guide+Understanding+and+Using+Topics+in+Microsoft+Copilot+Studio)





> 
>
> # Prompt [先用 skill.md 直接做个需求拆解示范 | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZEazFNR1k0TVRVMUxUYzBNRGt0TkdWaE1TMDVaREZsTFRRM01ETXpNVEkwWW1ZMVkzd3lNREkyTFRBeUxUSTJWREF6T2pJek9qVXlMamN4TXpNNU9EaGEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAyLTI2VDAzOjIzOjUyLjg5OFoifSwiY2hhdFR5cGUiOiJ3b3JrIiwidmVyc2lvbiI6MS4xfQ)
>
> 

先用 skill.md 直接做个需求拆解示范

Response

可以，我给你做一个**“skill.md 直接驱动”的需求拆解示范**。为了示范更贴近你日常，我选用你们内部已有的 **Modbus 协议文档**来当“Confluence 需求输入”（因为它本身就包含新增点位注意事项、功能码限制、寄存器范围等关键约束），然后按我们写的 Confluence→Jira skill 的输出结构，拆成 **Epic / Stories / Sub-tasks / AC / 澄清问题**。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

> 说明：下面“需求输入”是我从 `[Modbus通信协议1.7.7（20250603）.docx](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc=%7B3402595B-8E76-4470-B6A2-B8230EF3A7EF%7D&file=Modbus%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE1.7.7%EF%BC%8820250603%EF%BC%89.docx&action=default&mobileredirect=true&DefaultItemOpen=1&EntityRepresentationId=cb3040d1-8e18-4d80-bcbd-53f290d99c4b)` 摘取并概括成一个典型需求场景（新增点位、寄存器映射、功能码限制等），用于演示拆解。文档里明确提到：一次读≤64寄存器、常用功能码 0x03/0x04/0x06/0x10、以及“新增点位需确认旧协议未使用该寄存器”等约束。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

## 0) Confluence 需求输入（示范）

**标题**：新增 Modbus 点位（读写寄存器）用于设备状态与控制
 **要点/约束（来自协议文档）**

- Modbus 功能码：读多个寄存器 0x03（一次读≤64个寄存器），读输入寄存器 0x04，写单寄存器 0x06，写多寄存器 0x10 [[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- 新增点位：必须确认旧 Modbus 协议没有使用该寄存器（避免冲突）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- 数据编码示例：0x1234 发送顺序 0x12 再 0x34（高字节在前）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- 机型/映射相关字段存在（例如：机型组合地址映射、协议版本号用 BCD 等）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

**本次新增需求（示范场景）**

- 新增 3 个“只读状态点位”（比如运行模式、故障汇总、关键温度）
- 新增 2 个“可写控制点位”（比如强制开关、目标值）
- 要求：协议更新、实现映射、可被上位机读写验证、并确保兼容

------

# 1) Jira Epic（1 个）

**Epic：Modbus 协议点位扩展（新增状态/控制寄存器）**
 目标：在不破坏存量点位与主站兼容性的前提下，新增若干状态与控制寄存器，并完成实现与验证。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

# 2) Jira User Stories（按“可交付”拆）

## Story 1：寄存器地址规划与冲突检查

**As a** 协议维护者/开发
 **I want** 为新增点位分配寄存器地址并验证不与旧协议冲突
 **So that** 新旧主站/设备不会因地址重叠产生读写错误。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### Sub-tasks

- T1.1：从协议文档梳理现有寄存器占用区间（对应机型/段落）
- T1.2：为 5 个新增点位选择目标地址（输入寄存器 or 保持寄存器）
- T1.3：执行冲突检查（确认旧协议未使用该寄存器）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- T1.4：输出“地址分配表 + 数据类型/单位/读写属性”

### Acceptance Criteria（测试导向）

- AC1：每个新增点位都有唯一地址、数据类型、读写属性、单位说明
- AC2：有明确证据表明地址未被旧协议占用（例如：对照表/检查记录）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### 待澄清问题

- Q1：新增点位归属哪一类地址空间（Input Register 0x04？Holding Register 0x03/0x06/0x10？）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- Q2：每个点位的数据类型（Uint16 / Uint32 / Float32）与缩放倍率/单位？

------

## Story 2：读状态点位（0x03/0x04）实现与验证

**As a** 上位机（主站）
 **I want** 通过 0x03/0x04 读取新增状态寄存器
 **So that** 能获取设备关键状态用于监控。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### Sub-tasks

- T2.1：实现 3 个只读点位的变量采集与寄存器映射
- T2.2：确保一次读请求不超过 64 寄存器的限制（必要时拆分读取块）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- T2.3：补充日志/调试输出（便于现场定位：请求地址、寄存器数量、返回码）
- T2.4：台架验证：构造主站读取并对照实际变量

### Acceptance Criteria

- AC1：主站使用 0x03 或 0x04 读取，返回数据与设备内部变量一致
- AC2：当请求寄存器数量 > 64 时，系统有明确处理策略（拒绝/异常码/建议拆包），且不会异常崩溃[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- AC3：数据字节序符合协议示例（高字节先发）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### 待澄清问题

- Q3：状态点位是放在 Input register 还是 Holding register？（文档对机型基本信息使用 0x04 有示例）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

## Story 3：写控制点位（0x06 / 0x10）实现与安全约束

**As a** 上位机（主站）
 **I want** 通过 0x06 / 0x10 写入新增控制寄存器
 **So that** 能下发控制指令并被设备正确执行。[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### Sub-tasks

- T3.1：实现 2 个可写点位的寄存器写入回调/处理逻辑
- T3.2：实现写入生效策略（例如：状态变化才执行写入；这类策略在文档中提到控制指令仅在变化时下发的建议）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- T3.3：增加输入合法性检查（范围、枚举值、互斥条件）
- T3.4：异常码/错误处理（非法地址、非法值、写保护等）
- T3.5：回读验证（写后读一致性）

### Acceptance Criteria

- AC1：使用 0x06 写单寄存器：写入成功后，读回值一致，且控制行为生效[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- AC2：使用 0x10 写多寄存器：在合法范围内写入成功；在非法值/非法地址时返回明确失败（异常码或拒绝）
- AC3：写入不会破坏系统实时性/安全性（至少在测试中无异常重启/卡死）

### 待澄清问题

- Q4：控制点位是否需要“生效开关/强制控制单独地址片区”？（文档中提到“强制控制数据需要单独一片地址；强制控制需要有生效开关”）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

## Story 4：协议文档更新（Confluence）与版本标识

**As a** 协议使用者（上位机/测试/集成）
 **I want** 在 Confluence 协议文档中看到新增点位说明与版本变更记录
 **So that** 所有人能按一致规则读写与验收。[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### Sub-tasks

- T4.1：更新点位表（地址、读写属性、数据类型、单位、说明）
- T4.2：更新“更改记录”与版本号（示例：协议版本采用 BCD 表示的说明存在）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- T4.3：补充读取限制（一次读≤64寄存器）与功能码说明[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

### Acceptance Criteria

- AC1：Confluence/协议文档包含新增点位完整定义
- AC2：版本变更记录可追溯（新增项、日期、版本）[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

------

# 3) （可选）Bug/Task 补充：集成验证与回归

如果你们流程里习惯把验证单独列任务：

- Task：主站仿真/抓包验证（0x03/0x04/0x06/0x10 全覆盖）[[1\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)[[2\]](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)
- Task：回归测试：确认旧点位读写不受影响（尤其是相邻地址区间）

------

# 4) 你在 Copilot 里怎么“照抄式”做同样的事（最短操作）

你只要把这段扔给 Copilot（复制即用）：

```markdown
你是资深嵌入式/软件工程师和敏捷实践者。
请将以下 Confluence 需求内容拆解为 Jira 可执行工作项，输出：
1) Epic
2) User Stories（As a/I want/So that）
3) 每条 Story 的 Sub-tasks
4) 每条 Story 的 Acceptance Criteria（Given/When/Then 优先）
5) 待澄清问题清单（必须列出）

Confluence 内容：
<<<
（粘贴协议文档里相关段落：功能码/限制/新增点位注意事项 + 你的新增点位描述）
>>>
```



## 我只问你 1 个最关键的信息（为了把示范变成“你真实可用的 Jira”）

你这次要拆的需求，**Confluence 原文/要点**你可以直接粘一段（哪怕只有 5 行）。
 只要有原文，我就能按上面的格式**给出可直接粘进 Jira 的 Story + AC**（包含边界/异常/验证步骤）。


References

[1] [Modbus通信协议1.7.7（20250603）](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={3402595B-8E76-4470-B6A2-B8230EF3A7EF}&file=Modbus通信协议1.7.7（20250603）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)

[2] [Modbus通信协议1.7.4（20240314）](https://carcgl-my.sharepoint.com/personal/qing_liu4_carrier_com/_layouts/15/Doc.aspx?sourcedoc={C9DF6FF0-7687-4728-A642-947E4DDD6A3F}&file=Modbus通信协议1.7.4（20240314）.docx&action=default&mobileredirect=true&DefaultItemOpen=1)



