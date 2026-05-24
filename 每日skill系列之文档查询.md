# 每日skill系列之文档查询


最近这一年，我开始深入研究AI工程化，聚焦企业如何落地，在实践的过程中记录一些经验和感悟。

---

查文档、搜网络还是付费权威数据？Context7、Tavily Search 还是 Valyu？

这个问题决定了你的 AI 代理能获取什么样的信息。

---

## 三大信息获取方式概览

### Context7：免费官方文档查询

Context7 提供 RAG（检索增强生成）服务，专门用于获取库和框架的官方文档。

==**核心特点：**==

- 免费 MCP 服务器
- 主要从公共 GitHub 仓库获取文档
- 实时拉取正确上下文，确保文档与实际实现一致
- 零网络延迟，离线可用

Medium 技术博客评价：“Context7 可以在正确的时间自动拉取正确的上下文——检索相关代码、注释和示例——使生成的文档与实际实现保持一致。”

==**安装命令：**==

# 通过 npx skills 安装  
npx skills add context7-docs-lookup  
   
# 或使用插件市场  
/plugin install context7-docs-lookup

**适用场景：** 查询库文档、API 参考、框架使用示例。

---

### Tavily Search：实时网络搜索

Tavily 是专为 AI 代理设计的搜索引擎，提供快速、干净、结构化的搜索结果。

==**核心特点：**==

- AI 优化搜索，专为代理和自动化工作流设计
- 支持普通搜索、深度搜索、新闻搜索
- 提取功能：从 URL 提取内容，返回干净的文章
- 地图功能：发现网站所有 URL
- 爬取功能：深度爬取整个网站

Tavily 官方博客宣称：“Tavily 的 Fast 和 Ultra-Fast 搜索深度提供亚秒级结果，不牺牲相关性，以更少的 Token 最大化信息密度。”

基准测试显示：

- 延迟：669ms（最快）
- Agent Score: 13.67
- 免费 tier： 每月 1,000 次查询

==**安装命令：**==
 
# 获取 Tavily API Key: tavily.com  
# 安装技能  
npx skills add tavily-ai/skills --all  
   
# 或单独安装搜索技能  
npx skills add tavily-ai/skills --skill tavily-search
  

==**使用示例：**==

# 基础搜索  
tvly search "your query" --json  
   
# 深度搜索（更多结果）  
tvly search "quantum computing" --depth advanced --max-results 10 --json  
   
# 新闻搜索  
tvly search "AI news" --time-range week --topic news --json  
   
# 提取 URL 内容  
tvly extract "https://example.com" --json  
   
# 爬取整个网站  
tvly crawl "https://docs.example.com" --output-dir ./docs/

**适用场景：** 实时信息搜索、新闻查询、网页内容提取、网站爬取。

---

### Valyu：付费权威数据源

Valyu 提供权威、付费的数据访问，包括 SEC 文件、研究论文、临床数据、经济指标等。

==**核心特点：**==

- 访问付费墙后的权威数据
- SEC 文件、研究论文、临床数据、经济指标
- 专为需要可信数据的应用设计
- 付费服务，免费试用

一篇基准测试文章总结：“Valyu 在 5 个领域的搜索 API 测试中脱颖而出。单一最大的答案质量决定因素不是模型，而是模型能读到什么。”

==**安装命令：**==

# 注册 Valyu 账号：platform.valyu.ai  
# 安装技能  
npx skills add valyu-search
 
**适用场景：** 金融分析、学术研究、医疗数据、经济研究、需要权威数据源。

---

## 核心对比维度

|维度|Context7|Tavily Search|Valyu|
|---|---|---|---|
|数据来源|GitHub 公共仓库|实时网络|付费权威数据源|
|新鲜度|取决于仓库更新|实时|取决于数据源更新|
|Token 效率|高（只返回相关部分）|中（返回摘要）|高（精选数据）|
|离线支持|支持|不支持|不支持|
|成本|免费|免费 tier 1,000次/月|付费（有免费试用）|
|搜索延迟|极低（本地）|669ms-998ms|未知|
|数据覆盖|开源库文档|全网|专业数据源|

---

## 我的建议

**选择 Context7 如果你：**

- 查询开源库的官方文档
- 需要离线访问
- 想要免费方案
- 关注延迟和 Token 效率

**选择 Tavily Search 如果你：**

- 需要实时网络信息
- 搜索新闻、博客、文章
- 需要爬取网站
- 想要结构化搜索结果
- 每月查询量不超过 1,000 次

**选择 Valyu 如果你：**

- 需要权威数据源
- 金融分析、学术研究、医疗数据
- 数据质量要求极高
- 愿意为付费数据源买单

**组合使用建议：**

- 最佳实践： Context7（库文档）+ Tavily（实时搜索）+ Valyu（权威数据）
- 日常开发： Context7 + Tavily 足够
- 专业研究： 必须加 Valyu

一篇技术博客总结得很好：“文档用 Context7，实时信息用 Tavily，权威数据用 Valyu。”

---

## 快速开始

**Context7:**

npx skills add context7-docs-lookup  
   
# 使用  
# Agent 会自动在需要时调用

**Tavily Search:**

# 1. 获取 API Key: https://tavily.com  
# 2. 设置环境变量  
export TAVILY_API_KEY="tvly-your-api-key"  
   
# 3. 安装技能  
npx skills add tavily-ai/skills --all

**Valyu:**

# 1. 注册账号: https://platform.valyu.ai  
# 2. 获取 API Key  
# 3. 安装技能  
npx skills add valyu-search

---

## 实战案例

**场景 1：查询 React 文档**

# 用 Context7  
# Agent 自动拉取 React 官方文档  
# 返回最新 API 和示例

**场景 2：搜索最新 AI 新闻**

# 用 Tavily  
tvly search "AI agent news" --topic news --days 7 --json  
   
# 返回最近 7 天的 AI 新闻

**场景 3：分析公司财务数据**

# 用 Valyu  
# 访问 SEC 文件  
# 获取权威财务报告

---

**感谢你的阅读。**

如果这篇文章对你有帮助，欢迎点赞支持、分享给朋友、在评论区分享你的想法。

期待和你的交流！