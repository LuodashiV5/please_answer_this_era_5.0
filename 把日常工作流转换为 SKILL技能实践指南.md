
## 前言

我们了解了一系列的skills技能相关的技术知识，但是在项目开发中，却发现没有啥用武之地，个人感觉我日常开发项目不需要，没必要，找不到做的必要性。

出现这些想法或者个人的意愿与看法，其实都是因为我们开发人员都在忙碌一个项目之后接着下一个项目，每天都是这样的忙碌着，其实我们后面就缺乏了系统性的总结。

又或者每天事情很多的，不是工作就是家庭，想想都很累，哪还有功夫去总结日常琐碎的项目开发中遇到的一系列问题。我们牛马需要的是休息...

但是这些想法在以前AI没有出来之前，我觉得也是没有问题的，因为历史代码都在，大不了后面花时间找呗！AI出来之后，我们需要做真正重要的事情，其他的事情都直接交给AI来实现就可以了，我们开发人员需要打造我们自己的核心竞争力，做一些AI不能的事情，如何还是重复之前的AI都能做的，那么我们迟早会被时代所淘汰。

AI时代的程序开发工程师们，我们需要重新定义我们自己。

## 为什么要把工作流变成 SKILL？

每次用 AI 都要重新解释一遍背景，写一长串 Prompt，拿到结果就走。第二天遇到同样的事，又得再来一遍。而有时候往往一样的提示词，给出的结果还是不一样的，于是又要开启新的一轮或者N轮对话，才能解决问题。这样用 AI 效率太低，我们处于被动的不理局面，太影响心情了。

把工作流固化成 SKILL 后，你只需要说一句话，AI 就能自动跑完整个流程。比如"发布这篇文章到微信公众号"，AI 就知道要读取文件、转换格式、调用 API、处理错误，不需要你一步步指导等等。

## 什么时候值得做成 SKILL？

不是所有任务都值得做成 SKILL。满足这些条件的才值得：
1. **重复发生**：一周至少用 3 次以上
2. **流程固定**：步骤基本不变，只是输入不同
3. **容易出错**：手动操作容易漏步骤或出错
4. **有明确边界**：能说清楚什么时候用，什么时候不用

举个例子：每次发布文章到微信公众号，都要读取 Markdown、转换 HTML、上传图片、调用 API。这个流程固定，但容易漏步骤。做成 `wechat-publisher` 技能后，一句话就搞定。

反例：偶尔写个邮件、查个资料，这种一次性的任务不值得做成 SKILL。

## 工具和资源

### skill-writer：帮你写 SKILL.md

`skill-writer` 是项目中的技能，专门帮你创建新技能。它会：
- 问你技能要做什么、什么时候用
- 帮你生成符合规范的 `SKILL.md` 文件
- 指导你设计技能结构

用法很简单，直接对 Claude 说：
```
使用 skill-writer 技能，帮我创建一个代码审查助手技能
```
`skill-writer` 会问你几个问题，然后生成 `SKILL.md` 的框架，你再填充具体内容。
### skill-creator：完整的技能脚手架

`skill-creator`提供了更完整的技能创建流程（因为官方出品），包括：
- `init_skill.py`：自动创建技能目录结构
- 标准的文件夹组织（scripts/、references/、assets/）
- SKILL.md 模板

如果你要创建复杂的技能（需要多个脚本、模板文件），用 `skill-creator` 更省事。

### MCP：连接外部世界

MCP（Model Context Protocol）让 Claude 能调用外部服务。常用的 MCP 包括：
- **GitHub MCP**：操作 Issue、创建 PR、搜索代码
- **Playwright MCP**：自动化测试网页、抓取数据
- **数据库 MCP**：查询数据库、执行 SQL

如果你的工作流需要调用外部 API、操作数据库，需要配置对应的 MCP 服务器。

### CLAUDE.md 和 AGENTS.md, Cursor rule：项目记忆

这两个文件不是技能，而是 Claude 的"长期记忆"：

- `CLAUDE.md`：项目级别的规范，比如编码风格、测试命令、构建流程
    
- `AGENTS.md`：你的个人偏好，Claude 出错被你纠正后，会记录在这里
    
- `Cursor rule` :  你项目中的一系列规范与要求，一一记录下来
    

这三个文件在不同的AI编辑工具中, 都会记住项目的上下文，不用每次都解释,AI会自动取读取。

