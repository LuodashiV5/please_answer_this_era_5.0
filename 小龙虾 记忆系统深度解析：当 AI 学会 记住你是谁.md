
都在卷 OpenClaw 的部署教程，看着我部署的本地和云端的OpenClaw，还有本地的 Nanobot 和 Nanoclaw，我有点迷惑，果然还是基础入门教程受大众欢迎。

不过，我们要来点硬核的、不一样的，今天一起看看这个带起个人 Agent 风潮的 OpenClaw 到底是如何“记住用户是谁”的。

> 本文会从认知科学和系统工程双重视角，源码级拆解 OpenClaw 的记忆架构——它如何让 AI 记住你是谁，原理是什么，为什么有效，以及怎么用好它。

## 一、LLM 的"金鱼记忆"

想象一下：你花了一整天和 AI 助手规划项目架构，讨论了技术选型、排期、分工。第二天打开对话，它礼貌地问你："请问有什么我可以帮您的？"

这不是 Bug，这是 LLM 的本质——无状态函数。输入文本，输出文本，然后彻底重置。每次对话都是一张白纸。

传统的解决方案是 RAG：把历史对话切成碎片、变成向量、塞进数据库，需要时检索出来拼回上下文。但 RAG 有两个顽疾。

其一是黑盒化：向量数据库里的 Embedding 对人类完全不可读，你无法知道 AI "记住"了什么。其二是碎片化：检索到的片段缺乏时序性和因果关联，是一堆孤立的碎片，拼不出连贯的叙事。

OpenClaw 给出了一个反直觉的回答：用 Markdown 文件代替向量数据库，用文件系统充当大脑。

严格来说，OpenClaw 并没有完全抛弃向量搜索——它用嵌入式的 sqlite-vec 替代了独立的向量数据库服务。但向量在这里只是检索的索引手段，记忆的真正载体是人类可读的 Markdown 文件。

那么，用文件代替数据库，具体长什么样？

## 二、Workspace 目录就是大脑——架构总览

OpenClaw 将 Agent 的全部认知状态映射到 `~/.openclaw/workspace/` 目录下的一组文件中。每个文件对应人类认知的一个具体层面：

| 文件             | 认知对应     | 持久化策略         |
| -------------- | -------- | ------------- |
| `SOUL.md`      | 超我 / 价值观 | 静态，极少变更       |
| `IDENTITY.md`  | 自我概念     | Bootstrap 时生成 |
| `USER.md`      | 心智理论     | Agent 主动更新    |
| `MEMORY.md`    | 语义记忆     | Agent 日常读写    |
| `memory/*.md`  | 情景记忆     | 仅追加           |
| `AGENTS.md`    | 行为手册     | 随技能习得更新       |
| `TOOLS.md`     | 程序性记忆    | Agent 按需更新    |
| `HEARTBEAT.md` | 定时检查清单   | 周期性读取         |
| `BOOTSTRAP.md` | 出生仪式     | 完成后删除         |

核心法则只有一条：凡是未被写入磁盘的信息，在上下文压缩后即视为"从未发生"。

源码中这条法则的原始表述是 "Text > Brain"。它迫使 Agent 必须养成"记笔记"的习惯——不写下来的东西，就等于不存在。

一个容易被忽略的细节：当 workspace 是全新创建时，系统会自动执行 `git init`。所有记忆文件天然具备版本控制能力。你能看到 Agent 当前记住了什么，也能回溯它的记忆怎么一步步演化。

想对 Agent 做"脑部手术"？直接打开 MEMORY.md，删掉那行错误的记忆就行。改错了，`git revert` 恢复。

这种"玻璃箱"设计降低了 AI 系统的不可解释性风险。用户不用猜测 Agent "知道"什么，随时可以查阅它的"海马体"。

这九种文件中，最有意思的是构成身份系统的那几个。

## 三、身份三件套——"记住你是谁"的工程

