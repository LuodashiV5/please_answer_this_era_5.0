
_此处声明，本文９０％的部分是由__openclaw__大龙虾写的。实话说，你看到的全网关于教你怎么用__openclaw__都是大龙虾自己写的。为什么呢？因为如果你发现大龙虾可以自己写，你很难还愿意在键盘上一个字一个字的去敲了。但是，为了保证质量，这个文章的大纲是我本人写的，对大龙虾写的东西也做了一定修改。文中代码都是自动生成，其实不需要用户写代码。_

## **一、创建属于自己的 Skill：让 AI 真正为你所用**

### **1.1 什么是 Skill？为什么需要自定义？**

在 OpenClaw 的世界里，** Skill（技能）** 是赋予 Agent 特定能力的模块化组件。官方把 Skill 定义为"可复用的功能单元"——就像乐高积木，你可以把各种能力拼装起来，打造出独一无二的 AI 助手。

但为什么需要自定义 Skill 呢？

想象一下：你有一个专门研究股票的 Agent，它需要每天监控特定公司的新闻、分析财报、发送邮件报告。这些功能组合在一起，就是一个完整的 Skill。如果不封装成 Skill，每次都要手动执行一系列操作，既繁琐又容易出错。

**注意：所有代码和脚本都可以让****openclaw****自己生成！！！**

**自定义 Skill 的核心价值：**

·**复用性**：写一次，到处用

·**标准化**：团队协作时，大家用同一套规范

·**可维护性**：功能模块化，更新迭代更方便

·**可分享**：可以发布到 ClawHub，让社区受益

### **1.2 Skill 的标准结构**

一个标准的 OpenClaw Skill 包含以下文件结构：

my-skill/  
├── SKILL.md            # 技能说明文档（必须）  
├── README.md       # 详细介绍  
├── scripts/               # 脚本文件  
│        ├── main.py  
│        └── helper.sh  
├── config/               # 配置文件  
│        └── settings.json  
└── assets/               # 静态资源  
        └── logo.png

**最核心的文件是 `SKILL.md`**，它决定了 OpenClaw 如何识别和使用这个 Skill。一个标准的 SKILL.md 包含：

\--- 
name: my-awesome-skill  
description: "这是一个示例技能，用于演示如何创建自定义 Skill"  
---  
  
# My Awesome Skill  
  
## 触发条件  
  
当用户说以下类似内容时，触发此 Skill：  
- "帮我做 XXX"  
- "执行 XXX 任务"  
  
## 使用方式  
  
### 步骤 1：准备工作  
...  
  
### 步骤 2：执行操作  
...  
  
### 步骤 3：输出结果  
...  
  
## 注意事项  
- 注意点 1  
- 注意点 2

### **1.3 实战：创建一个股票监控 Skill**

让我们通过一个实际案例，看看如何从零创建一个 Skill。

**需求**：创建一个监控特斯拉股票新闻的 Skill，每小时检查一次，发现重大新闻时发送飞书通知。

**创建步骤：**

**Step 1：创建目录结构**

mkdir -p ~/.openclaw/skills/tesla-monitor/scripts

**Step 2：编写 SKILL.md**

\---  
name: tesla-stock-monitor  
description: "监控特斯拉股票相关新闻，发现重大动态时发送通知"  
---  
  
# 特斯拉股票监控  
  
## 触发条件  
  
当用户说以下类似内容时，触发此 Skill：  
- "帮我监控特斯拉股票"  
- "设置特斯拉新闻提醒"  
- "TSLA 监控"  
  
## 使用方式  
  
### 步骤 1：配置监控参数  
编辑 `config/watchlist.json`，设置监控关键词和通知方式。  
  
### 步骤 2：启动监控  
运行监控脚本：

./scripts/monitor.sh

  
### 步骤 3：查看结果  
监控结果会发送到配置的飞书账号。  
  
## 注意事项  
- 需要配置 Tavily API Key 用于搜索  
- 需要配置飞书 Webhook 用于通知  
- 建议设置合理的监控频率，避免频繁打扰

**Step 3：编写核心脚本**

#!/bin/bash  
# scripts/monitor.sh  
  
API_KEY="your-tavily-api-key"  
WEBHOOK="your-feishu-webhook"  
  
# 搜索最新新闻  
RESULT=$(curl -s "https://api.tavily.com/search" \  
  -H "Content-Type: application/json" \  
  -d '{  
    "api_key": "'$API_KEY'",  
    "query": "Tesla TSLA news today",  
    "search_depth": "advanced",  
    "max_results": 5  
  }')  
  