但是我个人认为，在我们平时的日常开发过程中，如果你基本上就是给一句或者几句提示词，然后加图片加一下功能性的描述，直接告诉AI你要什么，如果都是这样的流程，项目记忆有时候其实并没有用到。

一般上述的这些所谓的项目记忆，比较适合团队开发，项目规范这样比较适合，纯粹个人的 vibe coding 其实无所谓是否需要，看你个人的选择，有些人可能觉得有总比没有好，那么就加，有些人觉得不需要，那么就是不需要加。

## 如何把工作流转换成 SKILL：四步法

### 第一步：识别工作流的边界

先搞清楚这个工作流：
- 输入是什么？（文件路径、参数、配置）
- 输出是什么？（生成的文件、API 调用结果、状态变更）
- 什么时候用？（触发条件）
- 什么时候不用？（边界情况）

举个例子，代码审查助手：
- 输入：代码文件路径
- 输出：审查报告（Markdown 格式）
- 触发：用户说"审查这段代码"、"检查代码质量"
- 边界：只审查代码，不修改代码

### 第二步：拆解成具体步骤

把工作流拆成可执行的步骤，每个步骤都要明确：
- 做什么
- 需要什么工具（读文件、执行脚本、调用 API）
- 可能出什么错
- 出错怎么处理

比如 wechat-publisher 的步骤：
1. 读取 Markdown 文件（可能文件不存在）
2. 获取微信 Access Token（可能配置错误）
3. 上传封面图（可能图片格式不对）
4. 转换 Markdown 为 HTML（可能格式有问题）
5. 调用 API 创建草稿（可能网络错误）
每个步骤都有错误处理，脚本用退出码告诉 AI 成功还是失败。

### 第三步：设计技能结构

根据复杂度选择结构：

**简单技能**（只有指令，不需要脚本）：
```markdown
skill-name/
└── SKILL.md
```

**中等技能**（需要辅助脚本）：
```markdown
skill-name/
├── SKILL.md
└── scripts/
    └── helper.py
```

**复杂技能**（需要多个资源）：
```markdown
skill-name/
├── SKILL
.md
├── scripts/
│   └──main.py
├── templates/
│   └── template.html
└── reference.md
```

### 第四步：编写 SKILL.md

`SKILL.md` 是技能的核心，要写清楚：
1. **name 和 description**：技能叫什么，做什么，什么时候用
2. **触发条件**：用户说什么话时激活这个技能
3. **工作流程**：具体怎么做，分几步
4. **工具使用**：需要什么工具（Read、Write、run_command 等）
5. **错误处理**：可能出什么错，怎么处理
description 字段特别重要，要包含"何时使用"的信息。比如：
```markdown
description: 将 Markdown 内容一键推送到微信公众号草稿箱。支持自动排版、样式映射以及草稿管理。用户提到"发布到公众号"、"存为草稿"、"同步到微信"时激活此技能。
```
这样 Claude 才知道什么时候该用这个技能。

## 常见问题和避坑

### 问题 1：技能不生效

可能原因：
- `SKILL.md` 的 description 没有包含触发词
- 技能放在错误的位置（应该放在 `.claude/skills/` 或 `~/.claude/skills/`）
- 需要重启 Claude Code 才能发现新技能
解决：检查 description 字段，确保包含用户可能说的话。如果是新技能，重启一下。

### 问题 2：脚本执行失败但没有报错

脚本要有明确的退出码：
- `sys.exit(0)` 表示成功
- `sys.exit(1)` 表示失败
如果脚本失败但返回 0，AI 会以为成功了，不会重试。

### 问题 3：技能太复杂，一次做不完

先做最小版本，能跑通就行。比如代码审查助手：
- 第一版：只检查语法错误
- 第二版：加上代码风格检查
- 第三版：加上性能建议
不要一开始就想做个"全能神"。

### 问题 4：不知道怎么写 SKILL.md

用 `skill-writer` 辅助。直接说：
```markdown
使用 skill-writer 技能，帮我创建一个 [你的技能名] 技能
```
`skill-writer` 会问你问题，然后生成框架，你再填充细节。

### 问题 5：需要调用外部 API 但不知道怎么做

两种方式：

1. **用脚本**：在 `scripts/` 目录写 Python/Shell 脚本，脚本里调用 API
2. **用 MCP**：如果 API 使用频繁，可以做成 MCP 服务器
简单的一次性调用用脚本，复杂的、需要状态管理的用 MCP。

