

为了让大模型具备使用工具、查阅上下文和逻辑推理的能力，拿langchain举例，LangChain 在底层大量使用了诸如 **ReAct (Reason + Act)** 这样的代理（Agent）框架。这意味着它会在你不知情的情况下，往原本简单的对话中塞入极其冗长的**系统提示词（System Prompts）**、**工具描述说明（Tool Descriptions）**，并且在每一次执行工具前后，都会带着累积的完整历史记录，反复向大模型发起多轮请求。

通过一个最简单的代理执行流程，拆解“明天上海的天气？”这个问题是如何榨干 Token 的。

### “明天上海的天气？” Token 消耗详尽拆解

假设你使用 LangChain 构建了一个带有一个“天气查询 API”工具的 Agent。大模型使用的是标准的 Token 计算逻辑（例如 OpenAI 的 `tiktoken` 算法）。

**步骤 1：LangChain 后台偷偷拼接的初始请求（第 1 次调用大模型）**你只输入了 10 个 Token，但 LangChain 发给大模型的真实输入如下：

> **[System Prompt]** 你是一个有用的AI助手。你可以使用以下工具：
> WeatherAPI: 用于查询天气，输入应该是具体的城市名和时间。
> 你必须遵循以下格式回答： Thought: 你需要思考下一步做什么 Action: 你需要使用的工具名称，必须是 [WeatherAPI] 中的一个 Action Input: 工具的输入参数 Observation: 工具返回的结果 ... (此处省略 LangChain 默认的上百字格式说明) ...**[User Input]** 明天上海的天气？

- **输入消耗**：默认模板 + 工具说明约 **200 Token**，用户问题 **10 Token**。
- **输出消耗**：大模型开始按要求思考并输出：“Thought: 我需要查上海明天的天气。Action: WeatherAPI。Action Input: 上海, 明天”。约 **30 Token**。
- **本轮累计**：~240 Token。

**步骤 2：LangChain 本地执行工具（不消耗大模型 Token）**LangChain 截获了 `Action`，在本地运行代码去调用天气 API，得到结果返回：“Observation: 明天上海，15°C~20°C，小雨。”

**步骤 3：带着所有历史记录再次请求（第 2 次调用大模型）**因为大模型是无状态的，LangChain 必须把前面所有的废话和新获取的信息**全部打包**再发一次。

> **[System Prompt + 工具说明]** (约 200 Token)**[User Input]** 明天上海的天气？ (10 Token)**[History]** Thought: 我需要查... Action Input: 上海, 明天 (30 Token)**[Observation]** 明天上海，15°C~20°C，小雨。 (20 Token)

- **输入消耗**：200 + 10 + 30 + 20 = **260 Token**。
- **输出消耗**：大模型觉得信息足够了，输出最终答案：“Thought: 我现在知道天气了。Final Answer: 明天上海的天气是15°C到20°C，有小雨。”约 **30 Token**。
- **本轮累计**：~290 Token。

**两轮累计**：~530 Token。

**对比结论：**

- **不使用 LangChain（直接调用大模型，人类手动查天气后喂给它）**：输入 30 Token + 输出 20 Token = **50 Token**。
- **使用 LangChain Agent**：第1次调用 (210入 + 30出) + 第2次调用 (260入 + 30出) = **530 Token**。
- **损耗率**：在这个简单的单步工具调用中，Token 消耗放大了 **10 倍以上**。如果涉及多步推理或报错重试（例如模型第一次调用工具参数写错了），消耗会呈指数级爆炸。