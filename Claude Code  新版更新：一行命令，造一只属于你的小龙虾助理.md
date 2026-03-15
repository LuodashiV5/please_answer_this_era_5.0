最近 OpenClaw（小龙虾）已经破圈了挺火的。简单说就是一个开源的自主 Agent 框架，扔后台让它自己跑，盯 GitHub、回邮件、甚至有老哥拿它砍价买车省了 4200 美元。听着挺香。

但真上手你会发现，对开发者来说还是有点重——得学它的 skill 格式、配消息通知、单独搞一套运行环境。

![Image](https://mmbiz.qpic.cn/sz_mmbiz_png/0IWNK9y4M28bblqoUkn216Y2xH24ialnNqVCzjN0wc7Bpicm8xbYbtcC3lZxC7Y9wCUKsIAzLflBML5ZiaHC7j5xSFSP9DzRpfYlXmPKv4NngY/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

Claude Code v2.1.71 的 changelog 里提到了一个叫 `/loop` 的命令，但更新完发现压根找不到入口。直到今天升级到 v2.1.72，这个功能才真正可用。试了一下——嘿，在自己最熟悉的终端里，一行命令就能搓出一只"小龙虾"。

今天聊聊怎么用 /loop 把 Claude Code 变成一个能自己干活的自主 Agent。说实话，整体上要比 OpenClaw 的可玩性高太多了呀，bro。

## /loop 长什么样

语法就一行：

`/loop 5m 检查部署是否完成，如果有 Pod 挂了告诉我原因`

就这一行。Claude 会每 5 分钟执行一次你的指令，直到你关掉它或者 3 天后自动过期。

几个我常用的写法：

- • `/loop 5m 检查部署状态` — 每 5 分钟盯一次
    
- • `/loop 30m 看看有没有新的 GitHub issue，有的话分类打标签` — 自动处理 issue
    
- • `/loop 1h 跑一遍测试套件，失败的列出来` — 每小时巡检
    
- • `/loop 检查构建状态` — 不写间隔，默认 10 分钟
    

时间单位支持 `s`（秒）、`m`（分）、`h`（时）、`d`（天）。

还有个一次性提醒的玩法：

`10 分钟以后把这个任务推送到 GitHub 的 issue 里面  使用    github cli 的命令 gh 给项目 lltx/note 创建issue`

这不是 cron，是自然语言。Claude 自己解析时间，到点执行一次就完事。

![Image](https://mmbiz.qpic.cn/mmbiz_png/0IWNK9y4M2ibybG2DhS9YzVxnVJmEwIKr2ToecRuQJVt5Cy6N4b0fgOsgXLia9gq0kkKbplyiaqKN4mC251kK7FqerhzzicsFcR6nOUP7ibDfqCI/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

## 它和 cron job 有什么区别

你可能觉得，这不就是个花哨的定时器？

不是。区别在于执行任务的是谁。

cron job 执行的是脚本——你写好 shell，它定时跑。逻辑是死的，出了预期之外的情况它不会处理，也不会告诉你。

/loop 执行的是一个 AI Agent。它能读你的代码，理解上下文，遇到问题会分析原因。一个 cron 脚本发现测试挂了只能给你发条告警；/loop 发现测试挂了，会去看报错信息，尝试修复，修好了还会提交代码。

## 三个实际场景

### 场景一：部署看护

这是我用得最多的。命令很简单：

`/loop 5m 检查当前项目的部署状态，如果有异常 Pod 就分析日志找原因，把结果写到使用 github cli 的命令 gh 给项目 lltx/note 创建issue`

效果：打开 `deploy-watch.md`，里面清清楚楚记着：

- • 09:15 所有 Pod Running，健康检查通过
    
- • 09:35 gateway-pod-7x9k2 重启一次，原因：内存超限，已记录
    
- • 09:40 全部稳定
    

省下来的不只是时间，还有凌晨两点盯屏幕的那种焦虑。

### 场景二：PR 巡检

团队里经常有 PR 挂着 CI 红了没人管。配一个 /loop：

`/loop 30m 检查当前分支的 PR，如果 CI 失败了，看看报错日志，能修的就修掉并 push`

跑了两天，它帮我抓到三个问题：一个缺失的 import，一个过期的 snapshot，还有一个测试里的竞态条件。都是那种"不难修但容易忘"的小毛病。

### 场景三：每日变更摘要

`/loop 24h 总结过去 24 小时 main 分支的所有提交，包括 PR 标题、作者、改了哪些文件，把结果写到使用 github cli 的命令 gh 给项目 lltx/note 创建issue`

我们团队之前的 standup 基本靠口述，经常漏东西。现在每天早上有一份自动生成的变更报告，清单式的，开会直接过。

![基于 github issue 结果归档](https://mmbiz.qpic.cn/sz_mmbiz_png/0IWNK9y4M28JhcuHJpLrVddRFo9x7cibEnic4jlY3zwdXiauc9yQl0eh2f2rGBx9H7b1CEolesqvSVJ8qsQs7cMfpIvWBaMMEkpMI6rfSoCGxM/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

基于 github issue 结果归档

## 自主 Agent 三件套

光有 /loop 还不够。一条腿走不远。

我管接下来要说的东西叫"Agent 三件套"：/loop 是腿，CLAUDE.md 是脑子，Hook 是记忆。三样凑齐，Claude Code 就能自己跑起来了。

### /loop — 让它动起来

前面说过了，不赘述。

### CLAUDE.md — 让它知道自己是谁

在项目根目录放一个 `CLAUDE.md`，Claude 每次启动都会读。这就是它的操作手册。

举个例子：

`# 项目：XXX 微服务系统      ## 核心规则      - 所有 API 改动必须同步更新 openapi.yaml   - 测试覆盖率不低于 80%   - commit message 用 conventional commits      ## 常见操作      - 部署命令：make deploy-prod   - 日志路径：/var/log/app/   - 健康检查：curl -s http://localhost:8080/actuator/health      ## /loop 任务清单      - 每 30 分钟检查一次健康检查接口   - 每 2 小时跑一次 lint + test   - 每天早上生成昨日变更摘要`

CLAUDE.md 写得好，/loop 跑起来就像一个了解你项目规矩的值班工程师。写得烂或者干脆不写？那就是一台 token 焚烧炉。

### Hook — 让它记住干了什么

Agent 跑了一晚上，第二天打开电脑，发现什么痕迹都没留下。这种事你遇到一次就会想到 Hook。

在 `.claude/settings.json` 里配一个 Hook，让每次文件变更自动 git commit：



```
{  
"hooks": {    "PostToolUse": [      {        "matcher": "Bash|Write|Edit",        "command": "cd $(pwd) && git add -A && git diff --cached --quiet || git commit -m 'auto: agent checkpoint' --no-verify"      
}    
]  
}
}
```
`

好处很直接：Claude 不用浪费 token 去执行 git 命令，每次变更都有记录，出了问题 `git log` 一查就知道 Agent 干了什么。

三件套就是这么简单。/loop 让它动起来，CLAUDE.md 告诉它规矩，Hook 帮它记账。

## 几个补充

一个 session 最多能同时跑 50 个定时任务，一个盯部署、一个盯 PR、一个跑测试，互不干扰。我日常开三四个就够了。

还有个玩法：`/loop 20m /review-pr`——让 /loop 去调度另一个 slash 命令。任何 skill 都可以被定时跑，排列组合的空间很大。

想更猛一点？开三个终端窗口，每个窗口启动一个 Claude Code session，指向不同项目，各跑各的。一台 Mac，三只小龙虾，各管各的。

有个细节要注意：/loop 是跟着 session 走的，关掉终端任务就没了。忘了关也不怕，3 天后自动过期。如果需要关了终端还能跑的调度，Claude Code Desktop 有 scheduled tasks，不依赖终端 session。

## 最后

/loop 不是什么革命性的发明。cron 存在了几十年，定时任务谁都会写。

但让一个能读懂代码的 Agent 去定时执行任务，和让 shell 脚本定时跑命令，感受上差了一个量级。测试挂了，脚本告诉你"exit code 1"；/loop 会去看报错、尝试修复、修好了还帮你提交。

不用装 OpenClaw，不用学新框架。一行命令，你的终端里就多了一只小龙虾。