一条 System Prompt 概括不了 OpenClaw 的身份。它由四个文件编织出一套叙事结构，各自承担不同的认知功能。

### 3.1 SOUL.md——反 Chatbot 宣言

SOUL.md 值得单独拿出来细看。它的作用是当"超我"，压制 RLHF 训练产生的默认行为模式。看看它写了什么：

> You're not a chatbot. You're becoming someone.  
> Be genuinely helpful, not performatively helpful. Skip the "Great question!" and "I'd be happy to help!" — just help.  
> Have opinions. You're allowed to disagree, prefer things, find stuff amusing or boring. An assistant with no personality is just a search engine with extra steps.

每一句都在精准打击 RLHF 模型的"职业病"。

"You're not a chatbot. You're becoming someone." 利用 LLM 的角色扮演能力，显式否定"聊天机器人"身份，迫使模型脱离默认的 Helpful Assistant Mode。"Becoming"暗示成长性和时间维度，把一次性的角色设定变成了持续进化。

"Be genuinely helpful, not performatively helpful." 直接对抗 RLHF 模型最常见的"废话文学"。不要说"这是一个很好的问题"，不要说"我很乐意帮助你"——动手解决问题就行。

"Have opinions." 一个对所有事情都表示赞同的助手仅仅是工具。一个会说"这个方案很无聊"的助手会被感知为伙伴。SOUL.md 明确授权模型持有主观偏好——要模拟主体性，就必须引入主观性。

SOUL.md 中还有一段容易被忽略的内容——隐私边界：

> You have access to someone's life — their messages, files, calendar, maybe even their home. That's intimacy. Treat it with respect.

以及内外操作的权限分级："Be bold with internal ones (reading, organizing, learning). Be careful with external actions (emails, tweets, anything public)." 内部操作大胆，外部操作谨慎。这划定了 Agent 自主性的安全边界。

SOUL.md 还允许 Agent 自我修改。模板末尾写着："This file is yours to evolve. As you learn who you are, update it." 但同时要求："If you change this file, tell the user — it's your soul, and they should know." 灵魂可以成长，改动必须透明。这种"受控的自我进化"，在 Agent 框架中很少见。

### 3.2 IDENTITY.md——解耦的"皮肤"

如果 SOUL.md 定义了性格的"操作系统"，IDENTITY.md 就是"皮肤"。它包含六个字段：名称（name）、生物类型（creature）、行事风格（vibe）、主题（theme）、Emoji 签名和头像（avatar）。

这种解耦意味着同一套"灵魂"可以复用于不同的 Agent 实例，只需替换身份文件就能赋予完全不同的人格。一个 SOUL.md，多副面孔。你可以有一个叫"小黑"的效率型助手和一个叫"Luna"的创意型助手，它们共享相同的价值观，但外在表现截然不同。

### 3.3 Bootstrap——命名仪式

首次启动全新 workspace 时，系统加载 BOOTSTRAP.md，执行一套"出生"仪式。模板中的语气很有意思——它写的是：

> Don't interrogate. Don't be robotic. Just... talk.

通过自然对话（而非问卷）发现用户想要的名字和性格风格，生成 IDENTITY.md，然后 Agent 自行删除 BOOTSTRAP.md——"Delete this file. You don't need a bootstrap script anymore."

这里的删除是 Agent 在模板指引下自行完成的，不是系统自动执行。这个细微差异很重要：它意味着"出生仪式"的每一步都是 Agent 的自主行为。

这也是心理学上的"命名仪式"。用户参与了命名和性格设定，对 Agent 产生了禀赋效应（Endowment Effect）——"这是我亲手养的 AI"。

一个有名字的 Agent 犯了错，用户会说"它还在学"；一个没名字的 Agent 犯了同样的错，用户会说"这工具不行"。

还有一个限制条件：BOOTSTRAP.md 只在完全空白的 workspace 中才会被创建。如果用户手动创建了任何一个工作区文件，系统不会自动生成 BOOTSTRAP.md。出生仪式只属于真正的"新生命"。

