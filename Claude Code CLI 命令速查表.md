

原创 若风007 若风科技说

 _2026年1月27日 00:13_ _广东_ 听全文

我整理了 Mac 上 Claude Code CLI 工具中，官方所有的模型和命令以及相关的快捷键，供大家参考和查阅

为了防止你以后找不到，建议先点赞收藏起来，下面我们正式开始

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDP3Q5gMozgQPpj3bqUgNdgSzGufGVSZehfJ2EHiacdD7nJ2AvwJeIXo6Q/640?wx_fmt=png&from=appmsg)

# **符号命令（ Symbol Command）**

> 共计 4 个，对大多人而言，`@` 和 `/` 最常用

| **符号命令** | **说明**  | **Description** |
| -------- | ------- | --------------- |
| !        | bash 模式 | for bash mode   |
| /        | 命令模式    | for commands    |
| @        | 引用文件路径  | for file paths  |
| &        | 后台运行    | for background  |

# **快捷键（ Shortcut）**

> 共计 8 个

| **快捷键**     | **说明**   | **Description**               |
| ----------- | -------- | ----------------------------- |
| esc + esc   | 清除所有输入内容 | double tap esc to clear input |
| shift + tab | 自动接收编辑   | to auto-accept edits          |
| ctrl + o    | 查看详细输出   | for verbose output            |
| shift + ⏎   | 换行       | newlines                      |
| ctrl + _    | 撤销       | to undo                       |
| ctrl + z    | 暂停       | to suspend                    |
| cmd + v     | 粘贴图片     | to paste images               |
| ctrl + s    | 暂存提示词    | to stash prompt               |

# **斜杠命令（Slash Command）**

> 共计 30 个

