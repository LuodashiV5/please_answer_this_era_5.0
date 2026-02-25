
开发一个集成 **Skill（技能）** 与 **MCP（Model Context Protocol，模型上下文协议）** 的 AI Agent，本质上是构建一个既有“专业领域知识/工作流（Skill）”又具备“标准化外部连接能力（MCP）”的智能系统。

## 1. 核心概念区分
在动手之前，需要理清两者的角色：
- **MCP (连接层)**：像 USB 接口。它规定了 Agent 如何发现、读取和调用外部工具或数据（如读取 GitHub、操作数据库、查询天气）。
- **Skill (逻辑层)**：像应用程序。它是一组经过编排的指令，告诉 Agent 在特定场景下如何组合使用这些工具来完成一个复杂的任务（如“写一份周报”或“进行代码审查”）。
---

## 2. 准备工具链

### 开发语言与 SDK
- **Python / TypeScript**: 官方 MCP SDK 支持最好的两种语言。
- **uv (Python 包管理)**: 推荐用于快速创建 MCP 环境（`uv init`, `uv add mcp`）。

### MCP 服务器工具
- **Claude Desktop**: 最常用的本地测试宿主。
- **MCP Inspector**: 用于调试 MCP Server 的命令行工具（`npx @modelcontextprotocol/inspector`）。

### 框架支持
- **LangChain / LangGraph**: 提供了 MCP 适配器，方便将 MCP 工具集成到长程对话中。
- **Anthropic SDK**: 如果你开发基于 Claude 的 Agent，这是首选。
---

## 3. 开发流程步奏

### 第一步：构建或集成 MCP Server
你需要让 Agent “手长”一点，能触达外部数据。
1. **使用现成的 MCP**: 从MCP 仓库直接下载（如 Google Drive, Slack）。
2. **自定义 MCP**:
- 定义 **Resources** (静态数据，如本地文档)。
- 定义 **Tools** (执行动作，如调用 API)。
- 定义 **Prompts** (预设模版)。

### 第二步：编写 Skill（领域逻辑）
Skill 是为了降低 Token 消耗并提高准确度，采用“渐进式披露”原则。
1. **编写 `SKILL.md`**: 这是一个说明文件，告诉模型这个技能是干什么的、什么时候用。
2. **定义 Workflows**: 在 Skill 文件夹中定义具体步骤。例如：
- _步骤1_: 使用 MCP 查询最近的 GitHub PR。
- _步骤2_: 使用本地 Skill 逻辑对 PR 内容进行分类。
- _步骤3_: 格式化输出。

### 第三步：集成与编排（Agent 层）
在你的 Agent 主程序中将两者结合：

```python
# 伪代码示例：使用 MCP 客户端连接服务并应用 Skill
from mcp import Client, StdioServerParameters
# 1. 连接 MCP 服务器
params = StdioServerParameters(command="python", args=["my_mcp_server.py"])
async with Client(params) as client:
# 2. 获取可用工具
tools = await client.list_tools()

# 3. 在 Prompt 中注入 Skill 逻辑
system_prompt = "你是一个代码专家。请按照 [Skill:代码审查] 的流程，调用 [MCP:GitHub] 工具执行任务。"

# 4. 执行 Agent 循环
# ...
```
---
## 4. 最佳实践建议

| **环节**       | **建议**                                                            |
| ------------ | ----------------------------------------------------------------- |
| **Token 优化** | 不要一次性把所有 MCP 工具定义塞给模型。使用 Skill 的“描述信息”让模型按需加载工具。                  |
| **安全性**      | MCP 服务器运行在本地或受控环境，调用涉及写操作的工具（如删除文件）时，务必加入人工确认（Human-in-the-loop）。 |
| **调试**       | 优先使用 `mcp-inspector` 确保 Server 端接口定义正确，再集成到 Agent 业务逻辑中。          |
