从今天起，人类写过的每一本专业书，都是你 AI 还没装上的子弹。

# 你的书架是你的军火库

**pdf2skill** 是一个"文档转技能编译器"，可以自动把 PDF 书籍、手册、行业报告转换成可以被 AI 助手（Claude Code 或 OpenCode）直接识别和调用的完整技能包，帮助你把"读过的书"变成"AI 的能力"。

## 🔍 一、pdf2skill 是什么？

**pdf2skill** 的核心目标：

> ⭐ 将任何 PDF 文档（专业书籍、操作手册、行业报告、技术规范），通过 AI 语义分析，转成一套结构完整、可直接导入智能助手的技能包。

这意味着：

- • 不用自己逐页阅读几百页的专业书籍；
    
- • 不用手动提炼知识点再写成 Prompt；
    
- • 不用懂 JSON Schema 或任何编程语言；
    
- • 一份 PDF 进去，一个 skills.zip 出来，解压即用。
    

## ✨ 二、pdf2skill 核心功能

pdf2skill 有以下核心能力：

✅ **语义拆解**：不按章节机械切割，按语义密度和逻辑边界智能拆分，识别核心概念、操作步骤、异常处理分支

✅ **逻辑建模**：自动建立知识点之间的依赖关系——A 是 B 的前置条件，C 是 D 的异常分支，E 和 F 可并行执行

✅ **技能封装**：输出标准 Skill 文件，每个技能有明确的输入、输出、触发条件和执行逻辑

✅ **路由生成**：自动生成技能索引和路由规则，AI 可根据用户问题自动匹配最相关的技能

✅ **零代码**：使用者不需要写任何代码，PDF 进，skills.zip 出

✅ **兼容 Claude Code 和 OpenCode**：生成的 SKILL.md 直接放入 skills 目录即可生效