| **斜杠命令**            | **说明**                                         | **Description**                                                                                               |
| ------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| /help               | 显示帮助和可用命令                                      | Show help and available commands                                                                              |
| /skills             | 列举可用技能                                         | List available skills                                                                                         |
| /todos              | 列举当前所有的待办项                                     | List current todo items                                                                                       |
| /agents             | 管理 智能体 配置                                      | Manage agent configurations                                                                                   |
| /add-dir            | 添加一个新的工作目录                                     | Add a new working directory                                                                                   |
| /chrome             | Claude Chrome 浏览器插件（测试版）设置                     | Claude in Chrome (Beta) settings                                                                              |
| /clear              | 清除历史会话，然后释放上下文                                 | Clear conversation history and free up context                                                                |
| /compact            | 清除对话历史记录，但保留上下文摘要。 可选操作：/ 精简模式                 | Clear conversation history but keep a summary in context. Optional: /compact [instructions for summarization] |
| /config             | 打开配置面板                                         | Open config panel                                                                                             |
| /rename             | 重命名当前会话                                        | Rename the current conversation                                                                               |
| /usage              | 显示计划使用限额                                       | Show plan usage limits                                                                                        |
| /context            | 将当前上下文占用情况可视化为彩色网格                             | Visualize current context usage as a colored grid                                                             |
| /terminal-setup     | 启用 Option+Enter 快捷键的换行及视觉提示音功能                 | Enable Option+Enter key binding for newlines and visual bell                                                  |
| /cost               | 显示当前会话的总开销                                     | Show the total cost and duration of the current session                                                       |
| /doctor             | 诊断并验证你的 Claude Code 安装及设置情况                    | Diagnose and verify your Claude Code installation and settings                                                |
| /exit               | 退出 REPL                                        | Exit the REPL                                                                                                 |
| /export             | 导出当前会话到一个文件中或者剪切板                              | Export the current conversation to a file or clipboard                                                        |
| /fork               | 在此节点复刻当前对话的分支版本                                | Create a fork of the current conversation at this point                                                       |
| /hooks              | 管理工具事件的钩子配置                                    | Manage hook configurations for tool events                                                                    |
| /ide                | 管理 IDE 集成并显示状态                                 | Manage IDE integrations and show status                                                                       |
| /init               | 新建一个 CLAUDE.md 文件并写入代码库文档内容                    | Initialize a new CLAUDE.md file with codebase documentation                                                   |
| /install-github-app | 为代码仓库配置 Claude GitHub 工作流                      | Set up Claude GitHub Actions for a repository                                                                 |
| /install-slack-app  | 安装 Claude Slack 应用                             | Install the Claude Slack app                                                                                  |
| /login              | 使用你的 Anthropic 账号登录                            | Sign in with your Anthropic account                                                                           |
| /logout             | 退出你的 Anthropic 账号                              | Sign out from your Anthropic account                                                                          |
| /mcp                | 管理 MCP 服务                                      | Manage MCP servers                                                                                            |
| /memory             | 编辑 Claude 记忆文件                                 | Edit Claude memory files                                                                                      |
| /mobile             | 展示二维码以下载 Claude 移动应用                           | Show QR code to download the Claude mobile app                                                                |
| /model              | 为 Claude Code 设置 AI 模型                         | Set the AI model for Claude Code                                                                              |
| /output-style       | 直接或通过选择菜单设置输出格式                                | Set the output style directly or from a selection menu                                                        |
| /permissions        | 管理工具权限的允许与拒绝规则                                 | Manage allow & deny tool permission rules                                                                     |
| /plan               | 启用规划模式或查看当前会话规划                                | Enable plan mode or view the current session plan                                                             |
| /plugin             | 管理 Claude Code 插件                              | Manage Claude Code plugins                                                                                    |
| /pr-comments        | 获取 GitHub 拉取请求中的评论                             | Get comments from a GitHub pull request                                                                       |
| /release-notes      | 查看更新日志                                         | View release notes                                                                                            |
| /resume             | 恢复对话                                           | Resume a conversation                                                                                         |
| /rewind             | 将代码和 / 或对话恢复至之前的节点                             | Restore the code and/or conversation to a previous point                                                      |
| /review             | Review 拉取请求                                    | Review a pull request                                                                                         |
| /sandbox            | ○ 沙箱功能已禁用（按回车键进行配置）                            | ○ sandbox disabled (⏎ to configure)                                                                           |
| /security-review    | 对当前分支的待处理变更完成安全审核                              | Complete a security review of the pending changes on the current branch                                       |
| /stats              | 查看你的 Claude Code 使用数据及活动记录                     | Show your Claude Code usage statistics and activity                                                           |
| /status             | 查看 Claude Code 状态信息，包括版本、模型、账号、API 连接状态及工具运行状态 | Show Claude Code status including version, model, account, API connectivity, and tool statuses                |
| /statusline         | 配置 Claude Code 的状态栏界面                          | Set up Claude Code's status line UI                                                                           |
| /stickers           | 申领 Claude Code 贴纸                              | Order Claude Code stickers                                                                                    |
| /tasks              | 列出并管理后台任务                                      | List and manage background tasks                                                                              |

# 运行示例

## /cost

这里可以看到你的 API 花销

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPFL24DdRPpHnm35wxySqFQrRUNh1Y8n1PN8Zbvne15lxTTNic7o6sX9Q/640?wx_fmt=png&from=appmsg)

## /doctor

这个可以看到你的环境相关的配置

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPDrjWaXMQVeOA9U7tr9GRlBAWGtQcXP8WPGuMCFbwK88gc1dBZUjKmQ/640?wx_fmt=png&from=appmsg)

## /context

这里可以清除的看到上下文使用的情况

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPSXLwBbdKxWgiboousBoLE2bx2slXxSPbekSaQIBf8MBcF97KzgmGNvw/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPyJdgLZAZFzbO7X5nBxh9np6riap5sX2vxFMdLicVeE1f88UgjianmWtTA/640?wx_fmt=png&from=appmsg)

## /stats

模型使用量统计，支持查看所有时间，过去 7 天，过去 30 天的数据

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDP1QmJO21Niacs5ibNCVOY4gfo4gYRvEjicfQe6b79FNHufVzUYuQsOXxVg/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPScQmHQPaIXXgOAgFDlJ4Ts1ickttk0vVIsQynmUXlKJcuYwOZibSEtTg/640?wx_fmt=png&from=appmsg)

