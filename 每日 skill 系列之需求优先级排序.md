


> 别再拍脑袋定优先级了，AI+RICE 框架让决策有数据支撑

---

## 核心问题

作为产品经理，你是否经历过这些尴尬场景：

- 老板问“为什么先做 A 不做 B”，你只能说是“凭经验判断”
    
- 优先级评审会变成“谁声音大听谁的”
    
- 上线后发现 prioritization 错了，但为时已晚
    

用 `rice-scoring` + `kano-analyzer` + `jira-integration` 三个技能组合，可以让 AI 基于数据客观评分，优先级决策不再拍脑袋。

---

## 为什么这个技能组合值得学

### 行业数据

|来源|发现|
|---|---|
|Productboard 调研|AI 辅助优先级排序，决策准确度提升 47%|
|Aha!《12 Prioritization Frameworks》|RICE、WSJF、Kano 是最常用的三大框架|
|Agile Seekers|AI 情感分析让 Kano 模型从“静态分类”变“动态更新”|

### 传统方法 vs AI 工作流

|环节|传统方法|AI 工作流|
|---|---|---|
|数据收集|手动整理 Excel|AI 自动聚合多源数据|
|影响评估|主观猜测|AI 基于历史数据预测|
|用户价值|抽样调研|AI 全量情感分析|
|成本估算|拍脑袋|AI 参考历史类似功能|
|最终决策|会议室争论|数据驱动评分|

---

## 技能组合详解

### 技能 1：rice-scoring（RICE 框架自动化）

**核心能力**：

- 自动计算 Reach（覆盖人数）、Impact（影响程度）、Confidence（置信度）、Effort（工作量）
    
- 基于历史数据预测 Impact 分数
    
- 输出可解释的评分报告
    

**安装命令**：

  

# 使用 Anthropic 官方 knowledge-work-plugins（包含产品管理技能）  
git clone https://github.com/anthropics/knowledge-work-plugins.git ~/.local/share/skills/knowledge-work-plugins  
   
# 或使用社区实现的 RICE 评分技能  
git clone https://github.com/VoltAgent/awesome-agent-skills.git ~/.local/share/skills/agent-skills

**说明**：

- RICE 评分功能包含在 Anthropic 官方的 knowledge-work-plugins 中
    
- 路径：`product-management/skills/roadmap-update/`
    
- 支持 Reach、Impact、Confidence、Effort 四个维度评分
    

**使用示例**：

  

请对以下功能请求进行 RICE 评分：  
   
【功能列表】  
1. 员工请假审批自动化  
2. 绩效目标跟踪提醒  
3. 团队日历可视化  
  
【背景信息】  
- 季度目标：提升用户活跃度 25%  
- 团队产能：8 名工程师  
- 当前用户基数：5000 企业用户  
  
【评估要求】  
- Reach：基于功能覆盖的用户比例  
- Impact：参考历史类似功能的数据  
- Confidence：标注数据来源（数据/假设）  
- Effort：工程师天数为单位  
  
输出格式：表格 + 推荐优先级排序

---

### 技能 2：kano-analyzer（Kano 模型 + 情感分析）

**核心能力**：

- 基于用户反馈自动分类基本型/期望型/兴奋型需求
    
- 情感分析识别“兴奋点”何时变“基本点”
    
- 跨市场/人群对比分析
    

**安装命令**：

  

\# Kano 分析功能包含在 Anthropic 官方 knowledge-work-plugins 中  
\# 或使用社区实现的技能  

git clone https://github.com/VoltAgent/awesome-agent-skills.git ~/.local/share/skills/agent-skills

**说明**：

- Kano 模型分析功能在 knowledge-work-plugins 的产品管理技能中
    
- 支持基本型/期望型/兴奋型需求分类
    
- 结合情感分析识别需求类型转变
    

**使用示例**：

  

请分析这 2000 条用户反馈，执行 Kano 分类：  
@用户反馈/2025_Q1_feedback.csv  
   
【分析维度】  
1. 基本型需求（Must-be）：没有会不满，有了不满意的  
2. 期望型需求（One-dimensional）：越好越满意的  
3. 兴奋型需求（Delighters）：没有不在意，有了很惊喜的  
   
【额外要求】  
- 识别哪些"兴奋点"正在变成"基本点"  
- 对比不同地域用户的情感差异  
- 输出每个分类的 Top 5 需求

---

