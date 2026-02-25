

![](https://mmbiz.qpic.cn/mmbiz_jpg/CINOlNxdPrJESxzqC5iczxaiauoYSJVia46BMiaLs3sflfYlG2OWiaia4U22Pu4h4zmXMJ4nH5lYic7tSd7NjsWQr4Dew/640?wx_fmt=jpeg&from=appmsg)

> _把 Claude Code 当聊天工具用？你可能浪费了 50% 的效率。作者用了两个月才发现，原来 Claude Code 的输入区本质上是一个终端行编辑器，bash/zsh 的 Readline 快捷键都能用。本文整理了最实用的光标操作技巧，帮你把 Prompt 当代码写，效率翻倍只需掌握 6 个快捷键。附完整速查表。_

我把 Claude Code 当成聊天工具用了两个月，直到某天看到同事用 `Ctrl + A` 跳到行首修改 Prompt，才意识到自己一直在用笨办法。

Claude Code 的输入区本质上就是一个终端行编辑器。如果你用过 bash 或 zsh，那些 Readline 快捷键在这里都能用。我整理了一下常用的，希望能帮你少踩点坑。

---

# 启动与退出

![](https://mmbiz.qpic.cn/mmbiz_jpg/CINOlNxdPrJESxzqC5iczxaiauoYSJVia46mTTs4rwzick6ZicmkopUkgvw0DtkI6mDDIR5NIXh0ciciaIj0ic8GZwKviaQ/640?wx_fmt=jpeg&from=appmsg)

## 启动 Claude Code

| 命令       | 说明        |
| -------- | --------- |
| `claude` | 标准启动命令    |
| `cc`     | 自定义别名（推荐） |

**设置别名**（添加到 `~/.zshrc` 或 `~/.bashrc`）:

`alias cc='claude'   `

我一开始每次都输入 `claude`，后来发现一天要打开十几次，两个字母比六个字母快多了。现在 `cc` 已经成了肌肉记忆。

## 退出 Claude Code

| 快捷键 / 命令         | 说明        |
| ---------------- | --------- |
| `Ctrl + C`       | 中断并退出（推荐） |
| `exit` 或 `/exit` | 输入命令退出    |
| `Ctrl + D`       | EOF 信号退出  |

`Ctrl + C` 最直接，按一下就走。`exit` 适合在某个操作完成后优雅退出。`Ctrl + D` 在 Unix 里代表"输入结束"，也能用来退出。

---

# 基础移动

![](https://mmbiz.qpic.cn/mmbiz_jpg/CINOlNxdPrJESxzqC5iczxaiauoYSJVia46Txxeq8WnVyQEgLxr84nnt3H0iciaL3GRA9ich3yB8ovYicYzgib7yObAtJw/640?wx_fmt=jpeg&from=appmsg)

## 行级跳转

| 快捷键        | 单词含义      | 作用   |
| ---------- | --------- | ---- |
| `Ctrl + A` | A (字母表首位) | 跳到行首 |
| `Ctrl + E` | End       | 跳到行尾 |

经常写了一半发现没加角色设定，或者想改输出格式，直接跳到行首或行尾比按方向键快多了。

## 字符和单词移动

| 快捷键        | 单词含义     | 作用     |
| ---------- | -------- | ------ |
| `Ctrl + B` | Backward | 向左一个字符 |
| `Ctrl + F` | Forward  | 向右一个字符 |
| `Alt + B`  | Backward | 向左一个单词 |
| `Alt + F`  | Forward  | 向右一个单词 |

`Alt + B/F` 在 macOS 上有时候会和系统快捷键冲突，如果不好用可以检查一下终端设置。

---

# 删除操作

![](https://mmbiz.qpic.cn/mmbiz_jpg/CINOlNxdPrJESxzqC5iczxaiauoYSJVia46yZeeDtBLnHOEWgzDJYbWJ31kKY9kk7II8ArA7MrBtic6KvCh6hK02Dw/640?wx_fmt=jpeg&from=appmsg)

## 局部删除

| 快捷键        | 单词含义      | 作用        |
| ---------- | --------- | --------- |
| `Ctrl + H` | H (删除前字符) | 删除光标前一个字符 |
| `Ctrl + D` | Delete    | 删除光标后字符   |
| `Ctrl + W` | Wipe      | 删除光标前一个单词 |
| `Alt + D`  | Delete    | 删除光标后一个单词 |

说实话 `Ctrl + H` 我基本不用，直接按 Backspace 更顺手。但 `Ctrl + W` 删除单词确实好用，尤其是想快速改某个参数的时候。

## 大段删除

| 快捷键        | 单词含义           | 作用    |
| ---------- | -------------- | ----- |
| `Ctrl + K` | Kill (剪切)      | 删除到行尾 |
| `Ctrl + U` | Undo (Unix 惯例) | 删除到行首 |

比如我写了个 Prompt 想换个输出格式，就用 `Ctrl + K` 把后面全删了重写，不用一个个字符删。

---

# 历史记录

![](https://mmbiz.qpic.cn/mmbiz_jpg/CINOlNxdPrJESxzqC5iczxaiauoYSJVia467q2KrF66CYUOwOticglkXia2HDIEzSCffAHymCqlpNbNv5JH0ZxBcoYA/640?wx_fmt=jpeg&from=appmsg)

## 上下翻找
| 快捷键        | 单词含义     | 作用  |
| ---------- | -------- | --- |
| `Ctrl + P` | Previous | 上一条 |
| `Ctrl + N` | Next     | 下一条 |

`Ctrl + P` 就是方向键上，`Ctrl + N` 是方向键下。看个人习惯，我其实更习惯按方向键。

## 搜索历史
| 快捷键        | 单词含义    | 作用     |
| ---------- | ------- | ------ |
| `Ctrl + R` | Reverse | 反向搜索历史 |

按 `Ctrl + R` 之后输入关键词（比如 `flutter` 或 `jenkins`），就能找到之前用过的类似 Prompt。我经常复用之前的 Prompt 稍微改改，比从头写快多了。

---

# 其他操作
| 快捷键        | 单词含义        | 作用        |
| ---------- | ----------- | --------- |
| `Ctrl + Y` | Yank (粘贴)   | 粘贴刚删的内容   |
| `Alt + Y`  | Yank pop    | 轮换之前的删除记录 |
| `Ctrl + L` | L (或 Clear) | 清屏（上下文还在） |
| `Ctrl + C` | Cancel      | 中断当前生成    |

`Ctrl + Y` 我用得不多，但有时候删错了想恢复还挺方便的。`Ctrl + C` 倒是经常用，发现 Claude 理解错了就赶紧中断重问，别等它说完一长串废话。

---

# 实用建议

## 渐进式写 Prompt
我不会一次性写个完美 Prompt，通常先写个简单的试试水，然后根据 Claude 的反馈不断改。这和写代码的小步迭代挺像的。

## Prompt 结构
我习惯用这个结构：
`【角色】   【任务】   【输出要求】   `

熟悉 `Ctrl + A/E/K/U` 之后，改 Prompt 就快多了。比如想换个角色，跳到行首改完就行；想换输出格式，`Ctrl + K` 删掉后面重写。

## 复用历史
`Ctrl + R` 搜索历史真的能省不少时间。我发现自己经常问类似的问题，把之前的 Prompt 拿过来稍微改改比重写快多了。

---

# 最后说两句
刚开始用 Claude Code 时觉得快捷键太多记不住，其实常用的就那几个。
**必记组合**: `cc` 启动、`Ctrl + C` 退出、`Ctrl + A/E` 行首尾、`Ctrl + K/U` 大段删除、`Ctrl + R` 搜索历史。这几个基本能覆盖 90% 的日常使用。

别把这些快捷键当成必须掌握的技能，用着顺手就行。毕竟工具是拿来用的，不是拿来背的。

---

**附: 完整速查表**

| 操作   | 快捷键                  | 优先级 |
| ---- | -------------------- | --- |
| 启动   | `cc`                 | ⭐⭐⭐ |
| 退出   | `Ctrl + C`           | ⭐⭐⭐ |
| 行首尾  | `Ctrl + A / E`       | ⭐⭐⭐ |
| 大段删  | `Ctrl + K / U`       | ⭐⭐⭐ |
| 搜索历史 | `Ctrl + R`           | ⭐⭐⭐ |
| 单词移动 | `Alt + B / F`        | ⭐⭐  |
| 单词删除 | `Ctrl + W / Alt + D` | ⭐⭐  |
| 清屏   | `Ctrl + L`           | ⭐   |