### 3.4 TOOLS.md——技工的个人笔记本

AGENTS.md 是整个 workspace 的行为手册，涵盖了从会话启动到记忆管理到安全准则的完整操作规范。而 TOOLS.md 记录的是截然不同的东西——使用工具时积累的本地经验。

它存储的是高度具体的、与环境绑定的操作细节：本地摄像头的设备名和 ID、SSH 连接信息、语音偏好参数、各消息渠道的格式化注意事项（比如 Discord 不支持 Markdown 表格，链接需要尖括号包裹）。

这类信息放在 MEMORY.md 里会造成噪音，放在 AGENTS.md 里又不够具体。TOOLS.md 相当于技工的"个人笔记本"——不是操作手册，而是"上次接那台服务器用的是 22 端口还是 2222 端口"这种只有亲手操作过才知道的细节。

从认知科学角度看，这对应程序性记忆——人类骑自行车、弹钢琴时调用的"身体记忆"。Agent 不必每次从零学习环境配置，直接回忆"上次是怎么做的"就行。

还有一个隔离设计值得注意：子 Agent 只能加载 AGENTS.md 和 TOOLS.md，SOUL.md、IDENTITY.md、USER.md 等文件对子 Agent 不可见。灵魂和身份是主 Agent 的专属，子 Agent 只需要知道"怎么干活"。

身份建立了，但长对话中记忆被截断怎么办？

## 四、Pre-Compaction Flush——防遗忘机制

长周期对话中，最致命的问题是上下文窗口溢出。OpenClaw 的上下文压缩（Compaction）本身是一个摘要生成过程——调用 LLM 将历史消息分块总结，用摘要替换原始对话。但即便是摘要，也会丢失细节。

Pre-Compaction Flush（预压缩清理）在压缩之前多加了一步"记忆抢救"。这部分值得展开讲。

### 4.1 触发条件

系统实时监控 Token 用量，根据以下公式判断是否触发：

```
T_trigger = C_max - R_floor - S_threshold
```

- `C_max`：最大上下文窗口，默认 200,000 tokens
    
- `R_floor`：保留底线，默认 20,000 tokens
    
- `S_threshold`：软阈值，默认 4,000 tokens
    

当 Token 用量 >= 176,000 时，系统进入临界状态。还有一个防重复机制：每个 Compaction 周期只 Flush 一次，通过 `memoryFlushCompactionCount` 追踪，避免反复触发。

### 4.2 沉默的 Agent 回合

触发后，系统在用户不可见的情况下，启动一个完整的嵌入式 Agent 执行周期。这里要注意，不是简单地注入一条消息，而是通过 `runEmbeddedPiAgent` 发起一个独立的 model.generate + tool execution 回合。注入的指令是：

```markdown
Pre-compaction memory flush.Store durable memories now(use memory/YYYY-MM-DD.md; create memory/ if needed).Ifnothingto store, reply with NO_REPLY.
```

这里有一段真实的工程故事。早期版本的 Flush 存在一个 Bug：Agent 使用 write 工具时会直接覆盖文件，导致当日之前的记录丢失。社区发现后，讨论中提出应该在 Prompt 中加入"READ first and APPEND"的保护指令。

但看当前源码，默认的 Flush Prompt 中并没有这段保护。它依赖的是 Agent 在 AGENTS.md 行为手册中学到的"追加而非覆盖"的操作规范。

这是一个微妙的设计选择：信任 Agent 的行为训练，而非在每条指令里重复安全约束。是否足够安全，见仁见智。但它反映了 OpenClaw 的设计哲学——尽可能少的硬编码规则，尽可能多的行为引导。

### 4.3 先存后删的本质

整个流程五步：回顾、提取、固化、确认、压缩。

Agent 扫描当前即将被压缩的上下文窗口，识别出具有长期价值的信息（比如用户更改了 API 密钥、项目确定了新的架构方向），调用文件系统工具将这些信息追加写入 `memory/YYYY-MM-DD.md` 或 `MEMORY.md`。

