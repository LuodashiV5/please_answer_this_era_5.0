这是一篇写给完全没有编程经验的朋友的攻略。

如果你刚开始用Cursor，甚至不太确定"终端"是什么，没关系，跟着一步步来就行。

# 先说几个基础概念

# 哪些软件支持Skill

目前支持Agent Skill功能的主流软件有：

|软件|简介|Skill存放位置|
|---|---|---|
|**Cursor**|基于VS Code的AI代码编辑器，本文重点介绍|`~/.cursor/skills/`|
|**Claude Code**|Anthropic官方的AI编程工具（命令行版）|`~/.claude/skills/`|
|**Open Code**|类似于Claude Coded的开源编程工具|类似Cursor的目录结构|

这几个软件的Skill格式基本一致，都是用SKILL.md文件来定义。所以你在Cursor里写的Skill，稍微改改路径，也能在Claude Code里用，而且他们都是支持Claude Code的安装路径的，所以本期文章中也会按照Claude Code的Skill安装。

本文以Cursor为例，但思路是通用的。

# Cursor是什么

Cursor是一个写代码的软件（专业术语叫"IDE"），但它和普通的代码编辑器不一样——它内置了AI助手。

你可以在里面跟AI对话，让它帮你写代码、改代码、解释代码。有点像把ChatGPT塞进了一个代码编辑器里。

如果你还没安装Cursor，去下载安装。过程很简单，跟装普通软件一样。

下载地址：

https://cursor.com

# Skill是什么

打个比方：假设你请了个新助理，每次让他帮你写周报，都要说一遍：

格式要用这个

重点写这几项

别用'赋能''颠覆'这种词

说一次两次还行，天天说就烦了对吧？

Skill就相当于你写了一份《周报撰写指南》交给助理。

以后只要说"帮我写周报"，他就知道怎么做了。不用每次都从头交代，也不用担心他忘了。

技术上讲，Skill就是一个文本文件，里面写清楚：

这个技能是干什么的

什么时候该用它

具体怎么执行

# Markdown是什么

Markdown是一种写文档的格式，比Word简单很多。

它用一些简单的符号来表示格式，比如：

```markdown
 
</span> 表示一级标题</span></code><code><span leaf="">
<span class="code-snippet__string">## 小标题</span>&nbsp;表示二级标题</span>
</code><code><span leaf=""><span class="code-snippet__string">加粗</span>&nbsp;表示加粗</span></code><code>
<span leaf=""><span class="code-snippet__string">- 内容` 表示列表项
 
```

你不用专门学Markdown，看几眼就能懂。Cursor自带Markdown预览，写的时候能实时看效果。

# 安装配置：保姆级教程

第一步：确认Cursor是最新版

打开Cursor，然后：

Mac用户：按键盘上的 Cmd 和Shift和J

Windows用户：按键盘上的 Ctrl 和 Shift和J

会弹出Cursor设置页面。点击左侧的Rules，确认是否有Skill的模块。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/ciaSWdic6kWmgaRU37DldvOLpJfPvwL6HqTgAwwmSfj8pDY2M1kqzeSsP2icuD6HPwskcpG4Hov7zeichupHNEmWtA/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=0)

如果有，则说明你的版本没问题，如果没有，则需要去下载最新版。

第二步：找到存放Skill的位置

Skill文件要放在特定的文件夹里，Cursor启动时会自动去找。

这个文件夹的位置是：

|系统|文件夹路径|
|---|---|
|Mac|`/Users/你的用户名/.claude/skills/`|
|Windows|`C:\Users\你的用户名\.claude\skills\`|

"你的用户名"是什么？

就是你登录电脑用的账户名。比如你的电脑登录名是"xiaoming"，那路径就是：

Mac: /Users/xiaoming/.claude/skills/

Windows: C:\Users\xiaoming/.claude\skills\

第三步：创建Skill文件夹

这一步我们要创建存放Skill的文件夹。我分别说Mac和Windows怎么操作。

Mac用户操作方法

方法一：用访达（Finder）创建（推荐新手）

1. 打开"访达"（Finder）

2. 按键盘Cmd + Shift + G，会弹出一个输入框

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/ciaSWdic6kWmhghskZDTKtGM2VtELEwJExN2Y5HAjCh7bl068XruI0WIGkYtzDLGSv3FsWpA1YBGrlkPCqLxLDhQ/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=1)

3. 输入~/.claude然后按回车

4. 如果提示"文件夹不存在"，说明还没创建过，继续下一步

5. 按Cmd + Shift + G，输入~按回车，进入你的用户目录

6. 按Cmd + Shift + .（句号），显示隐藏文件夹

7. 右键空白处 → 选择"新建文件夹" → 命名为.claude

8. 双击进入.clauder文件夹

9. 再新建一个文件夹，命名为skills

到这里，skills文件夹就创建好了。

方法二：用终端创建

1. 打开"终端"应用

2. 复制粘贴下面这行命令，然后按回车：

```markdown
mkdir -p ~/.claude/skills
```

这一行命令会自动创建整个文件夹结构。

---

Windows用户操作方法

方法一：用文件资源管理器创建（推荐新手）

1. 按Win + E打开文件资源管理器

2. 在地址栏（顶部显示路径的地方）点一下，输入：

```
%USERPROFILE%
```

然后按回车。这会带你到你的用户文件夹。

3.显示隐藏文件夹（重要！）

4. 看看有没有.claude文件夹

5. 在.claude里新建文件夹，命名为skills

到这里，skills文件夹就创建好了。

方法二：用PowerShell创建

1. 按Win + X，选择"Windows PowerShell"或"终端"

2. 复制粘贴下面这行命令，按回车：

```
New-Item-ItemType Directory -Force-Path"$env:USERPROFILE\.claude\skills"
```

---

第四步：创建你的第一个Skill

现在来创建一个实际的Skill——周报助手。

创建文件夹：

在skills文件夹里新建一个文件夹，命名为weekly-report-helper。

让AI帮你写SKILL.md：

1. 用Cursor打开weekly-report-helper文件夹

2. 按Cmd+L（Mac）或Ctrl+L（Windows）打开Chat对话框

3. 对AI说：

```markdown
帮我创建一个Cursor Agent Skill，用来写周报。要求： 
- 文件名：SKILL.md- 技能名称：weekly-report-helper 
- 功能：帮我把本周工作整理成周报格式 
- 周报格式要包含：已完成、进行中、下周计划 
- 语言要简洁，不要用"赋能"、"抓手"这种词

