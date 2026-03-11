Prompt [claude code 出品的 find skills  和 skill_creater，可以用在... | Try in Copilot Chat](https://m365.cloud.microsoft/chat/entity1-d870f6cd-4aa5-4d42-9626-ab690c041429/eyJpZCI6IlZYTmxjbFl4ZkdoMGRIQnpPaTh2YzNWaWMzUnlZWFJsTFdsdWRDNXZabVpwWTJVdVkyOXRMM3hQU1VRNk1EVXlObVV4WXpBdFl6Y3dZeTAwTkdFM0xUazNNall0TXpjNFlqWXdNRE0wTWpFemZHRXpNak13WkdSaExXTXpNalF0TkRVeFpTMDVNMll4TFRKbVpEY3dNelk0Tm1WbU5ud3lNREkyTFRBeUxUSTRWREF6T2pFMU9qVXpMalk0TURRMU5qRmEiLCJzY2VuYXJpbyI6InNoYXJlTGlua1ZpYVJpY2hDb3B5IiwicHJvcGVydGllcyI6eyJwcm9tcHRTb3VyY2UiOiJ1c2VyIiwiY2xpY2tUaW1lc3RhbXAiOiIyMDI2LTAyLTI4VDAzOjE1OjUzLjg3MloifSwiY2hhdFR5cGUiOiJ3b3JrIiwidmVyc2lvbiI6MS4xfQ)

claude code 出品的 find skills  和 skill_creater，可以用在 copilot 企业版吗，怎么操作

Response

结论先说：**Claude Code 的 `find skills` / `skill_creator` 这套“Skills（SKILL.md + 插件命令）”机制，不能“直接”在 Microsoft 365 Copilot 企业版里原样运行**——因为它们是 **Anthropic/Claude Code 的插件与技能体系**（例如 `/skill-creator` 这类命令），而 **Copilot 企业版走的是“Copilot Chat + Agents（Copilot Studio / SharePoint Agents / Declarative Agents）”的扩展路线**。不过你完全可以把它们的**方法论/模板内容迁移**到 Copilot 里，用“Copilot Agent”或“提示词模板”实现同等效果。下面给你一套可落地的操作方式。

------

## 1) 为什么不能直接用（一句话理解）

- Claude Code 的 `skill_creator` 属于 **Claude 的插件/Skill 调用体系**（例如网页上明确写了用 `/skill-creator` 进入 Create/Eval/Improve/Benchmark 模式）。[[1\]](https://claude.com/plugins/skill-creator)
- Copilot 企业版的“可扩展”主要是 **Agents**（Copilot Studio Lite/Studio、SharePoint Agents、Declarative/Custom agents 等），不是 Claude 的 SKILL.md 插件系统。你们内部材料也强调了 Agent 这条路径，并提到如果看不到 Agents，需要申请权限。[[2\]](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1)[[3\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4316102755/End-User+Guide+for+Building+Copilot+Studio+Solutions)

所以：**不能把 Claude 的 `/skill-creator` 直接搬到 Copilot 里运行**；但可以把“Skill 的结构化提示词/工作流/评测方法”搬过去。

------

## 2) 你在 Copilot 企业版里能实现什么“等价物”

把 Claude 的两件事拆开看：

### A. `find skills`（找现成技能/模板）→ Copilot 的等价做法

1. **企业内检索现成模板/指南**：在 Copilot（Work）里直接搜你们已有的 Copilot/Agent 指南与培训材料，例如 [Copilot](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1&EntityRepresentationId=7a8a42f0-b741-44ce-88cd-c48819a5b2b3)、[Carrier Copilot中文培训.pdf](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1&EntityRepresentationId=dfa3e4c1-f386-4e9f-9323-957d433cbbf9)、[Copilot for CST – What it Is, How to Use It and Why It Matters](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1&EntityRepresentationId=d4510878-7883-4f0c-96f0-13eb24a276c3)、以及 End-User Guide for Building Copilot Studio Solutions 这种“怎么做 Agent/解决方案”的文档。[[4\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)[[5\]](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1)[[2\]](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1)[[3\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4316102755/End-User+Guide+for+Building+Copilot+Studio+Solutions)
2. **把你自己的 SKILL.md 当“可检索知识库”**：把 SKILL.md（或你整理的模板）放在 SharePoint/OneDrive 的固定目录，然后让 Copilot（Work）引用这些文件做“提示词库/规范库”。

> 这相当于：Claude 的“skills 市场/目录”，在 Copilot 里用“企业文件 + 搜索 + 引用来源”来实现。

### B. `skill_creator`（生成/迭代技能）→ Copilot 的等价做法

1. **用 Copilot Studio 做一个“Skill Builder Agent”**：让它专门负责“收集需求→生成模板→生成评测用例→输出可复制的提示词/说明文档”。你们的内部指南明确给了 Copilot Studio 的入口与“Solution/Agent/Flow/Connector”组织方式。[[3\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4316102755/End-User+Guide+for+Building+Copilot+Studio+Solutions)

2. 用 Agent 的“说明书 + 约束 + 示例”替代 SKILL.md

   ：把你 Claude Skill 里那套 frontmatter（name/description/触发条件）转成 Copilot Agent 的：

   - **用途描述（Trigger/When to use）**
   - **输入输出格式（I/O Schema）**
   - **执行步骤（Workflow）**
   - **质量门槛（Quality gates）**
   - **示例（Examples）**

3. 评测/迭代（Eval/Improve/Benchmark）怎么替代？

   Copilot 目前没有“跟 Claude 插件一模一样”的 

   ```
   /skill-creator
   ```

    四模式，但你可以用两种方式实现“工程化迭代”：

   - **在 Agent 里内置“自测清单 + 对照用例”**：每次生成输出时强制跑一遍 checklist（比如：是否包含行动项表格、是否标明负责人/截止/验收标准等）。
   - **把测试用例存成文件/表格**（Excel/Markdown），让 Copilot 每次都按用例回归检查（这就是你在公司里最容易落地的“准 Benchmark”）。

------

## 3) 具体怎么操作（你照着做即可）

### 路线 1：你已经有 Agents 权限 → 直接上 Copilot Studio（推荐）

1. 打开 Copilot Studio：按 End-User Guide for Building Copilot Studio Solutions 的指引进入 Copilot Studio，并在对应 Environment 里创建 Solution。[[3\]](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4316102755/End-User+Guide+for+Building+Copilot+Studio+Solutions)
2. 在 Solution 里新建一个 Agent，命名例如：**Skill_Creator_Equivalent**
3. 把你 Claude 的 `skill_creator` 逻辑写进 Agent 的系统指令/对话开场（例如：先问 6 个问题：目标、触发场景、输入、输出格式、边界条件、验收标准）
4. 输出物做成两份：
   - **“Copilot 提示词模板”（可复制粘贴）**
   - **“Agent 使用说明/自测清单”（可长期维护）**
5. 如果你想“像 find skills 一样可检索”，就把这些模板文件统一存放到 SharePoint，并在模板里要求 Copilot 引用来源文件。

> 你们内部材料也明确提到：Agent building in Copilot Studio Lite 是一条能走通的路；如果你看不到 Agents，需要提权限申请。[[2\]](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1)

### 路线 2：你没有 Agents 权限 → 用“文件模板 + Copilot Chat”也能跑（最快落地）

1. 建一个文件夹：`/Copilot_Prompts/Skills/`（SharePoint 或 OneDrive）
2. 每个“skill”一个 Markdown 文件（相当于 SKILL.md），内容包含：
   - 适用场景（When to use）
   - 输入要求（Input contract）
   - 输出结构（Output schema）
   - 步骤（Workflow）
   - 自测清单（Quality gates）
3. 你在 Copilot Chat（Work）里这样用：
   - “请引用我文件夹里 XX 模板，按模板流程生成输出；输出后按自测清单逐条检查并修正。”
4. `find skills` 的等价就是：让 Copilot 在该目录里“按场景列出最匹配的 3 个模板并解释理由”。

------

## 4) 你需要注意的权限点（避免你卡住）

在你们内部资料 [Copilot for CST – What it Is, How to Use It and Why It Matters](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1&EntityRepresentationId=d4510878-7883-4f0c-96f0-13eb24a276c3) 里写得很直白：**如果你在 Copilot 里看不到 Agents 区域，通常是权限不包含创建/管理 Agent，需要提申请**。[[2\]](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1)
 另外 [Copilot](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1&EntityRepresentationId=7a8a42f0-b741-44ce-88cd-c48819a5b2b3) 页面也提到在企业内如何申请 Copilot 相关能力（例如通过内部工单渠道申请 license/访问）。[[4\]](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)

------

## 5) 给你一个“迁移模板”（把 Claude Skill 变 Copilot Agent 指令）

你可以把 Claude 的 SKILL.md 核心段落直接改成下面这种结构，Copilot Chat/Agent 都能用：

**【Copilot Skill Template】**

- **Name**：XXX
- **When to use**：列 3–5 条触发条件
- **Inputs required**：必填/可选字段（含示例）
- **Output format**：固定 Markdown/表格/JSON 结构
- **Workflow**：Step1…StepN（每步都要有“检查点”）
- **Quality gates**：边界/一致性/不确定就标注来源
- **Examples**：2–3 个典型输入输出

这就是把 Claude Skill 的“可复用提示词资产”变成 Copilot 可消费的“提示词/Agent 说明书”。

------

## 我建议你下一步怎么做（不废话，直接推进）

如果你想我“按你现有的 Claude Skill”帮你一键迁移，我只需要你给我**任意一个**样例（贴 SKILL.md 内容或发 SharePoint 链接）。我会直接输出两份：

1. **Copilot Chat 可直接粘贴的 Prompt 版本**
2. **Copilot Studio Agent 的 System Instruction 版本（含自测清单）**

你更想先迁移哪一类 skill？（比如你前面常用的 code review / meeting minutes / daily report）


References

[1] [Skill Creator – Claude Plugin | Anthropic](https://claude.com/plugins/skill-creator)

[2] [Copilot for CST – What it Is, How to Use It and Why It Matters](https://carcgl.sharepoint.com/sites/Insight/news/Pages/Copilot-for-CST---What-it-Is,-How-to-Use-It-and-Why-It-Matters.aspx?web=1)

[3] [End-User Guide for Building Copilot Studio Solutions](https://carrier-digital.atlassian.net/wiki/spaces/AA/pages/4316102755/End-User+Guide+for+Building+Copilot+Studio+Solutions)

[4] [Copilot](https://carcgl.sharepoint.com/sites/DigitalTeamSite/SitePages/Copilot.aspx?web=1)

[5] [Carrier Copilot中文培训](https://carcgl.sharepoint.com/sites/ChinaDigitalHub/Shared Documents/Carrier China All Member/01 Help Desk SOP 用户日常办公操作手册/Copilot - MS Copilot助力高效工作 - Part 1：Chat & Outlook & Teams/Carrier Copilot中文培训.pdf?web=1)