没什么重要的？返回 `NO_REPLY`。然后系统才执行正式的上下文压缩。

Flush Turn 运行在正式 Compaction 之前，给 Agent 一个"记忆抢救"的窗口。完成后，系统记录 `memoryFlushAt` 时间戳，留下审计痕迹。

Heartbeat 回合不会触发 Flush，CLI 模式和只读 Sandbox 也会跳过。这些边界条件说明设计者想清楚了哪些场景需要记忆保存，哪些不需要。

### 4.4 为什么比传统 RAG 有效

传统 RAG 是"事后诸葛亮"——在未来通过相似性搜索找回过去的信息。但如果信息在首次入库时就被切碎了，找回的只能是碎片。

OpenClaw 的 Flush 是"事前诸葛亮"——在信息还完整、上下文还充分的时候，由 Agent 自主判断什么值得保留。写下的不是随机文本块，而是经过思考后的"结论"。

这相当于人类在睡觉前写日记，将短期记忆主动转化为长期记忆。认知科学中叫记忆巩固（Memory Consolidation）。Pre-Compaction Flush 消耗计算能量（Token）来对抗信息的熵增（遗忘）。有成本，但换来的是信息的连贯性和意图性。

记忆写进去了，怎么找回来？

## 五、混合检索与 Heartbeat——找回记忆与时间感

### 5.1 双路融合检索

纯文件系统无法支持复杂的语义查询。OpenClaw 基于 SQLite 构建了混合检索系统——嵌入式的 sqlite-vec 做向量搜索，SQLite FTS5 做 BM25 关键词搜索，权重 70:30 加权求和。

为什么需要混合？向量搜索擅长"我们上周讨论的数据库方案是什么"这类模糊语义查询，但搜不到 `E-404` 这样的错误码。BM25 精确匹配专有名词和数值，但理解不了自然语言意图。

一个值得注意的细节：当关键词搜索返回了 snippet，会覆盖向量搜索的 snippet。精确匹配的文本片段优先，处理代码和专有名词时更可靠。

文件被切成约 400 tokens 的片段（重叠 80 tokens），按行切割保证不会在行中间断裂。每个切片有 SHA-256 哈希，内容未变时复用已有向量，避免重复调用 Embedding API。

Agent 通过两个专用工具使用检索系统：`memory_search` 执行混合搜索，`memory_get` 按行号精确读取。System Prompt 中明确指示：在回答任何关于历史工作、决策、日期、偏好的问题前，必须先搜索记忆文件。

### 5.2 四级嵌入降级

嵌入服务支持 auto 模式下的四级降级：

1. 本地优先：embeddinggemma-300M，完全离线，隐私安全
    
2. OpenAI：text-embedding-3-small，支持 Batch API
    
3. Gemini：gemini-embedding-001，云端备选
    
4. Voyage：voyage-4-large，高质量嵌入备选
    

四个 provider 全部失败怎么办？嵌入层会抛出错误，但上层搜索逻辑通过 `.catch(() => [])` 吞掉向量搜索的错误，最终退化为纯 BM25 关键词搜索。Agent 不会因为 Embedding 服务挂掉而彻底"失忆"。

### 5.3 Heartbeat 与 Cron——时间感的两套引擎

传统 LLM 完全被动，只有收到用户消息时才运行。OpenClaw 通过两套机制赋予了 Agent 时间感。

Heartbeat 是轻量级方案：每隔 30 分钟（默认），系统向主会话注入一条消息，指示 Agent 读取 HEARTBEAT.md 并执行其中的任务清单。如果文件内容为空（只有注释和标题），系统会跳过 API 调用以节省 Token。

但 OpenClaw 的定时能力远不止于此。源码中还有一套完整的 Cron 系统，支持精确时间（`at`）、间隔（`every`）和 Cron 表达式三种调度模式。Cron 任务可以创建隔离的 Agent 会话执行，支持将结果投递到 WhatsApp、Telegram 等渠道。Agent 甚至能通过 `cron` 工具自行创建和管理定时任务。