```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/ciaSWdic6kWmhghskZDTKtGM2VtELEwJExgYzvKgdJE1uY6KP42QQmudJ9hJGKmcEKe7DmHMIiaiae8NjGF9WgCnrQ/640?wx_fmt=png&from=appmsg&tp=wxpic&wxfrom=5&wx_lazy=1&watermark=1#imgIndex=2)

4. AI会生成一个完整的SKILL.md文件

5. 点击AI回复中的"Apply"或"应用"按钮，文件就自动创建好了

6. 按Cmd+S/Ctrl+S保存

7. 重启Cursor

如果AI生成的内容不满意，可以继续对话让它修改。

比如：

• "格式再简化一点"

• "加一个'本周亮点'的部分"

• "触发关键词再多加几个"

改到满意为止，然后保存。

---

# 测试你的Skill

**1.重启Cursor**

**2.打开Chat对话框**

**3.测试一下**

如果AI还是用它自己的方式回复，可能是：

• 文件名不对（必须是SKILL.md，大小写敏感）

• 头部格式有问题（检查三个短横线有没有打对）

• 没有重启Cursor

---

# 常见问题

Q：Skill文件夹在哪里？我找不到

**Mac**：按Cmd + Shift + G，输入 '~/.claude/skills'

**Windows**：在文件资源管理器地址栏输入%USERPROFILE%\.claude\skills

如果提示不存在，说明你还没创建这个文件夹，回到前面"第三步"创建。

Q：我按教程做了，但Skill没生效

检查以下几点：

**1.文件名对不对**

**2.头部格式对不对**

**3.重启Cursor了吗**

Q：name和文件夹名不一样会怎样

可能导致Skill无法被识别。保险起见，让它们保持一致。

比如文件夹叫weekly-report-helper，那name也写weekly-report-helper。

Q：description写什么最有效

包含两类信息：

1. 这个Skill做什么："帮助用户撰写周报"

2. 什么时候触发："当用户说'帮我写周报'、'整理本周工作'时使用"

关键词越具体，AI越容易判断什么时候该用这个Skill。

Q：正文可以写多长

建议控制在500行以内。

太长的话会占用AI的"记忆空间"（专业术语叫token），影响对话质量。

如果内容确实很多，可以拆成多个文件，在SKILL.md里引用。这个属于进阶用法，先不用管。

---

# 总结

装好Cursor之后，配置Skill的核心步骤：

1.创建skills文件夹：在.claude/下面建skills文件夹

2.创建你需要的Skill：告诉AI你想要什么技能，让它帮你生成SKILL.md

3.重启Cursor：让它加载新的Skill

Skill的核心价值：把你的工作习惯教给AI，让它越来越懂你。

从简单的开始——周报、会议纪要、邮件模板——慢慢积累，你会发现AI帮你干活越来越顺手。

---

我是瓦力，AI时代的观察者和翻译者。用技术视角看生活，用生活语言讲技术。