## /task

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPxVwWKzsAAufRKLaXstkg8X0xDVsYMTD9FRVqBvhApBUILbqpqylv0A/640?wx_fmt=png&from=appmsg)

## /memory

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPicI2NZfSS8VazPAw8uXRusdF68KqLl8tib1ia6qq9PsTE375MsZVC0OSQ/640?wx_fmt=png&from=appmsg)

## /chrome

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPZmWruMXKDBzlq2SiazwcHnuvdZGpBIfXU2uoqeYS4hsoRBsTn1kXquw/640?wx_fmt=png&from=appmsg)

## /config --> Language (语言设置)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPQz0cXkiaK1UqZZSpo1kHLvmreT95icPH0Q8WvtTalfkqiaaha0E3Rt60Q/640?wx_fmt=png&from=appmsg)

## /config --> Status (配置状态查询)

这里从 url host可以看出使用的模型类型，我使用的是 bigmodel（智谱）的

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPowBXBicHZzoY7KLTh9OC5ePyns9HGzYkzrUp7YffHsGlRhUic9NG1ypg/640?wx_fmt=png&from=appmsg)

## /help --> custom-commands

安装的自定义命令，部分 Skills 会出现在这里

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPgn2DupKTdmd0FFO3FU1crWrsotASiaruZDbu8EBxictR9alrCPictYwhQ/640?wx_fmt=png&from=appmsg)

## /config --> Model ( 模型切换)

官方支持 3 种模型

| **模型**     | **描述**        |
| ---------- | ------------- |
| Sonnet 4.5 | 默认模型，用于处理日常事务 |
| Opus 4.5   | 最强模型，用于处理复杂事务 |
| Haiku 4.5  | 最快模型，用于日常快速问答 |

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPY3c5zvHY1MXg0FicXw0I6zUeDDBj7O1cCrGJjx7oV8udOKxfVvFDGww/640?wx_fmt=png&from=appmsg)

## /config --> Config --> Default permission mode ( 默认权限模式)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDP9n5feBoxH966OsDszJeicKHR2YUlBgcq68ibBYClEkGNwBe9M9usM6pA/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPoTpA0yan8ARicyaRddJk2kwuXBgqDdhA7TImClR5FFFw9icWfcAqOVnQ/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPNIrk0ENh1Yumk6VKo7YoTd8VPKX91T6TNicPrcznIrFTVicAicRmCwbVg/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPiccVJXRicbpzibJicyqt49oRUWEYeK8PyAoXqMgjU3iawBmNNzIqyuwz9iag/640?wx_fmt=png&from=appmsg)

## /config --> Config --> Editor (编辑器模式)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPzvnROp68b7H9vqxfzlKbtXaro556xQ0zKydiaeop04iceglZzJRSMsWw/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPpCFCz7Fxbf5Ej6AYaQDSCt1dFrglkn4Ys2hxUg2c6C6llWVfnkY5Jw/640?wx_fmt=png&from=appmsg)

## /config --> Config --> Notifinations (通知样式配置)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPkbJbaiaO4tHGZC10EWH1jLhcmKV50GPnialUlkb5ib3iaM0ibHd0OFyYWBg/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPia5gomjCKTAJHiagDBxEa7sZ7I6dN9DIBV5zUbBXYd1Us1wt5sbwgMPw/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPnmqmic4SFK7Oic40GiayhFy2oeuOCcibKHOAlvdkCq5UPvA88pjVYdaTlg/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPhYjbHGBfWcWVstXsvWZ2yMq0hGfYjsdhSoTyOy6jBLIria9uck104Nw/640?wx_fmt=png&from=appmsg)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPTg7dcz5T8VWoJicCMqfgo4UneZbJb6usYX1Axz8wM6FDAwibRB7PpyLw/640?wx_fmt=png&from=appmsg)

## /config --> Config --> Theme ( 主题配置)

![](https://mmbiz.qpic.cn/mmbiz_png/CINOlNxdPrKiatJnfibHo7w5AEsWuCxWDPicaLicXtqgsWibQE8VLNbKtPrtHubgT4oZv7z9pNklk2dg9bvDV589MIw/640?wx_fmt=png&from=appmsg)

  
