视频地址：

https://www.bilibili.com/video/BV1AfsMzGEcb/

# 什么是 规范(Spec)驱动开发?

从 Kiro 的例子中，我们可以知道规范驱动开发的流程
 

# 为什么要用 Spec?

 
  

  

# 怎么用？OpenSpec

https://github.com/Fission-AI/OpenSpec

## 介绍

它为 AI 编程工具（Claude Code、Cursor、Codex、OpenCode、windsurf 等）提供一种标准化的方式：

- 让 AI **生成、跟踪、验证、归档** 功能变更；
    
- 把“功能需求 → 任务分解 → 实现 → 验收” 全流程结构化；
    
- 实现 **AI 与人协同开发** 的一致性。
    

🧠 核心理念：

> “让 AI 先写清楚规范（spec）再写代码” 而不是盲目凭 prompt 去写。

![](https://my.feishu.cn/space/api/box/stream/download/asynccode/?code=YzJhMzA5OTc1YzgwMmY4NTg5NzI2ZTdkNGI0MDY3M2ZfQXQ4cFFVZ3FHeVJUVWFpejEyTUZKNHM0RGpGWkJlNVNfVG9rZW46TDJtVmJ2VXQ2b2F4RTB4Y2pDemNTaFNWbk9kXzE3NzIxNzQ0MDk6MTc3MjE3ODAwOV9WNA)

  

|   |   |   |
|---|---|---|
|阶段|AI 行为|Spec 作用|
|规划阶段|讨论目标、提案|记录需求与验收标准|
|设计阶段|拆解任务|生成 tasks.md / design.md|
|实现阶段|写代码|AI 参考 spec 保持一致性|
|验收阶段|测试 / 回归|从 spec 提取验收条件|
|维护阶段|归档|形成“系统文档库”|

## 安装

**Prerequisites 先决条件**

Node.js >= 20.19.0

**步骤 1：全局安装 CLI**

`npm install -g @fission-ai/openspec@latest`

验证安装：

`openspec --version`

**步骤 2：在项目中初始化 OpenSpec**

导航到您的项目目录：

`cd my-project`

`openspec init`

  

初始化过程中会发生： 系统会让你选择所用的 AI 工具（Claude Code / Cursor / OpenCode / Codex）； 自动在项目中创建 openspec/ 目录； 生成托管文件 AGENTS.md，用于不同 AI 工具共享说明； 为所选 AI 工具自动配置 /openspec 的斜杠命令（slash commands）。

`my-project/` `├── openspec/` `│ ├── specs/ # 当前系统的真实规格（living specs）` `│ ├── changes/ # 所有进行中的变更提案` `│ ├── archive/ # 已完成并归档的变更` `│ └── AGENTS.md # AI 助手共用说明文件`

## 使用

- 主动方式
    

使用 AI 编程工具的 自定义命令，比如 cursor,windsurf, auggie 等

```Markdown
/openspec:proposal  创建需求
/openspec:apply  执行需求
/openspec:archive 归档需求
```

- 被动方式
    

使用关键词，比如 spec, proposal 等触发创建规格文件

## 实战

### Cursor 功能迭代实战

  

# 什么时候该使用Spec？

暂时无法在飞书文档外展示此内容