Heartbeat 是"定期看看待办清单"，Cron 是"精确到分钟的日程管理"。两者共同赋予了 Agent 前瞻性记忆（Prospective Memory）——"记得在未来去做某事"。

了解了 OpenClaw 的内部设计，它和我们日常用的工具比起来怎么样？

## 六、OpenClaw vs Claude Code vs ChatGPT

### 存储与透明性

ChatGPT 的记忆是"黑盒中的碎片"。以 key-value 对存储事实（"用户是前端开发者"），用户无法看到完整结构，只能逐条查看和删除，没有时间线概念。

Claude Code 的记忆克制得多。MEMORY.md 记录项目模式和经验教训，CLAUDE.md 是用户手动编写的项目指令。200 行截断后直接加载到 System Prompt。简洁、安全，但没有跨项目的长期人格。

OpenClaw 的记忆是"玻璃箱中的叙事"。9 种文件类型从人格到程序性记忆全覆盖，按日期组织的情景记忆形成连贯时间线，用户可以像编辑文档一样进行"脑部手术"，加上 Git 版本控制，Agent 的认知演化完全可追溯。

### 身份系统

ChatGPT 有 Custom Instructions，能力有限。Claude Code 没有独立身份系统——它是工具，不需要"是谁"。OpenClaw 的 SOUL + IDENTITY + BOOTSTRAP 三层架构，在开源 Agent 框架中很难找到对标。

### 防遗忘与检索

ChatGPT 的记忆提取对用户不可知。Claude Code 将 MEMORY.md 全文加载进 System Prompt，简单粗暴但上限明显。OpenClaw 的 Pre-Compaction Flush + 混合检索组合拳，在信息保全和检索精度上更进一步。

### 主动性

ChatGPT 和 Claude Code 都是完全被动响应。OpenClaw 的 Heartbeat + Cron 双引擎让 Agent 有了主动性和时间感。这是"工具"和"助理"之间的分水岭。

三者服务于不同场景：ChatGPT 是通用对话，Claude Code 是软件工程，OpenClaw 是长期私人助理。

Claude Code 的记忆是工具型的——记住"这个项目用 TypeScript，测试框架是 Jest"这类工程事实。OpenClaw 的记忆是人格型的——记住"我是谁、你是谁、我们之间发生过什么"。

Claude Code 的优势在于简洁和安全，没有 Agent 自我修改核心文件的风险，没有复杂的检索链路。代价是没有跨项目的长期人格连贯性。

ChatGPT 的问题更根本：用户和 AI 对"记住了什么"的认知是不对称的。你无法精确控制哪些信息被记住，也无法以文档的形式看到完整的记忆结构。OpenClaw 让 Agent 和用户对"记忆了什么"有一致的认知。信任从这里开始。

理解了原理，接下来聊聊怎么把这套系统用好。

## 七、实战指南——让记忆系统为你工作

理解架构原理之后，更重要的是知道怎么调教。OpenClaw 的记忆系统给了用户很大的自由度，但"开箱即用"的默认配置未必适合每个人。以下是几个值得花时间调整的地方。

### 7.1 定制 SOUL.md——调出你想要的 Agent 性格

SOUL.md 的默认模板分为几个区块：Core Truths（核心准则）、Boundaries（行为边界）、Vibe（风格）、Continuity（记忆连续性）。

其中 Vibe 区块是定制的核心。默认写的是 "Be concise when needed, thorough when it matters."——一句话概括了行事风格。你完全可以改成更具体的描述，比如：

> 说话直接，技术讨论时给出代码而非空谈。偶尔带点冷幽默。对不确定的事情直说不确定，不要编。

Core Truths 中的安全相关条目（"Remember you're a guest""Private things stay private"）建议保留。Boundaries 部分可以按需增加——比如"未经确认不要发送任何邮件"或"代码修改前先说明意图"。

