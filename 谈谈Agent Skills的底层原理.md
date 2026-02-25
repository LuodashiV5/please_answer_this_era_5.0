
自去年 10 月推出以来，Agent Skills[1]迅速成为 Claude Agent 产品的核心组件，并在社区中得到了广泛的采纳和应用。关于 Skills 的介绍，网上已经有很多文章。本文尝试从底层原理的角度，探讨 Agent Skills 是如何工作的。

按照官方定义，Skills 是一种基于文件系统的资源，用于为 Agent 提供特定领域的专业知识，从而将其转变为专家。理解 Skills 的前提，在于先厘清 Agent 的基本原理。

## ReAct Agent

现代 Agent 都是基于ReAct[2]模式构建的。ReAct 的核心思想是将大语言模型的推理能力（Reasoning）与行动执行（Acting）相结合，使 Agent 能够反复思考问题、使用工具，并根据观察结果采取行动，从而实现用户目标。

![ReAct模式](https://mmbiz.qpic.cn/mmbiz_png/t55kqTnrJg1wY8IvfsQ2aaamPAMQm1PNbE04F8quhT893c320wicia6Xlfl2DVAAspG3buqmjrmia6pSmjhIEFiaow/640?wx_fmt=png&from=appmsg)

早期采用 ReAct 模式的 Agent，效果并不理想。随着 LLM 能力的持续演进，特别是函数调用（Function Calling[3]）的引入，ReAct 模式的效果得到了显著改善，使得 Agent 能够更可靠且高效地完成任务。

以天气查询为例，从上下文（Context）的角度来看，ReAct Agent 的运行过程大致如下：

```json
System: You are a helpful weather assistant.
User: What is the weather like in Chengdu?
Assistant: ToolCall(name="get_weather", args={"location": "Chengdu"})
User: ToolOutput(result={"weather": "Sunny", "temperature": "22°C"})
Assistant: The weather in Chengdu is Sunny with a temperature of 22°C.
```

## Claude Code

作为一个现代 Agent 系统，Claude Code 也遵循了 ReAct 模式。我们在揭秘 Claude Code：自主式编程[4]（[揭秘Claude Code：自主式编程](https://mp.weixin.qq.com/s?__biz=Mzg3NjgxNjY3MA==&mid=2247483777&idx=1&sn=67adc2fc2dd673fc8cbf79fa9e5b7934&token=348060578&lang=zh_CN&scene=21#wechat_redirect)）中介绍过它的核心架构：

![Claude Code自主式编程架构](https://mmbiz.qpic.cn/mmbiz_png/t55kqTnrJg1wY8IvfsQ2aaamPAMQm1PNicddYNl4WCnrA7hqGQSzfmFibibkH2hrXAzGyqf9cmccNsrQsfqaWicmzA/640?wx_fmt=png&from=appmsg)

Claude Code自主式编程架构

可以看出，Claude Code 与常规 Agent（如天气查询助手）最大的不同之处在于：它工作在操作系统之上，几乎所有的工具都是围绕**文件系统和 Shell 命令**展开的！

以“查看文件并创建一个 Hello World 函数”为例，Claude Code 运行过程中的 Context 大致如下：

```json
System: You are Claude Code, Anthropic's official CLI for Claude.  
User: What files are there?  
Assistant: ToolCall(name="Bash", args={"command": "ls"})  
User: ToolOutput(result="[README.md]")  
Assistant: There is only one file named README.md.  
User: Create a hello world function in Python.  
Assistant: ToolCall(name="Write", args={"file_path": "hello_world.py", "content": "def hello_world():\n    print('Hello, World!')\n\nif __name__ == '__main__':\n    hello_world()"})  
User: ToolOutput(result="Created `hello_world.py` with a simple hello world function.")  
Assistant: I've created a simple Python file with a "Hello, World!" function.
```

## 上下文管理

有了对 ReAct Agent 和 Claude Code 的基本认识，我们再来讨论一个关键话题——上下文管理。

了解大语言模型的读者可能知道，LLM 的上下文有两个重要特征：

1. **上下文窗口大小限制**：LLM 的上下文窗口大小是有限的（早期 GPT 3 仅有 2048 个 token），虽然这个大小在持续增长（比如最新 Claude Sonnet 4.5 已支持百万 token），但仍然是有上限的。
    
2. **上下文过载导致性能下降**：即使最先进的 LLM 支持长上下文（如百万 token），但如果上下文内容过多，其性能也会显著下降。除了经典的Lost in the Middle[5]，还会出现上下文污染（Context Poisoning）、上下文混淆（Context Confusion）等各种问题。感兴趣的读者可以进一步参考How Long Contexts Fail[6]。
    

因此，如何有效地管理上下文，成为了 Agent 设计中的一个重要课题。常见的上下文管理策略包括检索增强（RAG）、上下文总结（Context Summarization）、上下文隔离（Context Quarantine）和上下文卸载（Context Offloading）等。本文的讨论重点关注 Context Offloading。

关于 Context Offloading，How to Fix Your Context[7]一文给出了以下定义：

> 上下文卸载（Context Offloading）是指将信息存到 LLM 的上下文之外，通常借助能管理数据的工具来实现。

而该文引用的 Anthropic 原文The think tool[8]中，则这样指出：

> 这个“think”工具特别适合用在那些仅凭用户提问、Claude 信息不够没法直接回答的情况，还有那些需要处理外部信息（比如工具返回的结果）的场景。比起深度思考那种全面推演，Claude 用“think”工具做的推理会更聚焦，主要围绕模型刚发现的新信息展开。

## 在 Claude Code 中实现 Context Offloading

上述关于 Context Offloading 的描述稍显抽象。为了便于理解，我们来设想一个问题：如果要让 Claude Code 支持 Context Offloading，应该如何实现？

结合前文的讨论，我们知道 Claude Code 的能力基本建立在文件系统和 Shell 命令之上。因此，要实现 Context Offloading，从第一性原理出发，我们能够很自然地想到以下方案：

1. 将信息存储在文件系统，而不是一开始就全部放到 LLM 的上下文中；
    
2. 为了让 LLM 知道这些信息的存在，需要在 LLM 的上下文中（通常在系统提示词中）记录这些信息的位置；
    
3. 用户提问信息不全时，Agent 可以根据系统提示词的引导，按需从文件系统寻找信息；
    
4. 寻找信息的过程，需要借助文件读写和 Shell 命令等工具；
    
5. 将获取到的外部信息加载到 LLM 的上下文中，以辅助完成用户的目标。
    

以“生成博客前端页面”为例，为了不把所有的设计指南都塞到 LLM 的上下文中，可以将其保存到`resources/frontend-design.md`文件中：

```json
# Frontend Aesthetics Guidelines
Focus on:
**Typography**: Choose fonts that are beautiful, unique, and interesting...
**Color & Theme**: Commit to a cohesive aesthetic......
```

同时，在 Agent 的系统提示词中添加对这个文件的引用：

```json
You are Claude Code...
You have access to the following resources:
- `resources/frontend-design.md`: Guidelines for designing the frontend UI
....
```

如此一来，Agent 只会在用户请求生成前端页面时，才会去额外读取`resources/frontend-design.md`文件中的内容，从而避免了不必要的上下文膨胀。具体来说，运行过程中的 Context 可能会是这样的：

```json
System: You are Claude Code...\n\nYou have access to the following resources:\n
-`resources/frontend-design.md`: Guidelines for designing the frontend 
UI.User: Generate a blog frontend UI.
Assistant: ToolCall(name="Read", args={"file_path": "resources/fontend-design.md"})
User: ToolOutput(result="Choose fonts that are beautiful, unique, and interesting...")
Assistant: ToolCall(name="Write", args={"file_path": "app/index.html", "content": "<html><head><style>...</style></head><body>...</body></html>"})
User: ToolOutput(result="Created file `app/index.html`.")
Assistant: ToolCall(name="Write", args={"file_path": "app/styles.css", "content": "..."})
User: ToolOutput(result="Created file `app/styles.css`.")
Assistant: I've generated a simple blog frontend UI based on the guidelines.
```

讨论到这里，使用过 Skills 的读者可能发现了，如果把上述例子中的`resources/`重命名为`skills/`，那么`frontend-design.md`本质上就是一个 Skill（参考anthropics/skills/frontend-design/SKILL.md[9]）。

## Skills 的三层加载技术

至此我们可以看出，Skills 的核心思想，其实也遵循了 Context Offloading 的上下文管理策略。当然，上述例子只是最基础的实现。

![Agent Skills架构](https://mmbiz.qpic.cn/mmbiz_png/t55kqTnrJg1wY8IvfsQ2aaamPAMQm1PNqwPn84zcZgbhSib3Qkt3VXU3yQKaP09GIibNhBfanaSrDFlGh2uUDH8w/640?wx_fmt=png&from=appmsg)

Agent Skills架构

在 Anthropic 的设计中，又巧妙地引入了 Skills 的三层加载技术，以求最大化减少 LLM 上下文的负担：

1. **元数据（Metadata）**：可用 Skills 的名称、描述及其文件路径。这些信息会被预先放到上下文（系统提示词）中，以确保 Agent 知道有哪些 Skills 可以利用。
    
2. **指令（Instructions）**：每个 Skill 都有一个对应的`SKILL.md`文件，其中包含了 Skill 的详细描述、使用方法和示例等信息。当 Agent 需要某个 Skill 的帮助时，它会通过`Read`工具读取`SKILL.md`s 文件的内容，进而将其动态加载到上下文中。
    
3. **资源（Resources）**：除了`SKILL.md`文件，每个 Skill 还可以包含其他类型的资源文件，如配置文件、文档等。当 Agent 需要更具体的信息时，它会进一步读取这些资源文件的内容，从而将其加载到上下文中。
    

## 代码执行与虚拟机

除了前文讨论的内容，需要强调的是，Skills 的完整能力还涉及代码执行和虚拟机：

- **代码执行（Code Execution）**：某些 Skills 可能包含代码片段，甚至 Agent 为了处理任务还会动态生成代码，这些代码都需要执行。
    
- **虚拟机（Virtual Machine）**：为了确保安全性，通常需要在一个隔离的沙盒环境（虚拟机）中管理文件系统、执行 Shell 命令和运行代码。
    

![Agent Skills架构](https://mmbiz.qpic.cn/mmbiz_png/t55kqTnrJg1wY8IvfsQ2aaamPAMQm1PNIjwCGMbRkR0Y8YEBfUkVo3HXPLF4YpCbxwk1aRO8siaa5X8eIu85fGQ/640?wx_fmt=png&from=appmsg)

Agent Skills架构

由于篇幅所限，这里不再展开详细讨论，感兴趣的读者可以参考官方文档[10]或者其他相关资料。

## 结语

通过本文的探讨，相信读者对 Agent Skills 有了更深入的理解。在 Claude Agent 产品中，Skills 的实现基于 Context Offloading 这一上下文管理策略；而该策略的落地，则依托于 ReAct 模式的思想框架，以及文件系统、Shell 命令等基础工具的支撑。

此外，代码执行和虚拟机也是非常重要的话题，本文限于篇幅只做了简要提及。实际上，它们不仅是 Skills 的关键技术，也代表着 Agent 未来的主流演进方向。在后续的文章中，我们将继续深入探讨这些话题，敬请期待！

参考资料

[1]Agent Skills: _https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills_

[2] ReAct: _https://arxiv.org/abs/2210.03629_

[3] Function Calling: _https://platform.openai.com/docs/guides/function-calling_

[4] 揭秘 Claude Code：自主式编程: _https://russellluo.com/2025/09/demystifying-claude-code-agentic-coding_

[5] Lost in the Middle: _https://arxiv.org/abs/2307.03172_

[6] How Long Contexts Fail: _https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html_

[7] How to Fix Your Context: _https://www.dbreunig.com/2025/06/26/how-to-fix-your-context.html_

[8] The think tool: _https://www.anthropic.com/engineering/claude-think-tool_

[9] anthropics/skills/frontend-design/SKILL.md: _https://github.com/anthropics/skills/blob/main/skills/frontend-design/SKILL.md_

[10] 官方文档: _https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview_


Agent Skills的核心原理是通过"上下文卸载"(Context Offloading)解决LLM上下文限制问题。它基于ReAct模式，采用三层加载技术：先在系统提示词中注册元数据，再按需读取SKILL.md指令文件，最后调用相关资源文件。这种方式让Agent能精准调用专业知识而不造成上下文过载，配合文件系统和虚拟机技术支持，实现了高效的任务处理能力。