# 解析并发送通知  
echo "$RESULT" | python3 scripts/parse_and_notify.py "$WEBHOOK"

**Step 4：安装并启用 Skill**

openclaw skills install ~/.openclaw/skills/tesla-monitor  
openclaw skills enable tesla-stock-monitor

## **二、多 Agent 协作：打造你的 AI 梦之队**

### **2.1 从单兵作战到团队协作**

想象一下，你有一个复杂的任务：研究一家上市公司并生成投资报告。这个任务涉及：

·收集公司基本信息

·分析财务报表

·研究行业竞争格局

·撰写报告

·发送邮件

如果让一个 Agent 完成所有工作，可能会遇到：

·**上下文溢出**：信息太多，超出处理极限

·**专业度不够**：一个 Agent 难以精通所有领域

·**效率低下**：串行执行，耗时过长

**多 Agent 协作的核心思想**：让专业的人做专业的事。每个 Agent 负责一个子任务，通过协作完成复杂目标。

### **2.2 OpenClaw 的多 Agent 架构**

OpenClaw 支持两种多 Agent 模式：

**模式 1：主从模式（Master-Worker）**

主 Agent (Main)  
    ├── 研究 Agent (Researcher)  
    ├── 写作 Agent (Writer)  
    └── 发送 Agent (Sender)

主 Agent 负责任务分解和协调，子 Agent 负责具体执行。

**模式 2：流水线模式（Pipeline）**

数据收集 Agent → 分析 Agent → 写作 Agent → 审核 Agent

每个 Agent 的输出是下一个 Agent 的输入，形成流水线。

### **2.3 实战：构建研究报告生成团队**

让我们构建一个三 Agent 协作系统，自动生成公司研究报告。

**Agent 1：研究员（Researcher）**

# researcher-agent.yaml  
name: researcher  
description: "专门负责收集公司信息和行业数据"  
system_prompt: |  
  你是一个专业的行业研究员。你的任务是：  
  1. 搜索目标公司的基本信息（成立时间、主营业务、财务数据）  
  2. 识别主要竞争对手  
  3. 收集行业报告和市场数据  
    
  输出格式必须是结构化的 JSON，包含：  
  - company_profile: 公司概况  
  - competitors: 竞争对手列表  
  - industry_data: 行业数据  
  - sources: 参考来源

**Agent 2：分析师（Analyst）**

# analyst-agent.yaml  
name: analyst  
description: "负责分析数据并撰写报告"  
system_prompt: |  
  你是一个资深投资分析师。基于研究员提供的数据，你需要：  
  1. 分析公司的竞争优势和劣势  
  2. 评估行业前景  
  3. 对比竞争对手的表现  
  4. 撰写专业的投资分析报告  
    
  报告必须包含：执行摘要、公司概况、行业分析、竞争格局、投资建议。

**Agent 3：发送员（Sender）**

# sender-agent.yaml  
name: sender  
description: "负责发送报告"  
system_prompt: |  
  你是一个报告发送专员。你的任务是：  
  1. 将分析师撰写的报告转换为 Word 格式  
  2. 通过邮件发送给指定收件人  
  3. 确认发送成功并记录日志

**主 Agent 协调代码：**

# main_coordinator.py  
import json  
from openclaw import Agent, Session  
  
class ResearchTeam:  
    def __init__(self):  
        self.researcher = Agent("researcher")  
        self.analyst = Agent("analyst")  
        self.sender = Agent("sender")  
  
    def generate_report(self, company_name, email_to):  
        # Step 1: 研究员收集数据  
        print(f"[1/3] 研究员正在收集 {company_name} 的数据...")  
        research_data = self.researcher.execute(  
            f"请研究 {company_name} 的公司信息和行业数据"  
        )  
  
        # Step 2: 分析师撰写报告  
        print("[2/3] 分析师正在撰写报告...")  
        report = self.analyst.execute(  
            f"基于以下数据撰写投资分析报告：\n{research_data}"  
        )  
  
        # Step 3: 发送员发送报告  
        print("[3/3] 发送员正在发送报告...")  
        result = self.sender.execute(  
            f"将以下报告发送到 {email_to}：\n{report}"  
        )  
  
        return result  
  
# 使用示例  
team = ResearchTeam()  
team.generate_report("宁德时代", "yliu@cathay-capital.com")