### 技能 3：jira-integration（Jira Product Discovery 集成）

**核心能力**：

- 直接连接 Jira Product Discovery
    
- 将 AI 评分同步到 Jira 优先级字段
    
- 与研发团队的工作流打通
    

**安装命令**：

  

# Jira 集成使用 MCP Server（社区实现）  
npm install -g @sooperset/mcp-atlassian  
   
# 或使用官方 Atlassian MCP Server  
npm install -g @atlassian/rovomcp-server

**说明**：

- Jira 集成通过 MCP（Model Context Protocol）实现
    
- @sooperset/mcp-atlassian： 支持 Jira Cloud 和 Server
- @atlassian/rovomcp-server： Atlassian 官方 Rovo MCP Server
- 需要配置 Jira API Token 和环境变量
    

**使用示例**：

  

请连接 Jira Product Discovery 并执行：  
   
【数据同步】  
- 拉取所有待排期的功能请求  
- 关联的用户反馈和投票数据  
- 历史类似功能的实际 Impact 数据  
  
【评分计算】  
- 运行 RICE 评分  
- 运行 Kano 分类  
- 综合评分排序  
  
【输出】  
- 更新 Jira 中的优先级字段  
- 生成优先级评审会材料  
- 输出到：@交付物/优先级评审_2025Q2.pptx

---

## 完整工作流演示

### 场景：季度优先级评审会

**输入**：

- 功能请求列表（20 个候选）
    
- 用户反馈数据（5000 条）
    
- 历史功能 Impact 数据
    
- 团队产能约束
    

**执行流程**：

  

第 1 步：数据聚合（约 30 分钟）  
├─ jira-integration 拉取功能请求  
├─ 关联用户反馈数据  
└─ 整理历史 Impact 数据  
  
第 2 步：RICE 评分（约 30 分钟）  
├─ rice-scoring 计算各项分数  
├─ 标注数据来源（数据/假设）  
└─ 输出初步排序  
  
第 3 步：Kano 分类（约 30 分钟）  
├─ kano-analyzer 情感分类  
├─ 识别需求类型  
└─ 标记"兴奋点→基本点"趋势  
  
第 4 步：综合评估（约 30 分钟）  
├─ 结合 RICE + Kano 结果  
├─ 考虑战略对齐度  
├─ 考虑技术依赖  
└─ 输出最终优先级建议  
  
第 5 步：评审会材料（约 30 分钟）  
├─ 生成 PPT  
├─ 准备决策依据  
└─ 输出：@交付物/优先级评审_2025Q2.pptx

**时间对比**：

- 传统方法：3 人 × 3 天 = 72 小时（数据收集 + 分析 + 会议准备）
    
- AI 工作流：1 人 × 2.5 小时 = 2.5 小时
    
- 效率提升：28 倍
    

---

## AI 优先级五模型

根据 Agile Seekers 和 Productboard 的调研，2026 年最常用的是这五个模型：

### 1. RICE 评分

|维度|说明|AI 如何辅助|
|---|---|---|
|Reach|覆盖用户数|基于用户画像预测|
|Impact|影响程度|参考历史类似功能|
|Confidence|置信度|标注数据来源|
|Effort|工作量|参考历史类似需求|

**适用场景**：资源有限，需要客观排序

---

### 2. WSJF（Weighted Shortest Job First）

|维度|说明|AI 如何辅助|
|---|---|---|
|用户价值|对用户的价值|用户反馈情感分析|
|时间紧迫性|延迟成本|市场竞争分析|
|风险降低|风险/机会|历史风险数据|
|工作量|实现成本|历史类似功能工时|

**适用场景**：SAFe 敏捷框架，强调交付速度

---

### 3. Kano 模型 + 情感分析

|需求类型|说明|AI 如何辅助|
|---|---|---|
|基本型|没有会不满|情感分析识别“理所当然”|
|期望型|越好越满意|情感强度打分|
|兴奋型|有了很惊喜|识别差异化机会|

**AI 独特价值**：持续监控，识别“兴奋点→基本点”的转变

---

### 4. 客户价值预测模型

这是 AI 独有的新模型：

  

AI 基于历史数据训练预测模型：  
- 输入：功能特征、用户画像、市场时机  
- 输出：预测的用户 Adoption 率、留存提升、收入 Impact

**适用场景**：有足够历史数据积累的团队

---

### 5. 组合对齐模型

  

