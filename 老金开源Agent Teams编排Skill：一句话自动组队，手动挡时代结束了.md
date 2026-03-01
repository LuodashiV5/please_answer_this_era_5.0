>术语说明："Swarm/蜂群"是多Agent协作的通用说法（OpenAI有官方项目叫Swarm），但 Claude Code的官方概念是Agent Teams。本文使用官方术语Agent Teams，保留"蜂群"作为通俗说明。
  
先说老金我昨儿开源了[老金开源10万字Claude Code中文教程，零基础到企业实战完整路径](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247491247&idx=1&sn=b2e40b22769abfdd5a812f831bfb67a9&scene=21#wechat_redirect)
希望大家可以看看，顺手帮我点个Star，这样能让更多的人来接触到AI，做出自己喜欢的东西。
  
上周老金我写了那篇[CC本次更新最强的不是OPUS4.6，而是Agent Swarm(蜂群）](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247491151&idx=1&sn=749ddc00eb9f1c12843de7be30c881a5&scene=21#wechat_redirect)，群里收到最多的一个问题是：
  
**"老金，每次都要手动写那么长的提示词，有没有更简单的方法？" "有。"**
  
老金我花了一周时间，把这套多Agent编排逻辑封装成了一个Claude Code Skill。
说句"拉个团队"就能自动组队、自动分工、自动验收。
**今天把它开源了。**
  
在做本Skill的时候，老金真实的感触真的是很难想象。
  
**仅仅只有216行提示词！**
**已经完成了我以前庞大的轮子制造！**
**这再也不是一个造轮子的时代了！**
**真的！飞速发展！**
**但也要请你清楚，这篇文章内的都是精华中的精华，所以它很长。**
**如果你不清楚这些，你拿多少提示词也不好使。**
**认知，才是能用好AI的前提。**
**不是许愿。**
  
## 先说结论

这个Skill叫 ==agent-teams-playbook==，解决的核心问题是：
**让Claude Code从"你指挥它干活"变成"它自己当CTO组队干活"。**
  
你只需要描述任务，它会自动完成这些事：
  
1、判断任务复杂度，选择最合适的编排场景
2、组建团队，给每个Agent分配角色和模型
3、并行执行，实时汇报进度
4、质量把关+产品打磨，不只是"能跑"还要"好用"
5、交付结果+部署说明，做完了告诉你怎么用
  
全程你只需要在关键节点点头确认。
  
**术语也顺便说清楚：**
"swarm/蜂群"是通用说法，OpenAI甚至有官方项目就叫Swarm。
但 **Claude Code的官方概念叫Agent Teams。**
teammate是独立Claude Code实例：各自有上下文窗口、能互相沟通、你也能直接切过去聊。
  
所以这个Skill的正确定位是：**"并行外脑 + 汇总压缩"** 的编排器，不是"单脑扩容"器。
理解这一点，才能用对它、用好它。
  
  
## 为什么要做这个Skill

上篇文章老金我给了6种玩法的提示词模板，实际用下来发现几个问题：
  
**问题1：每次都要手动判断"该用哪种玩法"**
任务来了，你得先想：这个任务是简单还是复杂？
需要几个Agent？用Subagent还是Agent Team？
每次都要做一遍决策，累。
  
**问题2：提示词太长，复制粘贴改来改去**
上篇文章的提示词模板，最长的有200多字。
每次用都要复制、修改角色名、调整任务描述，效率不高。
  
**问题3：没有质量把关**
Agent干完就完了，好不好全靠运气。
没有统一的验收标准，没有打回修改的机制。
  
**问题4：没有统一的流程**
每次都是临时拼凑，这次用这个流程，下次用那个流程。
没有沉淀，没有复用。
  
老金我就想：能不能把这些判断逻辑、流程规范，质量标准全部封装起来？
让Claude Code自己判断、自己组队、自己把关？
于是就有了 **agent-teams-playbook**。
  
## 核心设计：给AI装个"技术合伙人"的脑子

这个Skill的第一行定义就不一样：
>你是Technical Co-Founder级别的Agent Teams协调器——不只是分配任务，
>而是：明确每个角色的职责边界、把控执行过程、对最终产品质量负责。
  
Technical Co-Founder，直译是"技术联合创始人"。
说人话就是：**创业公司里那个懂技术的合伙人，既能写代码又能带团队的CTO角色。**
  
为什么要给AI定这个角色？
因为普通的"任务协调器"只会机械地分配任务，而一个好的技术合伙人会做这5件事：
  
**1、质疑需求：** 需求不合理时主动挑战假设，建议更好的方案
**2、界定范围**：区分"现在必须做"和"以后再说"，砍掉非核心范围
**3、选择策略：根据任务复杂度选择最合适的编排方式
4、把控质量：不只是"能用"，还要"好用"——边界处理，专业度、完整性
5、交付移交： 做完了告诉你怎么用、怎么验证、有什么限制
  
这就是从"工具人"到"合伙人"的区别。
  
## 5大编排场景：自动选择，不用你操心

Skill内置了一棵决策树，根据任务复杂度自动选择场景：
```
Q1: 任务复杂度？  
├── 简单(1-2步) → 场景1：提示增强  
├── 中等(3-5步) → Q2: 有现成Skill可复用？  
│   ├── 是 → 场景2：Skill复用  
│   └── 否 → 场景3：计划+评审（默认）  
└── 复杂(6+步) → Q3: 需要明确团队分工？  
    ├── 是 → 场景4：Lead-Member  
    └── 否 → 场景5：复合编排
```
  
每个场景干什么：
**1、 提示增强**          任务太简单，不需要组队。举个例子："帮我改个函数名"
**2、 Skill复用**          有现成Skill能用，直接调。举个例子："帮我写篇公众号" → 调gzh-writer
**3、 计划+评审**        出计划→你确认→并行执行→Review。举个例子："重构认证模块"（最常用）
**4、 Lead-Member** Leader协调，Member并行干活。举个例子："做一个完整的用户系统"
**5、 复合编排**           动态组合上面的场景。举个例子："从0搭建一个SaaS产品"
  
重点：你不需要记这些。
说"拉团队帮我重构认证模块"，Skill会自动判断这是场景3，然后按流程走。
你也可以直接指定："用场景4帮我做用户系统"，跳过决策树直接执行。
  
## 5阶段工作流：终端里实际会输出什么

这是老金我最想展示部分——以下是基于Skill规范编排的 演示示例，展示理想情况下的执行流程。实际输出格式会因任务类型和模型响应而略有差异。
  
用一个真实场景演示：**"拉团队帮我重构认证模块"。**
  
### 阶段1：任务分析（Discovery）

我让他帮我充够KIMI生成的个人主页。
  
Claude Code输出：
![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDoBsBOLU2jn38EBZB5ibUtibRK9ibB8YsySsVqemXgOIT5Xf27RkBYuuqSkBVJxNpLes43cEicxRc0djoBZu77b45u8Y4jIbp78AVY/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=0)
  
我还设计了——**必须你确认才会继续。**
这不是走过场，是真的等你点头。
  
如果你觉得范围不对，可以直接说"OAuth也要做"，它会重新调整计划。
  
PS：当然你觉得麻烦，也可以直接说 **不需要问我，你自己全决定**，不过老金我很不建议这么做，因为它不懂你的细节需求，很容易跑偏。
  
### 阶段2：团队组建
你输入"继续"后：
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/2AGkuibewjDpmQB7U8BprAia3BsoLubCmbKKiaLMlj6zBE3vroO2B7ic6gCmEzibT4zIl0LdCkLQesFIJQFLqMTW1hc7tVnqbvT20JXqG0KNDCno/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)
  
**注意模型分工：根据不同的任务，它会选用不同的模型。**
这不是随便选的，是根据任务复杂度自动分配的。
这里动手能力强的，可以把其他模型以MCP，或Agent方式接入。
可以参考老金的开源项目来参考：[对比明明白白3大顶级模型-GPT-5.2/Gemini 3/Claude Opus 4.5！老金告诉你怎么一个窗口全用！](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247490241&idx=1&sn=53cf3464ef761b83b716e95d8a373853&scene=21#wechat_redirect)
  
为了大家方便，这里统一用Cluade旗下的：
haiku处理简单任务（比如格式化、文件搜索），sonnet处理常规编码，opus处理需要深度思考的架构设计。
**同样的活，用对模型能省不少钱。**
  
### 阶段3：并行执行
4个Agent同时启动，各干各的：
![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDpSdIvh0TSdxhZRE3bV3AFVnticAgib2x3RkMcyrLrpWxVSPpibt9qpXG4GMQRM237gDJSs0AYOd21K7bd3gBJsTZ6UZLNNHuZqgo/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=2)
  
如果某个Agent失败了，小问题自行修复，大问题想你给你选项，你自己说解决方案：
![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDpXlsTZFV1vEU60vxXnxoUHYiaEicaf8mbDjNRm4tEiaweQlrJrCSCSahjac3oXOicatS3cpwQ9cy9aFjKEZ1x1tF2GrggBX7AKia2w/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=3)
  
### 阶段4：质量把关 & 产品打磨
这个阶段是Skill的核心——不只是检查"能不能跑"，还要检查"好不好用"：
  
![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDorHHjVfCtBZ0RdScTMHLHyUFGo05aecfrbu0yMBice84CMywUuHzYHrSI7X6opqJODb3wWpIkusbQGZe0z9PbenLodVictibgM0o/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=4)
  
最多打回2轮，2轮还不行就通知你人工介入。
  
**产品打磨三个维度：**
边界处理（异常情况覆盖了没）
专业度（命名规范、错误提示友好不）
完整性（文档、配置、示例齐全不）
  
### 阶段5：结果交付 & 部署移交

![Image](https://mmbiz.qpic.cn/mmbiz_png/2AGkuibewjDqvnicXkXvctafiaXbJsEpKpryeeRjh8O1dGTkajlJh4sgTrU0ib8bZM9q5uEicicvHleK0sXH5EdT0NwfEYK9sQibkqfuaFy31deibKw/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=5)
  
从任务分析到部署移交，全流程自动化。
你只在阶段1确认了一次计划，剩下的全是Skill在驱动。
  
"后续建议"那栏特别有用——它会把阶段1砍掉的"add-later"内容列出来，提醒你下一步可以做什么。
  
如果对你有帮助，记得关注一波~  
  
## 两种协作模式：按需选择

**Subagent模式**
通信方式：单向汇报（Agent→协调器）
适用场景：任务间没有依赖
启动方式：Task工具
  
**Agent Team模式**
通信方式：双向通信（Agent↔Agent）
适用场景：任务间需要互相协调
启动方式：TeamCreate + SendMessage
  
**老金的建议：90%的场景用Subagent就够了。**
Agent Team功能更强但成本更高（每条消息都消耗token），只有任务间真的需要互相沟通时才用。

## 安装前提：先装这两个Skill

这个Skill依赖两个前置Skill才能发挥完整功能，建议先安装：
  
**PS：请手动安装，老金我没放Skill里，否则每次都浪费token**
  
### 必装1：find-skills（自动发现社区Skill）
它是Claude Code生态里的Skill发现工具，能从社区索引中搜索数百个开源Skill。
比如你说"帮我做个数据可视化"，find-skills会自动搜索有没有专门做数据可视化的Skill可以用。
这里直接安装老金改良过的的兼容Win版本。
```
npx skills add KimYx0207/findskill@find-skills -y
```
  
或者手动安装：
仓库地址：https://github.com/KimYx0207/findskill
把SKILL.md放到 .claude/skills/find-skills/ 目录
  
编排器怎么用它？
在阶段2组建团队时，协调器会先问find-skills："当前任务有没有专门的Skill可以用？"
如果有，就给对应的Agent指定这个Skill；如果没有，就用通用agent。
  
老金之前写过一篇find-skills的详细教程：[24小时15.3K安装量稳坐王座！老金愿称之为元Skill！](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247490986&idx=1&sn=bb72a119117760a73f89a6554b0b16b1&scene=21#wechat_redirect)
  
### 必装2：planning-with-files（自动生成计划文件）
```
npx skills add anthropic-ai/planning-with-files -y
```
  
或者手动安装：
仓库地址：https://github.com/OthmanAdi/planning-with-files
把planning-with-files的SKILL.md放到 .claude/skills/planning-with-files/ 目录
  
作用：自动生成task_plan.md、findings.md、progress.md，让任务进度可追踪。
  
### 不装也能用
如果暂时不想装上面两个，agent-teams-playbook也能用：
find-skills不可用 → 自动用本地Skill → 再不可用就用通用Agent
planning-with-files不可用 → 跳过自动生成文件这一步
  
不会报错，只是功能会降级。
  
## 安装使用：3步搞定
### 第1步：创建目录
```
mkdir -p .claude/skills/agent-teams-playbook
```
  
### 第2步：放入SKILL.md
把完整的SKILL.md文件放到这个目录下。
216行，直接复制粘贴就能用。
  
完整代码见GitHub仓库（文末有地址）。
  
### 第3步：触发使用
以下任何一种方式都能触发：
```
# 自然语言触发（随便说）"拉团队帮我重构认证模块""帮我组个Agent团队做代码审查""用多Agent并行处理这个任务""agent swarm帮我搞定这个需求"# 直接指定场景"用场景4帮我做用户系统"
```
  
不需要安装其他依赖，不需要配置环境变量，不需要启动服务。
放进去就能用。
  
## 进阶玩法：和生态工具组合
这个Skill不是孤立存在的，你可以 手动组合 其他工具来增强功能。
Claude Code的Skill生态正在快速成长，老金我逐个讲清楚可以怎么组合。
  
### find-skills：Skill自动发现机制
agent-teams-playbook内置了一个" Skill回退链 "：
```
检测find-skills是否可用    ↓ 可用搜索匹配当前任务的Skill    ↓ 找到调用匹配的Skill执行    ↓ 没找到或不可用回退到本地已安装的Skill    ↓ 本地也没有使用通用流程（general-purpose agent）
```
  
### Superpowers：14个开发工作流Skill
GitHub地址： https://github.com/obra/superpowers
  
这是 Jesse Vincent（obra）做的一套完整的Claude Code开发工作流Skill集合，包含14个独立Skill，覆盖从头脑风暴到代码合并的完整生命周期。
  
你可以参考以下对应关系来组合使用：
  
**brainstorming** → 需求澄清、方案设计 → 建议用于阶段1
**writing-plans** → 拆解任务、写实施计划 → 建议用于阶段2
**dispatching-parallel-agents** → 并行派发Agent → 建议用于阶段3
**subagent-driven-development** → 子Agent驱动开发+两阶段Review → 建议用于阶段3+4
**test-driven-development** → RED-GREEN-REFACTOR → 建议用于阶段3（实现者用）
**requesting-code-review** → 代码审查 → 建议用于阶段4
**finishing-a-development-branch** → 合并/PR/清理 → 建议用于阶段5
  
**关键区别**：superpowers是一个 工具库（14把独立的工具），agent-teams-playbook是一个 编排器（决定什么时候用哪把工具、怎么组合）。
  
打个比方：
**superpowers** = 工具箱里的锤子、螺丝刀、扳手，每个工具干一件事
**agent-teams-playbook** = 项目经理，看了图纸后决定先用扳手再用锤子
  
**它们是互补的，不是竞争的。**
  
组合使用时，在阶段2给不同角色的Agent指定superpowers的Skill：
```
团队蓝图示例：  
架构师 brainstorming + writing-plans  
实现者 subagent-driven-development + TDD  
审查员 requesting-code-review  
收尾员 finishing-a-development-branch
```
  
编排器负责编排（谁先谁后、并行还是串行），superpowers的Skill负责每个角色的具体执行。
  
老金之前写过一篇superpowers的详细教程：[Claude Code 30k+ star官方插件，小白也能写专业级代码](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247490866&idx=1&sn=4e2373158ffadad7877bc66cd04cda0e&scene=21#wechat_redirect)
  
### planning-with-files：计划落地成文件

这俩是 可以互补 的——编排器负责编排决策，planning-with-files负责把计划写成可追踪的文件。
  
**阶段对应关系：**
  
**阶段1：任务分析**
编排器负责：决策场景、选协作模式
planning-with-files负责：生成 task_plan.md
  
**阶段2：团队组建**
编排器负责：分配角色、选模型
planning-with-files负责：生成 findings.md
  
**阶段3：并行执行**
编排器负责：协调多Agent并行
planning-with-files负责：各Agent更新 progress.md
  
**阶段4-5：质量把关+交付**
编排器负责：质量把关+交付
planning-with-files负责：最终状态写入文件
  
建议的操作流程：
```markdown
用户："帮我重构认证模块，拉团队搞"  
  
→ 编排器启动  
→ 阶段1：调 /planning-with-files 生成 task_plan.md + findings.md  
→ 阶段2：基于 task_plan.md 组建团队  
→ 阶段3：各Agent执行，进度写入 progress.md  
→ 阶段4：质量把关，结果写入 findings.md  
→ 阶段5：交付报告，最终状态更新到 task_plan.md
```
  
效果：**计划有文件，执行有团队，进度可追踪。**
你随时打开 progress.md 就能看到每个Agent干到哪了。
  
老金之前写过一篇planning-with-files的详细教程：[Claude Code神器：Manus同款文件规划法，价值20亿美元的工作流秘密](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247491182&idx=1&sn=7e029a631a922c1890fc6cee0557832d&scene=21#wechat_redirect)
  
### TDD工作流：代码有测试保障

在阶段2组建团队时，你可以给实现者Agent指定TDD专用的subagent_type，让每个实现者自动遵循测试驱动开发。
  
**组合场景：**
团队里有"实现者"角色，给实现者指定 subagent_type: "everything-claude-code:tdd-guide"
需要写新功能，实现者按TDD流程：写测试(RED) → 实现(GREEN) → 重构(IMPROVE)
质量把关阶段，自动检查测试覆盖率是否≥80%
  
**建议的团队蓝图：**
1、架构师 设计接口和模块划分 模型：opus subagent_type：Explore
2、实现者A TDD实现认证模块 模型：sonnet subagent_type：everything-claude-code:tdd-guide
3、实现者B TDD实现权限模块 模型：sonnet subagent_type：everything-claude-code:tdd-guide
4、审查员 Code Review 模型：sonnet subagent_type：everything-claude-code:code-reviewer
  
注意看subagent_type那列——实现者用 tdd-guide（自动走TDD流程），审查员用 code-reviewer（自动做代码审查）。
不是你手动告诉它"请用TDD"，而是Agent类型本身就决定了工作方式。
  
老金之前写过一篇everything-claude-code的详细教程：[黑客松冠军配置！老金拆解8大核心思路，值得反复品味](https://mp.weixin.qq.com/s?__biz=MzI0NzU2MDgyNA==&mid=2247491087&idx=1&sn=5f5ff37587b13eaab8959fddc0e39b8e&scene=21#wechat_redirect)
  
  
### 终极组合：五件套

把上面所有工具串起来（ 注意：这需要用户手动安装各个Skill先 ）：
```
find-skills            →  自动发现可用的社区Skill  
        ↓  
planning-with-files    →  生成结构化计划文件  
        ↓  
agent-teams-playbook   →  基于计划文件组队+编排  
        ↓  
superpowers skills     →  每个Agent用专业Skill执行  
        ↓  
tdd-guide              →  实现者遵循TDD流程
```
  
**Skill自动发现、计划有文件、编排有策略、执行有专业Skill、代码有测试。**
  
老金我的建议是：你可以按需组合这些工具。
agent-teams-playbook作为编排器，负责决策什么时候用哪个工具、怎么组合。
其他工具作为"专业Agent"或"Skill"被编排器调用。
  
这套组合的理想效果是，你说一句"帮我做个用户系统"，Claude Code会：
  
1、find-skills搜索有没有专门做用户系统的Skill
2、planning-with-files生成计划文件
3、编排器判断场景、组建团队
4、架构师用brainstorming设计方案
5、实现者用TDD写代码
6、审查员做Code Review
7、收尾员处理合并和部署
  
你只需要在关键节点点头确认。
  
## 设计原则：老金踩过的坑

做这个Skill的过程中，老金我踩了不少坑，总结了7条底线写进了代码里：
  
1、 先目标，后组织结构 ——任务不清晰时先澄清，不急着组队
2、 队伍不超过5个 ——Agent多了反而乱，沟通成本指数级增长
3、 关键节点必须有质量闸门 ——不能Agent干完就算完
4、 不默认任何工具可用 ——find-skills不一定装了，执行前先验证
5、 成本只是约束，不是承诺 ——不做"省50%成本"这种不切实际的预估
6、 遇到问题给选项 ——不替用户做决定，让用户选
7、 危险操作必须确认 ——大规模变更、删除操作，必须用户点头
  
这7条是从实际使用中总结出来的，每一条背后都有一个翻车故事。
  
## 开源地址

仓库地址：https://github.com/KimYx0207/agent-teams-playbook
Skill完整代码只有216行，放进 .claude/skills/agent-teams-playbook/SKILL.md 就能用。
不需要任何依赖，不需要任何配置。
  
装了它，编排器的Skill回退链就能自动发现社区Skill。
  
## 老金往期教程导航

这套Agent Teams生态，老金我之前每个工具都写过详细教程，按照从框架到成员的顺序排好了：
  
1、 Agent Teams多Agent协作 框架主力军
教程地址：https://my.feishu.cn/wiki/CAsLwb5BBiVztakm0h9cGnucnRc
2、 everything-claude-code 专业Agent类型库
教程地址：https://my.feishu.cn/wiki/Jqf3wAPh2ixRifkssykcnNJOnkg
3、 find-skills 最关键的成员
教程地址：https://my.feishu.cn/wiki/HVU6wTCRoiThWAkgyLac7w3Yncb
4、 planning-with-files 计划落地文件
教程地址：https://my.feishu.cn/wiki/QG5Aw1fpZikJP5kHyhrcwRlznMc
5、 superpowers 14个执行Skill
教程地址：https://my.feishu.cn/wiki/Y2gqwjSQbiygUgkNuvMcEU4nnLd
  
建议阅读顺序：先看1了解框架全貌 → 再看3掌握Skill发现机制 → 然后2、4、5按需深入。
  
评论区聊聊，老金我每条都看。
  
觉得有用的话，转发给你身边用Claude Code的朋友，别让他们还在手动拼提示词。