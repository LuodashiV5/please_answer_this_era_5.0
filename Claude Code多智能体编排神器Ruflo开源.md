智猩猩AI整理

编辑：没方

  

在AI辅助开发时代，**“单兵作战”**根本扛不住软件工程的真实战场。

  

一个动辄上万行代码的企业级应用，Claude Code 却经常“**失忆**”，上下文稍长就丢掉关键信息，前后逻辑断裂。

  

明明只是格式调整、文档整理这种简单任务，却持续调用昂贵的 **Opus** 模型，造成惊人的 token 成本浪费。

  

更要命的是，多环节开发任务需要人工反复切换模型角色，每切换一次就得重新解释上下文、调整提示词，思路被打断，效率直接腰斩。

  

而今天要给大家介绍的开源项目 **Ruflo（前名 Claude Flow） 正精准破解这些难题。Ruflo** ****是一个面向 Claude Code 的多智能体编排框架**，让单打独斗的大模型变成分工协作的智能体团队。能从每一次任务执行中自主学习，留存成功的执行模式，避免灾难性遗忘问题。还能将任务智能分配至各领域相应智能体处理，API 调用成本可降低高达 75%，Claude 能力上限提升 2.5 倍。 该项目在Github上已收获 21.6k Stars。**

![图片](https://mmbiz.qpic.cn/mmbiz_png/zJVQUll3YIZBU95xezl6kSWn4qwialP4r5Owiabnp1yebSMJEtg1Xc7NRSExzguukI8JgWVCa4biaaiaKb1efyuSeIzeAMnEqtGd3IGyaljIL8M/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1#imgIndex=1)

  

- 项目链接：  
    
    https://github.com/ruvnet/ruflo
    
      
    

_**01**_

**项目介绍  
**

  

Ruflo 是专为 Claude Code 量身打造的原生多智能体编排框架，通过蜂群式（Hive Mind）架构协调多个智能体完成软件开发任务。采用 Queen-Worker 层级调度，并支持多种共识算法自动解决多智能体协作中的冲突。

  

Ruflo 内置 **60+专业智能体**，覆盖编码、测试、安全审计、架构设计、文档撰写等全开发链路，像真实团队一样各司其职。此外，Ruflo 通过 **MCP 协议**无缝接入 Claude Code，用户不用离开聊天界面，就能直接召唤蜂群（多个专业智能体组成）、初始化任务、检索记忆，整个开发流程非常丝滑，还能一键解锁 170+ 专业工具。

  

****针对大模型的 “失忆” 问题，Ruflo 内置 RuVector 持久化向量记忆库，基于 PostgreSQL 与 HNSW 算法实现高速向量搜索。还结合了自进化神经架构 SONA 与 EWC++ 防遗忘技术，****Ruflo**** 会自动提炼并存储任务经验，在后续相似需求中实现记忆复用，持续提升协作效果。****

  

性能方面，Ruflo 设计了三级智能路由降低 API 成本。简单格式调整等任务用 WASM 本地秒处理；中等任务交给轻量模型；只有真正复杂的部分才调用 Opus 等高端模型。

  

_**02**_

**使用**

  

环境要求：Node.js 20+（必需）npm 9+ /pnpm/bun（ 包管理器）。

  

（1）安装 Claude Code

  

```
# 1. 全局安装 Claude Code
```

  

（2）npm/npx 安装：

  

```
# 快速启动（无需提前安装）
```

  

（3）基本使用

  

```
# 初始化项目
```

  

（4）升级

  

```
# 更新 helpers 和 statusline（保留你的数据）
```

  

（5）Claude Code MCP 集成

  

```
# 将 ruflo 添加为 Claude Code 的 MCP 服务器
```

  

添加完成后，Claude Code 可直接使用 Ruflo 的全部 175+ MCP 工具，例如：

  

- swarm_init - 初始化智能体蜂群
    
- agent_spawn - 生成专业智能体
    
- memory_search - 使用 HNSW 向量搜索模式
    
- hooks_route - 智能任务路由
    
- 以及 170+ 其他工具
    

  

_**03**_

**群体智能的涌现  
**

  

Ruflo正在解锁的，正是多智能体系统的核心价值——群体智能的涌现。这不仅是其区别于其他AI开发工具的核心竞争力，更精准契合了2026年“从单点应用到群体协同”的产业趋势。单个智能体的能力终究受限于模型边界，但多智能体群体通过分工协作、经验复用、冲突自解，正在突破这一物理极限。