![](https://mmbiz.qpic.cn/mmbiz_png/2SWibsXJ1He3tAdBwaT5fGsoz8Pp1G5TomW4qK1SibAFAzliavlibOwP1DjzHHtdcCkKCPb0z2ytYYOn1Xd4aGsrHibSibFEia2KBKaPAbqiceln1a0/640?wx_fmt=png&from=appmsg)

## 📦 三、快速开始（30 秒上手）

### 方式一：官网直接上传（推荐）

打开 **https://pdf2skills.memect.cn/quick-start** ，上传你的 PDF，等待编译完成，下载 skills.zip。

三步搞定：上传 → 编译 → 下载。

### 方式二：下载后导入技能

拿到 skills.zip 后，解压放到技能目录即可：

```
# Claude Code 用户unzip skills.zip -d ~/.claude/skills/你的技能名/# OpenCode 用户unzip skills.zip -d ~/.config/opencode/skills/你的技能名/# 项目本地unzip skills.zip -d your-project/.claude/skills/你的技能名/
```

导入完成，无需额外配置。AI 助手下次启动时自动加载。

### 方式三：进内测群，持续调优

扫码进群，我们愿意和你一起持续探讨：提升你的 PDF 转 Skill 的精度，适配你的具体使用场景。不只是给你一个工具，而是帮你把 Skill 真正调到能用、好用。

## 🧠 四、它和"让 AI 总结 PDF"有什么区别？

这是最常被问到的问题。区别很大：

|维度|直接让 AI 总结|pdf2skill 编译|
|---|---|---|
|产出物|一段文字摘要|一套可调用的 Skill 文件|
|颗粒度|章节级别，粗|细到单个操作步骤和判断节点|
|逻辑关系|丢失|完整保留依赖、分支、前置条件|
|可复用性|对话结束就没了|永久存在，跨会话调用|
|上下文占用|每次对话要重新喂文档|零占用，按需触发|
|实际效果|“这公司今年赚了点钱，增长不错”|调用 `financial-analysis-four-step-method` 执行四步分析框架|

一句话：**总结是读后感，编译是可执行的能力。**

![](https://mmbiz.qpic.cn/sz_mmbiz_png/2SWibsXJ1He1H0EQSeAia8Ds1srRLMdCicek54P9YPsI2rn6vs3mLvorTprDAyqcqhrmBK32f4OluEvuaNoow3KQB3ZiayWLKNnCicp9iacF5ClZE/640?wx_fmt=png&from=appmsg)

## 📌 五、实战案例：一本书 → 22 个 Skill → 一款完整游戏

这是一个完整的从 PDF 到成品的案例。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/2SWibsXJ1He21DRVjsoDu8eL1WUkDEAzPjVMicz4kIkGXIU1otK03snA00JIIeAv7DrzRRE8M1tAdc3gC8eM1Wr5fGgVshnaX5pW4EEMWDCcI/640?wx_fmt=png&from=appmsg)

我上传了 Wendy Despain 的《游戏设计的100个原理》，pdf2skill 生成了 **22 个独立 Skill**：

| 类别   | 生成的 Skill                                                                                                 | 覆盖内容                   |
| ---- | --------------------------------------------------------------------------------------------------------- | ---------------------- |
| 核心设计 | `game-design-methodology`, `game-development-planning`                                                    | 核心循环、项目规划              |
| 机制平衡 | `dynamic-difficulty-adjustment`, `doubling-halving-balance`, `flow-state-design-framework`                | 难度曲线、心流、快速调参           |
| 角色系统 | `character-optimization-design`, `reinforcement-feedback-systems`                                         | 属性系统、奖惩机制              |
| 关卡体验 | `game-competency-puzzle-design`, `experience-pacing-structure`, `environmental-storytelling`              | 谜题、节奏、环境叙事             |
| 交互设计 | `fitts-law-ui-aiming`, `hicks-law-decision-optimization`, `golden-ratio-design`, `visual-player-guidance` | Fitts/Hick定律、黄金比例、视觉引导 |
| 玩家心理 | `player-psychology-decisions`, `player-error-handling`, `fundamental-attribution-error`                   | 认知偏差、容错、归因分析           |
| 测试管理 | `game-prototyping-testing`, `game-team-management`                                                        | 原型测试、团队协作              |

![](https://mmbiz.qpic.cn/sz_mmbiz_png/2SWibsXJ1He3vpVHpMXbUSIXQGmB3ibWnrYN4FSBae5Nfa0sQhOsbDC07ACJlPDumuVNyHGNJMMkljfOvwMzt9Mps3oJph3pmW0kLD8K41Z0s/640?wx_fmt=png&from=appmsg)

每个 Skill 都有 frontmatter 元数据和可执行的设计框架——不是书的摘要，而是"当你遇到这个问题时，按这些步骤操作"的专业手册。

### 把 22 个 Skill 整合后，Claude Code 变成了游戏设计师

让 Claude Code 帮忙把 22 个零散 Skill 整合为一个主文件 + 参考文档的结构：

```
~/.claude/skills/game-design/
├── skill.md                    # 主技能文件（完整设计流程 + 速查表）
└── references/                 # 22 个深度参考文档    
	├── character-optimization-design.md    
	├── dynamic-difficulty-adjustment.md    
	└── ...（共 22 个）
```

主文件把书中 100 个原理提炼为 **8 个阶段的工作流**：概念定义 → 核心循环 → 机制与平衡 → 关卡与体验 → 视觉与交互 → 玩家心理 → 原型与测试 → 团队管理。

还自带 **4 种工作模式** 自动切换：

|模式|触发场景|做什么|
|---|---|---|
|从零开始|“我想做一个游戏”|走完整设计流程|
|专项咨询|“怎么设计难度曲线”|调用对应专项知识|
|诊断修复|“玩家反馈太难了”|定位问题 → 给方案|
|评审优化|“帮我看看这个设计”|用原理体系做 review|

### 实际产出：一款完整的策略游戏

装上这套 Skill 后，我用 Claude Code 的 Agent Team 做了一款叫《枢纽：天下之道》的策略游戏——基于施展老师的《枢纽：3000年的中国》。

Claude Code 启动 3 个 Agent 并行工作：一个解析《枢纽》的书籍 Skill，一个调用游戏设计框架，一个整理 100 个设计原理。素材到位后，走完 8 阶段设计流程，再启动 3 个内容 Agent 并行写历史事件，自己同时写核心引擎。

最终产出：**828 行代码，单文件 HTML，浏览器打开即玩。**

- • 七大空间面板（中原、草原、西域、过渡地带、高原、西南、海洋），各有兴/武/文/商四维属性
    
- • SVG 动态地图，区域节点实时反映状态变化
    
- • 每个历史事件配有历史引文和三个决策选项
    
- • 水墨风 UI + 多结局系统
    

在线试玩：https://orangeviolin.github.io/pivot-game/

**一本书 → 一套 Skill → 一个大师级助手 → 一个完整作品。这就是 pdf2skill 的完整链路。**

## 🔁 六、更多案例

### 《手把手教你读财报》→ 财报分析技能包

输入 300 页财报分析教材，输出：

- • `financial-analysis-four-step-method`：四步法系统分析框架
    
- • `cash-flow-statement-classification`：按三大活动分类分析现金流
    
- • `baijiu-revenue-forecasting`：基于产销存数据预测白酒企业营收
    
- • 共计 30+ 个细分技能，覆盖从读表到预测的完整链路
    

### 行业手册 → 操作规范技能

一份 200 页的行业操作规范，输出的不是摘要，而是一套带判断分支的操作流程：

```
IF 指标A > 阈值 → 执行流程1ELIF 指标B 异常 → 触发异常处理分支ELSE → 走标准流程
```

每个分支都是一个独立 Skill，AI 根据实际输入自动路由。

## 📁 七、生成的技能包里有什么？

一个 skills.zip 解压后的典型结构：

```
skills/
├── SKILL.md              # 技能主文档（AI 读这个文件理解所有技能）
├── skills/
│   ├── skill-001.md      # 单个技能定义
│   ├── skill-002.md
│   ├── ...
│   └── index.md          # 技能索引和路由规则
└── README.md             # 人类可读的说明文档
```

每个 Skill 文件包含：

- • **技能名称和 ID**：机器可识别的唯一标识
    
- • **触发条件**：什么场景下应该调用这个技能
    
- • **输入参数**：需要用户提供什么信息
    
- • **执行逻辑**：具体的分析步骤、判断规则、操作流程
    
- • **输出格式**：结果以什么形式呈现
    
- • **依赖关系**：和其他技能的前置/后置关系
    

## 🛠 八、技术细节

### 编译流程（Pipeline）

pdf2skill 内部分 5 个核心模块：

|模块|功能|说明|
|---|---|---|
|文档解析|PDF → 结构化文本|处理目录、标题层级、表格、图表说明文字|
|语义密度分析|识别高价值知识区域|基于信息产出率预测，跳过低密度段落|
|知识结构化|提取实体和关系|概念定义、操作步骤、条件分支、因果链|
|融合引擎|消除冗余，建立索引|多章节交叉引用的知识去重和关联|
|Skill 转换|输出标准格式|生成 SKILL.md + 单技能文件 + 路由索引|

### 支持的 PDF 类型

|类型|适合程度|说明|
|---|---|---|
|方法论书籍（管理、分析、设计）|⭐⭐⭐⭐⭐|有明确框架和步骤，转化效果最好|
|操作手册 / 使用指南|⭐⭐⭐⭐⭐|流程清晰，天然适合 Skill 化|
|行业报告 / 研报|⭐⭐⭐⭐|数据+分析框架，可生成分析类技能|
|专业领域教材（医学、法律、金融）|⭐⭐⭐⭐|知识密度高，技能数量多|
|技术规范 / 标准文档|⭐⭐⭐⭐|规则明确，适合生成判断类技能|
|纯理论著作|⭐⭐|可操作性低，生成的技能偏概念解释|
|文学作品 / 散文集|⭐|不推荐，缺少可结构化的逻辑|

## ❓ 九、常见问答（FAQ）

**我需要安装什么吗？** 不需要。打开 https://pdf2skills.memect.cn/quick-start ，上传 PDF，下载 skills.zip，解压导入即可。

**需要提供 API Key 吗？** 不需要。编译过程在我们这边完成。

**PDF 有大小限制吗？** 当前支持 500 页以内的 PDF。超大文档会分批处理。

**生成一本书的技能要多久？**

- • 100 页以内：2-5 分钟
    
- • 100-300 页：5-15 分钟
    
- • 300-500 页：15-30 分钟
    

（其实时间越长等于我们为你的PDF烧的token越多，skill的质量也越好）

具体取决于内容密度和结构复杂度。

**生成的 Skill 可以自己修改吗？** 可以。输出的都是 Markdown 文件，可以直接编辑、增删、调整触发条件。

**支持英文 PDF 吗？** 支持中文和英文。

## 结语

|特性|说明|
|---|---|
|输入格式|PDF（书籍、手册、报告、规范）|
|输出格式|skills.zip（标准 Skill 文件，解压即用）|
|核心能力|语义拆解 → 逻辑建模 → 技能封装|
|兼容工具|Claude Code / OpenCode / Cursor|
|零代码|不需要写任何代码或 Prompt|
|可编辑|输出 Markdown 格式，支持手动调整|
|当前状态|v0.1 内测，免费体验|

如果你是开发者、业务专家、咨询顾问，或者硬盘里有一堆"以后会看"的 PDF，**pdf2skill 帮你把它们从死文档变成 AI 的活技能。**

官网：**https://pdf2skills.memect.cn/quick-start** ，上传 PDF 即可体验。

想深度交流、持续调优 Skill 精度？扫码进内测群：