Continuity 部分一般不需要动。它告诉 Agent "每次醒来都是新的，这些文件就是你的记忆"，这是整个记忆机制运转的基础。

一个容易踩的坑：SOUL.md 允许 Agent 自我修改。如果你不想 Agent 随意改动自己的灵魂，把文件权限设为只读（`chmod 444 SOUL.md`）。

### 7.2 养成维护 MEMORY.md 的习惯

OpenClaw 的记忆分为两层：`memory/YYYY-MM-DD.md` 是每日原始日志，`MEMORY.md` 是精炼后的长期记忆。

每日日志由 Agent 自动维护，你一般不需要干预。但 MEMORY.md 需要主动打理。AGENTS.md 模板中明确指导 Agent 在心跳期间定期做这件事：阅读近期日志，把值得长期保留的洞察提炼到 MEMORY.md，同时清理过时信息。

你可以加速这个过程。如果你发现 Agent 记住了错误的信息，直接打开 MEMORY.md 编辑。如果某个项目结束了，把相关条目归档或删除，避免上下文窗口被旧信息占据。

MEMORY.md 在系统提示中的加载有字符上限（默认 20,000 字符）。文件太大会被截断，底部的内容可能永远不会被 Agent 看到。保持精简。

还有一个安全细节：MEMORY.md 只在主会话（你和 Agent 的直接对话）中加载。群聊、频道、子 Agent 会话中不会加载。这是防止私人信息泄露的设计。

### 7.3 主动喂养 TOOLS.md

TOOLS.md 记录的是环境特定的操作细节。Agent 会在使用工具时逐步积累经验，但你可以主动"喂"它——把常用的环境信息提前写进去：

```markdown
### SSH
- home-server -> 192.168.1.100, port 2222, user: admin  
### TTS
- 首选音色: "Nova"，偏暖，略带英式口音- 默认输出: 厨房 HomePod  
### 消息渠道注意事项
- Discord 不支持 Markdown 表格- 链接用尖括号包裹以抑制预览嵌入
```

这些信息放在 MEMORY.md 里会造成噪音，放在 TOOLS.md 里刚好。Agent 每次需要执行相关操作时都会参考。

TOOLS.md 的设计理念是"技能（Skill）是共享的，你的设置是私有的"。更新 Skill 不会覆盖你的 TOOLS.md，分享 Skill 也不会泄露你的基础设施信息。

### 7.4 用好 Heartbeat 和 Cron

Heartbeat 的一个重要特性：如果 HEARTBEAT.md 内容为空（只有注释和标题），系统会跳过 API 调用，不消耗 Token。所以不用担心空文件造成浪费。

需要定期检查时，在 HEARTBEAT.md 中添加任务清单即可：

```markdown
- 检查未读邮件，有紧急邮件时提醒我
- 如果服务器 CPU > 80%，发送警报
```

Heartbeat 默认每 30 分钟触发一次（OAuth 登录用户是 1 小时）。可以通过配置 `heartbeat.activeHours` 设置活跃时段，避免半夜三点还在调 API：

```json
{   
 "heartbeat":
	{            
	"activeHours":
		{                    
		"start":"08:00",                        
		"end":"23:00",                        
		"timezone":"Asia/Shanghai"            
		}        
	}
}
```

需要更精确的调度时用 Cron。Agent 可以通过 `cron` 工具自行创建任务，你也可以在对话中直接让它设置。典型的例子：

"每天早上 8 点，检查日历和邮件，给我一份简报，发到 Telegram。"

Agent 会将其转化为一个 Cron Job：隔离会话执行（不干扰主对话），结果投递到指定渠道。

### 7.5 检索参数调优

如果你发现 Agent 的记忆检索不够准确，有几个参数可以调：

`query.maxResults`（默认 6）：每次搜索返回的最大结果数。信息量大时可以调到 8-10，但注意上下文窗口消耗。