## **三、长期记忆系统：让 AI 真正"记住"你**

### **3.1 为什么长期记忆如此重要？**

想象一下，你和一个朋友聊天，每次见面他都不记得你是谁、你们上次聊了什么。这样的对话体验会很糟糕，对吧？

传统的 AI 助手就面临这个问题：

·**会话隔离**：每次对话都是全新的开始

·**上下文有限**：只能记住最近的几十轮对话

·**无法学习**：不会根据你的偏好和历史行为调整

**长期记忆（Long-Term Memory, LTM）** 解决了这些问题。它让 AI 能够：

·记住你的个人信息和偏好

·回顾之前的对话历史

·基于过往经验做出更好的决策

·在不同会话之间保持连续性

### **3.2 长期记忆的三种类型**

根据 Memory 的分类，长期记忆分为三种：

**1. 语义记忆（Semantic Memory）**

存储事实性知识：

·用户的姓名、职业、兴趣

·重要的日期和事件

·偏好设置（如"我喜欢简洁的回答"）

**2. 情景记忆（Episodic Memory）**

存储具体交互经历：

·之前的对话内容

·成功或失败的任务记录

·用户的反馈和评价

**3. 程序记忆（Procedural Memory）**

存储"如何做"的知识：

·用户喜欢的工作流程

·常用的工具和格式

·个性化的处理方式

### **3.3 OpenClaw 中的长期记忆实现**

OpenClaw 提供了多种长期记忆方案：

**方案 1：文件存储（MEMORY.md）**

最简单的方式，使用 Markdown 文件存储关键信息：

# MEMORY.md - 长期记忆  
  
## 用户基本信息  
- **姓名**: Ryan  
- **关注领域**: 比特币、股票投资、新能源汽车  
- **邮箱**: yliu@cathay-capital.com  
  
## 重要任务  
- 新能源汽车供应链研究（29家公司，已完成10家）  
- 比特币交易模拟（当前持仓11.56 BTC）  
  
## 偏好设置  
- 研究报告需要详细引用来源  
- 喜欢使用表格展示数据  
- 需要邮件提醒功能

**方案２：自建建立长期记忆系统**

比如我建立了一个 daily-chat-archive CRON 任务，它是这样存储的：

当前方法：

每天 21:00 执行

读取 /root/.openclaw/workspace/memory/YYYY-MM-DD.md 文件

使用 longterm-memory skill 的 store_memory 函数存储

存储类型：conversations

标签：["daily_archive", "YYYY-MM-DD", "cron_job"]

重要性：6

任务执行流程：

检查 memory/2026-xx-xx.md 是否存在

检查 AGENTS.md 是否有更新

检查 MEMORY.md 是否有更新

使用 longterm-memory skill 存储到向量数据库

存储方式：

文件格式：Markdown（便于阅读）

索引格式：JSON（快速检索）

存储位置：memory/ 目录下的分类文件夹

归档策略：90天+的旧记忆自动压缩归档

         

## **四、复杂任务的多步执行与上下文管理**

### **4.1 上下文溢出的挑战**

大型语言模型（LLM）都有上下文窗口限制：

在复杂任务中很容易溢出：

·一份详细的财报：20K tokens

·10 篇行业报告：50K tokens

·多轮对话历史：30K tokens

·系统提示和工具描述：20K tokens

**总计：120K tokens，已经接近上限！**

### **4.2 上下文管理的五大策略**

**策略 1：智能分块（Smart Chunking）**

不要把所有内容一次性塞给 LLM，而是分块处理。

**策略 2：动态剪枝（Dynamic Pruning）**

**策略 3：语义缓存（Semantic Caching）**

**策略 4：分层摘要（Hierarchical Summarization）**

**说实话，我目前只会第一种，但找到一个巧妙的办法进行分解任务。这个就是定义任务的WORKFLOW，每次做完一个任务启动新的AGENT做第二步。但还有时候会溢出。**

**OpenClaw****能做的事情很多，现在才是刚开始。但会用和不会用的确差别很大。**

**我现在最大的问题，就是记忆问题。因为这个大龙虾太容易忘记事情了，尤其是比较久以前的事情。但我觉得这个问题，还是有些笨办法可以解决，至少不影响我现在的工作。**


OpenClaw的长期记忆分语义、情景、程序三种，还要分别配置存储方式，Molili是不是把这些记忆能力整合好了，一键开启？