## 基础实战：创建一个简单的技能

假设你要做一个"自动生成 Git Commit Message"的技能（AI编辑工具中基本上都直接集成了）。但是如果我们是gemini 3, claude code 这类命令行工具的话，如果你要对提交代码信息做一些特殊的要求的话，那么还是需要这么一个技能的。实际上在我们团队开发合作项目的时候，定制化的代码提交信息非常有利于后面部分代码合并，这样容易找自己写的或者一堆项目中的某些功能代码。

### 1. 确定需求
- 输入：当前 git diff
- 输出：符合规范的 commit message
- 触发：用户说"生成 commit message"、"写提交信息"
### 2. 创建技能目录

```markdown
mkdir -p .claude/skills/commit-message-generator
```
### 3. 写 SKILL.md
```markdown
---
name: commit-message-generator
description: 根据 git diff 自动生成符合规范的 commit message。用户提到"生成 commit message"、"写提交信息"、"自动提交"时激活此技能。
---

# Commit Message Generator

## 工作流程

1.
 读取当前 git diff（使用 run
_command 执行 `git diff`）
2. 分析变更内容（新增、修改、删除的文件和代码）
3. 生成符合 Conventional Commits 规范的 commit message
4. 输出格式：`<type>(<scope>): <subject>`

## 示例

用户说："生成 commit message，信息标题前置XXX"，那么提交的git信息的前面就需要加上 XXX -

比如说：生成 commit message，git信息前置登录页面，那么提交的git信息就是类似：feat(api)：登录页面-页面初始化与接口对接

Claude 会：

1. 执行 `git diff` 获取变更
2. 分析变更内容
3. 生成类似 "feat(api): XXX - 添加用户登录接口" 的 message
```

### 4. 测试

直接对 AI 编辑工具 说："使用 commit-message-generator 生成 commit message,信息标题前置XXX"，看是否能正常工作。

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/0Zpa7MXnIPHnsPM4ZRGkrHTwALTtaibIeibPCBGMGSLP3cVL2bgwaH6n0Gvg7OFlErP0bV1V9zzOjXDHwNEtqibBQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/0Zpa7MXnIPHnsPM4ZRGkrHTwALTtaibIeUlm4nPMOq0a4QnVW9dUmcOXib9rMsL0aCrcP2rLZDnJzjLOqNNnLQDQ/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

### 5. 迭代优化

如果生成的 message 不够好，修改 `SKILL.md`，增加更多规则和示例。

## 进阶技巧

### 组合多个技能
一个复杂任务可能需要多个技能配合。比如发布文章到多个平台：
- `wechat-publisher`：发布到微信公众号
- `test-red-book`：生成小红书风格文案
- `docx`：生成 Word 文档
Claude 可以自动组合这些技能，完成整个流程。
### 使用脚本处理复杂逻辑

如果工作流有复杂的逻辑（比如正则替换、数据转换），写在脚本里，不要全写在 `SKILL.md` 里。
比如 wechat-publisher 的 Markdown 转 HTML，用了正则表达式注入内联样式，这种逻辑写在 Python 脚本里更合适。
### 配置和敏感信息
敏感信息（API Key、密码）不要写死在代码里，用 `.env` 文件：
```markdown
import os
import os
from dotenv import load_dotenv
load_dotenv()
app_id = os.getenv('WECHAT_APP_ID')
```
然后在 `.env` 文件里配置：
```markdown
WECHAT_APP_ID=your_app_id
```

记得把 `.env` 加到 `.gitignore`，不要提交到代码库。
## 总结

把工作流做成 SKILL 的核心思路：
1. **识别重复**：一周用 3 次以上的任务值得做成技能
2. **明确边界**：说清楚什么时候用、什么时候不用
3. **拆解步骤**：把工作流拆成可执行的步骤，每个步骤都有错误处理
4. **最小闭环**：先做能跑通的版本，再慢慢加功能
5. **善用工具**：用 `skill-writer` 辅助写 `SKILL.md`，用脚本处理复杂逻辑

如果你发现某个操作一天内重复了 3 次，停下来，把它做成技能。这样你就能把时间花在更有价值的事情上。

AI时代下，我们需要紧跟着AI的潮流去不断地逐浪，不断地尝试新的东西，沉淀知识与能力，改变自己，再去创造自我价值并实现自我价值。

---