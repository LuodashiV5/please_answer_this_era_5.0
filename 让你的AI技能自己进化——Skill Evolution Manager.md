大家好，我是小松鼠。
  
一名AI时代的学习者，专注探索个体在新时代的生存模式。
  
这是我的 第 45 篇 AIGC文章。
  
---
  
  
最近在用Claude Code的时候，接触了很多的Skills。
  
有些时候，我们希望把自己的一些改进快速添加到skills里面，避免下次使用时又去纠错提示词。
  
比如我用某个下载视频的Skill，第一次踩了坑——Windows系统路径要用双引号包裹。
好不容易搞定了，下次再用，同样的问题又来一遍。
  
  
后来我看到卡兹克，分享了一个他的技能：Skill Evolution Manager（技能进化管理器）。
  
本着尝试的心态，我去体验和优化了这个技能，和大家分享下。
  
讲清楚这个工具是干嘛的，怎么用，以及我对它做了哪些改进。
  
  
---
  
## 一、先搞清楚它解决什么问题
  
我们先说一个场景。
还是我开篇提到的下载视频的例子。
  
在用Claude Code帮你下载YouTube视频，用的是yt-dlp这个Skill。
  
第一次用，报错了。
  
折腾半天发现：哦，Windows下路径要转义。
  
你当场告诉AI这个经验，AI帮你解决了。
  
完美~~
  
但问题来了——
  
下周你再用这个Skill，同样的问题，AI又不知道了。
  
😀每次对话都是新的，AI没有"长期记忆"。
你的经验没有被保存下来。
  
打个比方：
这就像你每天上班都换来一个新同事（为什么频繁换同事，你别问）
昨天那个同事知道老板喜欢喝美式咖啡，今天这个同事不知道，又给老板买了拿铁。
  
Skill Evolution Manager就是来解决这个问题的——它帮你把"老板喜欢美式"这个经验写到交接文档里。
  
新同事来了先看文档，就不会犯同样的错。
  
  
---
  
## 二、它的核心设计思路
就是三步：
  
1️⃣ 提取经验 → 你和AI对话中踩的坑、总结的技巧，AI帮你提取出来
  
2️⃣ 保存到文件 → 把这些经验写到一个专门的文件里
  
3️⃣ 缝合到说明书 → 把经验自动追加到Skill的说明文档末尾（相当于对文档进行了补充示例）
  