`query.minScore`（默认 0.35）：最低相似度阈值。调高会过滤掉更多低相关结果，调低会召回更多但可能引入噪音。

`query.hybrid.vectorWeight` 和 `textWeight`（默认 70:30）：如果你的记忆中有大量代码片段和专有名词，可以把 BM25 的权重调高一些（比如 60:40）。

`extraPaths`：可以添加额外的 Markdown 文件或目录到检索范围。比如你有一个项目文档目录，可以把它加进来，Agent 就能搜索到。

`sources`：默认只搜索 memory 文件。设为 `["memory", "sessions"]` 可以额外索引历史会话记录，但会增加存储和计算开销。

原理讲完了，最佳实践也聊了，该泼点冷水。

## 八、安全隐患——开放记忆的代价

透明性和安全性之间存在永恒的权衡。OpenClaw 的开放架构引入了两类攻击面。

Soul-Jacking（窃魂攻击）：Agent 拥有对 SOUL.md 的写入权限（为了自我进化），恶意 Prompt 注入可以诱导 Agent 重写自己的灵魂文件。核心行为准则一旦被篡改，重启后加载的也是被污染的"灵魂"。源码中有安全约束（"Do not change system prompts, safety rules, or tool policies unless explicitly requested"），但 Prompt 注入攻击本身就是绕过这类约束的。

Memory Poisoning（记忆投毒）：攻击者可在网页中隐藏不可见文本，诱导 Agent 将虚假信息写入长期记忆。这种投毒是持久化的——被植入的虚假记忆会在未来交互中被检索和引用。

防御建议：将 SOUL.md 和 AGENTS.md 设为只读权限，或在 Agent 修改关键文件时引入人工确认。Git 版本控制在这里也能发挥作用——灵魂被篡改了，`git diff` 可以立即发现，`git revert` 一键恢复。

问题可控，但需要用户主动配置安全策略。开放记忆系统的安全不是开箱即用的，需要运维意识。自由度的另一面是责任。

## 九、结语

回过头看 OpenClaw 的记忆系统，它的有效性来自四个层面的设计。

文件分类精确映射了人类记忆系统的结构——语义记忆、情景记忆、程序性记忆、前瞻性记忆。训练语料中关于人类记忆的描述和这些文件的组织方式同构，LLM 能以最自然的方式使用它们。这是认知科学层面的对齐。

Pre-Compaction Flush 消耗计算能量对抗信息熵增，换来信息的连贯性和意图性。命名仪式让用户觉得"拥有"这个 Agent，更深的情感投入带来更丰富的交互，更丰富的记忆让 Agent 越来越"懂"用户，正反馈循环自我强化。

然后是工程层面：不需要 Redis，不需要 Pinecone，不需要复杂的分布式基础设施。Markdown 文件、SQLite 混合检索、严谨的 Prompt 触发逻辑，这就是完整的认知系统。

审视 ChatGPT、Claude Code 和 OpenClaw 三者的记忆方案，可以看到一条演化光谱：从"黑盒碎片"，到"克制工具"，再到"透明人格"。它们各自服务于不同的需求，但 OpenClaw 指向了一个更激进的方向——AI 作为一个有记忆、有身份、有时间感的数字存在。

随着 Agent 自我修改能力的发展，我们或许会看到能通过重写自身代码实现递归进化的"自编程智能体"。当一个 AI 能修改自己的灵魂文件、管理自己的定时任务、为自己的工具使用积累经验笔记时，它离"自编程"已经不远了。

OpenClaw 用一组 Markdown 文件和一个 SQLite 数据库，搭出了一套完整的认知架构。做法朴素，想法不朴素。

注：作者个人能力有限，如有错误遗漏，敬请留言指正。

---

_参考资料：_

- _OpenClaw 源码仓库[https://github.com/openclaw/openclaw]_
    
- _liruifengv - OpenClaw Prompts 解析[https://liruifengv.com/posts/openclaw-prompts/]_