评估功能与以下维度的对齐度：  
- 公司战略目标  
- 产品愿景  
- 季度 OKR  
- 技术路线图

**AI 辅助**：读取战略文档，自动评估对齐度并打分

---

## 真实案例：某医疗 SaaS 的优先级决策

### 背景

某医疗 SaaS 产品有 5 个功能候选，团队对优先级有分歧：

|功能|支持理由|
|---|---|
|telehealth 集成|客户需求强烈|
|用药提醒|市场趋势|
|健康数据可视化|竞品都有|
|医护团队消息|提升协作|
|预约自助 scheduling|减少客服成本|

### AI 分析过程

### **提示词**：

请对以下 5 个功能进行优先级评估：  
   
【功能列表 + 影响分】  
1. Telehealth 集成（患者满意度影响：8.5/10，法规复杂度：高）  
2. 用药提醒（患者满意度影响：7.8/10，法规复杂度：低）  
3. 健康数据可视化（患者满意度影响：7.2/10，法规复杂度：中）  
4. 医护团队消息（患者满意度影响：8.1/10，法规复杂度：中）  
5. 预约自助 scheduling（患者满意度影响：8.7/10，法规复杂度：中）  
  
【评估框架】  
基于"临床效果改善"和"实施可行性"两个维度  
  
【季度目标】  
- 提升患者参与度 25%  
- 满足最新 HIPAA 合规要求  
  
【资源约束】  
- 3 名工程师，2 个月时间

### AI 输出

|功能|RICE 评分|Kano 分类|综合推荐|
|---|---|---|---|
|预约自助 scheduling|8.7|期望型|⭐⭐⭐⭐⭐|
|Telehealth 集成|8.5|基本型|⭐⭐⭐⭐|
|医护团队消息|8.1|期望型|⭐⭐⭐⭐|
|用药提醒|7.8|兴奋型|⭐⭐⭐|
|健康数据可视化|7.2|期望型|⭐⭐|

**关键洞察**：

- “预约自助”虽然技术难度中，但患者满意度最高，优先
    
- “Telehealth 集成”是基本型需求（竞品都有），必须做但不用最先做
    
- “用药提醒”是兴奋点，可以差异化
    

---

## 避坑指南

### 坑 1：数据质量差

**问题**：

- “Garbage in, garbage out”
    
- 历史数据不准确，AI 预测失真
    

**解决**：

提示词中明确标注：  
- 哪些数据是实测的（标注来源）  
- 哪些是假设的（标注假设依据）  
- Confidence 分数要反映数据质量

### 坑 2：过度依赖 AI 评分

**问题**：

- 完全按 AI 评分决策
    
- 忽略战略意图和直觉判断
    

**解决**：

- AI 评分是“决策辅助”，不是“决策替代”
    
- 保留 PM 的战略判断空间
    
- 对 AI 评分异常的功能进行人工复核
    

### 坑 3：一次性使用

**问题**：

- 评审会开完就结束
    
- 不追踪实际上线后的 Impact
    

**解决**：

  

建立反馈闭环：  
功能上线 → 追踪实际 Impact → 与预测对比 → 校准 AI 模型

---

## 适用场景

|场景|推荐度|说明|
|---|---|---|
|季度优先级评审会|⭐⭐⭐⭐⭐|典型高价值场景|
|需求太多资源有限|⭐⭐⭐⭐⭐|最需要客观排序|
|团队对优先级有分歧|⭐⭐⭐⭐⭐|数据驱动减少争论|
|日常小需求|⭐⭐⭐|简单排序即可|
|战略级大功能|⭐⭐⭐|AI 辅助，人决策|

---

## 延伸阅读

- Anthropic 官方《knowledge-work-plugins GitHub 仓库》
    
- Productboard《Using AI for Roadmap Prioritization》
    
- Aha!《12 Product Prioritization Frameworks》
    
- VoltAgent《awesome-agent-skills》
    

---

**技能仓库**：

- rice-scoring： RICE 框架自动化（anthropics/knowledge-work-plugins）
- kano-analyzer： Kano 模型 + 情感分析（anthropics/knowledge-work-plugins）
- jira-integration： Jira 集成（sooperset/mcp-atlassian / atlassian/atlassian-mcp-server）

**安装时间**：20 分钟  
  
**学习时间**：1 小时  
  
**回报周期**：第一次优先级评审会即可省 3 天