![Image](https://mmbiz.qpic.cn/mmbiz_gif/aggHiadswE03TUfFClCufiaEbcPYLDgaJ1OY5dANazb0picYHv8P4ficm0E53JK3ibev931oeos0xticet2nZtnVmdMGMb4josxWMSmfNfatjsGk0/640?wx_fmt=gif&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)
  
  
这样设计有个好处：
  
Skill本身可能会更新版本（就像软件升级），但你的经验是独立存储的。
  
即使Skill升级覆盖了文档，你的经验还在那个文件里，重新"缝合"一下就恢复了。
  
  
---
  
## 三、怎么安装和使用
### 怎么安装？
  
```
帮我从git仓库安装这个技能
git clone https://github.com/qc932493926/Khazix-Skills.git
```
  
### 怎么触发？
很简单，对话快结束时说一句：
  
- "/evolve"
- "复盘一下刚才的对话"
- "把这个经验保存到Skill里"
AI就会自动分析你们的对话，找出哪些经验值得保存，然后帮你存起来。
  
就像下班前写个工作日志，把今天踩的坑记录下来。
  
  
---
  
## 四、我对它做了哪些改进
  
原版的设计有几个问题，我做了针对性优化~~
  
### 问题1：新增的经验没有对话背景
  
原来的经验就是一行文字，没有任何对话的信息。
  
这条经验是什么时候加的？验证过几次了？全都不知道。
  
就像笔记本上乱写的便签，时间久了自己都忘了这是啥。
  
我改成了每条经验都有完整记录：
- 什么时候添加的
- 针对哪个版本的Skill
- 被验证过几次（验证次数越多，越可信）
### 问题2：Skill更新后经验可能过时
  
假设你有一条经验是针对旧版本的。
  
后来Skill升级了，那条经验可能已经不适用了。
  
原版没有任何机制检测这个问题。
  
我新增了一个检测脚本——它会对比版本，自动标记可能过时的经验。
  
经验的生命周期是这样的：
![Image](https://mmbiz.qpic.cn/mmbiz_png/aggHiadswE01ficQQunIFeey7MYtZxJUJurVIBPmesaia73sgfP0lgleqRoLMicoJ1pbdBJnHwTYp94ibia8gaLe7lkGGSz1DRjqQ9T1hfkEpwHibc/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)
  
### 问题3：经验可能重复或矛盾
  
用久了，你可能加了两条差不多的经验：
  
- "下载时默认静音"
- "视频下载不要声音"
语义一样，但文字不同，系统不会去重。
  
更糟糕的是矛盾的情况：
- "默认1080p"
- "默认720p"
  
到底听谁的？==
  
我新增了一个冲突检测脚本——它会找出重复和矛盾的经验，让你决定保留哪个。
  
---
  
  
## 五、我优化后的技能结构
### Skills的文件结构
  
这个技能本身的结构：
  
```
```
skill-evolution-manager/
├── SKILL.md              # 说明书
└── scripts/              # 6个核心脚本    
		├── merge_evolution.py     # 合并经验    
		├── smart_stitch.py        # 缝合到文档    
		└── ...（其他4个）
```
```
我这里截取最新的SKILL.md文件来说明功能。
```
---
name: Skill Evolution Manager
description: 专门用于在对话结束时，根据用户反馈和对话内容总结优化并迭代现有 Skills 的核心工具。它通过吸取对话中的”精华”（如成功的解决方案、失败的教训、特定的代码规范）来持续演进 Skills 库。
license: MIT---

# Skill Evolution Manager
这是整个 AI 技能系统的”进化中枢”。它不仅负责优化单个 Skill，还负责跨 Skill 的经验复盘和沉淀。
## 核心职责
1.**复盘诊断 (Session Review)**：在对话结束时，分析所有被调用的 Skill 的表现。
2.**经验提取 (Experience Extraction)**：将非结构化的用户反馈转化为结构化的 JSON 数据（`evolution.json`）。
3.**智能缝合 (Smart Stitching)**：将沉淀的经验自动写入 `SKILL.md`，确保持久化且不被版本更新覆盖。
4.**经验注入 (Experience Injection)**：调用 Skill 前自动注入相关的已验证经验。
5.**版本验证 (Version Validation)**：Skill 更新后检测并标记可能失效的经验。
6.**冲突检测 (Conflict Detection)**：识别语义重复或矛盾的经验并协助解决。
## 使用场景
**Trigger**:
-`/evolve` - 复盘当前对话
- “复盘一下刚才的对话”
- “我觉得刚才那个工具不太好用，记录一下”
- “把这个经验保存到 Skill 里”
- “检查一下哪些经验过期了”
- “清理重复的经验”
## 数据模型 (Evolution Data Schema)
### 新版结构化数据模型
每条经验都是一个完整的条目，包含溯源和验证信息：
Json结构定义......
```
关键点：经验不是存在这里，而是存在每个被管理的技能自己的文件夹下。
  
比如你给yt-dlp保存了经验，它会变成：
  
```
yt-dlp/
├── SKILL.md           # 末尾会多一个"经验章节"，指向目录下的evolution.json
└── evolution.json     # 你的经验数据（新增）
```
  
  
### 执行流程
  
```
你说 "/evolve"    
	↓
AI 分析对话，判断是哪个 Skill（根据你调用了什么、报错信息等）    
	↓
merge_evolution.py → 把经验写入目标Skill的 evolution.json    
	↓
smart_stitch.py → 把 JSON 转成 Markdown，追加到 SKILL.md 末尾    
	↓
完成！下次AI读取这个Skill时就能看到你的经验了
  
```
  
注意哦，这里是AI自己根据上下文来判断用的哪个技能，
如果AI判断不准，你也可以明确说："把这个经验保存到 yt-dlp 里"。
  
  
### 实际演示效果

还是我们所说的yt-dlp技能
![Image](https://mmbiz.qpic.cn/mmbiz_png/aggHiadswE024fIicpiaeHYOxpiaaX4cNvTF5IVJC1Nl8aegYF9Vzgib5sbg64cgw1yicEuzhiaicvB7o69wkpvQWUt8lq4rG4SPoicLTgXrbVbXbcFE/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)
使用后，可以看到
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/aggHiadswE03SibpfKJWicKqTZic6WeeRGTnT5Vyq6icu7CKZtSvRmJRo7MaXUibR3I6ZCiaMnOnyxFmDJicwibgfM3Xl79x5icz8BuJzZB9ep46z2JL0/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=3)
![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/aggHiadswE00zGxc6jpJ5UZXY9eGu3e4pV4XFZrpVh2wPP6xx6GVQmAc4AL7k0MXPvic4qJHD0UXia0ppA6Zl2dJBocZrw7T3NpficJeyvuwRh0/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4)
  
  
### 适合的场景
#### 场景1：你经常用某个Skill，踩过很多坑
  
比如你天天用AI帮你写代码、下载视频、处理图片。
  
每次踩坑后说一句"/evolve"，把经验攒起来。
  
下次再用，AI就不会犯同样的错。
  
#### 场景2：你想让AI记住你的偏好
  
比如你喜欢代码注释用中文、下载视频默认最高画质、生成图片要16:9比例。
  
这些偏好保存下来，AI以后自动遵守。
```
把这个经验保存一下
```
  
#### 场景3：Skill升级后想保留老经验
  
Skill作者更新了版本，你的定制经验不会丢。
(evolution.json是独立存在的，不会随着技能升级后SKill.md被覆盖而消失)
  
运行一个命令，经验自动恢复到新版文档里。
  
  
---
  
## 尾声
  
这个工具的本质，是让AI拥有"长期记忆"的一种变通方案。
  
现在的大模型还做不到真正的持久化记忆，我们只能用文件来曲线救国。
  
但这已经够用了。