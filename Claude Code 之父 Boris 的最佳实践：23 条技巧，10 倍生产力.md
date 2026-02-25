


原创 九皋山人者 绿蚁红泥天欲雪


>  整理自 Claude Code 创建者 Boris Cherny 的两次分享
> - 第一次分享（2026年1月3日）：Boris 个人工作流详解
> - 第二次分享（2026年2 月 1 日）：来自 Claude Code 团队的 10 个技巧
 
---

## 第一部分：Boris 的个人工作流（13 条技巧）

### 1. 并行运行多个 Claude 会话
>我在终端里同时运行 5 个 Claude，将标签页编号为 1-5，并使用系统通知来了解哪个 Claude 需要输入。 

**解读：** 这是 Claude Code 最核心的生产力技巧——**并行化**。与其等待一个任务完成再开始下一个，不如同时推进多个独立任务。
**实操要点：**
- 在 iTerm2 中配置系统通知，当 Claude 需要输入时自动提醒
- 配置方法：iTerm2 System Notifications
- 每个终端标签处理一个独立任务，避免上下文混乱
**适合场景：** 多个不相关的功能开发、bug 修复、文档编写并行进行

---
### 2. 跨平台协作：终端 + Web + 手机
 
> 我同时在 claude.ai/code 上运行 5-10 个 Claude，与本地 Claude 并行工作。我会用 `&` 将本地会话交给 Web，有时用 `--teleport` 在两者之间切换。我每天早上还会从手机（Claude iOS 应用）启动几个会话，稍后再查看进度。

**解读：** Boris 总共同时运行 **15-20 个** Claude 会话！这揭示了一个重要理念：Claude Code 的威力来自并行化，而非复杂配置。
**关键命令：**
```markdown
# 将长时间任务交给 Web 后台运行
claude "分析整个代码库并生成文档" &
 
# 将 Web 会话拉回本地终端
claude --teleport <session-id>
```

**注意事项：**
- 约 10-20% 的会话可能因意外情况被放弃，这是正常的
- 手机端适合启动不紧急的后台任务
---
### 3. 模型选择：Opus 4.5 with Thinking
> 我所有工作都使用 Opus 4.5 with thinking。它是我用过的最好的编码模型，虽然比 Sonnet 更大更慢，但因为你不需要频繁纠正它，工具调用也更准确，所以最终几乎总是比用小模型更快。

**解读：** 这打破了"小模型更快"的直觉认知。Boris 的逻辑是：

| 模型        | 单次耗时 | 迭代次数 | 总时间  |
| --------- | ---- | ---- | ---- |
| Sonnet（快） | 1 分钟 | 5 次  | 5 分钟 |
| Opus（慢）   | 2 分钟 | 1 次  | 2 分钟 |
**核心洞察：** 真正的瓶颈不是 AI 处理时间，而是**人类纠正和指导的时间**。

---
### 4. 团队共享 CLAUDE.md
> 我们团队为 Claude Code 仓库共享一个 CLAUDE.md 文件。我们把它提交到 git，整个团队每周贡献多次。每当看到 Claude 做错什么，我们就添加到 CLAUDE.md，这样 Claude 下次就知道不要这样做了。

**解读：** CLAUDE.md 是 Claude Code 的"记忆文件"。通过持续积累团队的经验教训，Claude 会变得越来越了解你的项目。
**最佳实践：**

```markdown
# CLAUDE.md 示例结构
## 项目命令
- 安装: `bun install`
- 测试: `bun run test`
- 检查: `bun run typecheck`
## 不可违反的规则
- 不要在没有充分理由的情况下添加新依赖
- 所有改动必须包含验证步骤

## 常见错误（基于实际纠正添加）
- 不要使用 console.log 调试，使用 logger
- API 响应必须包含错误处理
```

**进阶技巧：** Boris 团队的 CLAUDE.md 约 2500 tokens，不要写太长，保持精炼。

---
### 5. 代码审查中更新 CLAUDE.md

> 在代码审查时，我经常在同事的 PR 上标记 @.claude 来将内容添加到 CLAUDE.md。我们使用 Claude Code GitHub Action（/install-github-action）来实现。

**解读：** 这实现了 Dan Shipper 提出的"复利工程"（Compounding Engineering）理念——每一次代码审查都是积累团队知识的机会。

**工作流程：**

```markdown
Claude 犯错 → 开发者发现 → 添加规则到 CLAUDE.md → 提交到 git → Claude 下次会话读取 → Claude 避免同类错误
```

**设置方法：** 在 Claude Code 中运行 `/install-github-action` 安装 GitHub Action

---
### 6. Plan Mode 先行
> 大多数会话从 Plan 模式开始（Shift+Tab 两次）。如果我的目标是写一个 Pull Request，我会使用 Plan 模式，与 Claude 来回讨论直到我满意它的计划。然后切换到自动接受编辑模式，Claude 通常可以一次完成。一个好计划真的很重要！

**解读：** Plan Mode 不是"训练轮"，而是"量好再切"的智慧。跳过计划阶段看似节省时间，实际上会花更多时间修复错误。

**操作方式：**

1. `Shift + Tab` 两次进入 Plan 模式
2. 与 Claude 讨论直到计划满意
3. 切换到自动接受模式执行
4. 如果执行出问题，回到 Plan 模式重新规划，而不是硬推

**高阶玩法：** 有团队成员会启动第二个 Claude 来以"Staff Engineer"角色审查第一个 Claude 的计划——新鲜的上下文，更少的偏见。

---

### 7. Slash Commands 自动化日常工作流

> 我为每个"内循环"工作流使用斜杠命令。这避免了重复提示，也让 Claude 可以使用这些工作流。命令存储在 `.claude/commands/` 目录下并提交到 git。

> 比如，Claude 和我每天使用 `/commit-push-pr` 斜杠命令几十次。该命令使用内联 bash 预计算 git status 等信息，让命令运行更快并避免与模型的来回交互。

  

**解读：** 如果你每天做某件事超过一次，就把它变成 Slash Command。

**示例命令结构：**
```markdown
---
name: commit-push-pr
description: 提交、推送并创建 PR
---5  
当前 git 状态：
$(git status --short)
  
当前分支：
$(git branch --show-current)
  
请基于以上信息创建一个语义化的 commit，push 并创建 PR
```

**关键点：** 使用内联 bash（`$(...)` 语法）预计算上下文，减少模型调用次数

---
### 8. Subagents 处理常见工作流

> 我定期使用几个子代理：`code-simplifier` 在 Claude 完成后简化代码，`verify-app` 有详细的端到端测试指令，等等。类似于斜杠命令，我把子代理视为自动化我对大多数 PR 执行的最常见工作流。

**解读：** Subagent 是独立运行的"子任务执行器"，它们有独立的上下文窗口，不会污染主会话。
**Subagent vs Slash Command 区别：**
- **Slash Command**：在当前会话执行，共享上下文
- **Subagent**：在独立会话执行，适合重型任务
**适用场景：**
- 高容量操作（测试输出、日志分析、深度代码搜索）
- 并行调查
- 保持主会话上下文干净
**简单触发：** 在提示词末尾加上 “use subagents”

---
### 9. PostToolUse Hook 自动格式化

> 我们使用 PostToolUse hook 来格式化 Claude 的代码。Claude 通常开箱即生成格式良好的代码，这个 hook 处理最后 10% 以避免后续 CI 中的格式错误。

**解读：** Hook 是在特定事件（如文件写入后）自动执行的脚本，实现"自动纠错"。
**配置示例（.claude/hooks.json）：**
```json
{
    "PostToolUse": [
        {
            "matcher": "Write|Edit",
            "hooks": [
                {
                    "type": "command",
                    "command": "bun run format || true"
                }
            ]
        }
    ]
}
```

**工作流改进：**

```markdown
之前：Claude 写代码 → Push → CI 失败（格式错误）→ 修复 → Push → CI 通过
之后：Claude 写代码 → Hook 自动格式化 → Push → CI 通过
```
---
### 10. 安全的权限管理

> 我不使用 `--dangerously-skip-permissions`。相反，我使用 `/permissions` 预先允许我环境中已知安全的常用 bash 命令，以避免不必要的权限提示。这些大多提交到 `.claude/settings.json` 并与团队共享。

**解读：** `--dangerously-skip-permissions` 允许 Claude 运行任何命令，非常危险。正确做法是精细化授权。

**推荐配置（.claude/settings.json）：**

```json
 {
   "permissions": {
     "allow": [
       "bun run build:*",
       "bun run test:*",
       "bun run typecheck:*",
       "bun run format",
       "git status",
      "git diff"
     ]
   }
 }
```

**原则：** 白名单机制，只允许已知安全的命令

---
### 11. MCP 集成外部工具

> Claude Code 为我使用所有工具。它经常搜索和发布 Slack 消息（通过 MCP 服务器），运行 BigQuery 查询来回答分析问题，从 Sentry 获取错误日志等。Slack MCP 配置提交到我们的 `.mcp.json` 并与团队共享。

**解读：** MCP（Model Context Protocol）让 Claude 可以调用外部服务。Boris 说他已经 6 个月没写过一行 SQL 了——都是 Claude 通过 BigQuery CLI 完成的。

**Slack MCP 配置示例（.mcp.json）：**

```json
 {
   "mcpServers": {
     "slack": {
       "type": "http",
       "url": "https://slack.mcp.anthropic.com/mcp"
     }
   }
 }
```

**优势：** 工具按需加载，不使用时不消耗上下文窗口

---
### 12. 长时间任务的处理

> 对于非常长时间运行的任务，我会：
> (a) 提示 Claude 完成后用后台代理验证，
> (b) 使用 agent Stop hook 更确定性地执行，或 © 使用 ralph-wiggum 插件。
> 我也会在沙箱中使用 `--permission-mode=dontAsk` 
> 或 `--dangerously-skip-permissions` 来避免权限提示，让 Claude 可以不被阻塞地工作。

**解读：** 长任务需要特殊策略，避免 Claude 被权限提示卡住。

**三种方案：**

1. **后台代理验证**：任务完成后自动触发验证
2. **Stop Hook**：更可靠的程序化验证
3. **ralph-wiggum 插件**：自动处理某些类型的阻塞

**沙箱使用场景：** 只有在隔离的沙箱环境中才考虑跳过权限

---
### 13. 最重要的技巧：给 Claude 验证能力

> 最后一个也是最重要的技巧：获得 Claude Code 优秀结果的关键是——给 Claude 一种验证其工作的方式。如果 Claude 有这个反馈循环，最终结果的质量会提升 2-3 倍。

> Claude 使用 Chrome 扩展测试我提交到 claude.ai/code 的每一个改动。它打开浏览器，测试 UI，然后迭代直到代码工作且用户体验良好。

**解读：** 这是 Boris 强调的**最重要技巧**。验证方式因领域而异：

|领域|验证方式|
|---|---|
|命令行工具|运行 bash 命令|
|后端服务|运行测试套件|
|Web 应用|浏览器测试（Chrome 扩展）|
|移动应用|手机模拟器测试|

**核心理念：** 投资建立可靠的验证基础设施，收益将体现在每一个任务上。

---

## 第二部分：团队的 10 个技巧

> 这部分技巧来自 Boris 的第二次分享，汇集了整个 Claude Code 团队的经验。
### 1. 并行工作：Git Worktrees

> 启动 3-5 个 git worktrees，每个运行自己的 Claude 会话。这是团队公认的**最大生产力提升**。有人设置 shell 别名（za, zb, zc）来一键跳转。

**解读：** Git worktree 允许在同一仓库的不同目录中检出不同分支，避免并行会话的冲突。
**设置方法：**

```markdown

1 # 创建 worktrees
2 git worktree add ../project-a feature-a
3 git worktree add ../project-b feature-b4 git worktree add ../project-c bugfix-c
5  
6 # 设置快捷别名（在 .zshrc 或 .bashrc 中）
7 alias za="cd ../project-a && claude"
8 alias zb="cd ../project-b && claude"
9 alias zc="cd ../project-c && claude"
```
**为什么用 worktree 而不是分支？** 每个 worktree 是独立目录，可以同时运行不同的 Claude 会话而不冲突。
---
### 2. 复杂任务从 Plan Mode 开始

> 把精力倾注在计划上，这样 Claude 就能一次完成实现。如果出了问题，切换回计划模式重新规划，而不是硬推。有人甚至启动第二个 Claude 作为"Staff Engineer"来审查计划。
> 
>   

  

**解读：** 与第一部分的技巧呼应。强调"双 Claude 审查"策略的价值。

  

**双 Claude 审查流程：**

```
1 Claude A 写计划 → Claude B 以 Staff Engineer 角色审查（新鲜上下文，更少偏见） → 修改计划 → 执行
```

---
### 3. 持续优化 CLAUDE.md

> 每次纠正后，告诉 Claude："更新你的 CLAUDE.md 这样你下次就不会犯同样的错误。"Claude 非常擅长为自己编写规则。持续迭代直到 Claude 的错误率可测量地下降。

**解读：** 让 Claude 自己总结教训，比人工编写规则更高效。

**实践方法：**

```markdown
1 用户：[Claude 犯了某个错误]
2 用户：更新 CLAUDE.md 确保你不再犯这个错误
3 Claude：[自动总结教训并添加规则]
```

**进阶：** 定期清理 CLAUDE.md，删除不再相关的规则，保持精炼

---

### 4. 创建自己的 Skills 并提交到 Git

>   
> 
> 如果你每天做某件事超过一次，就把它变成 skill 或斜杠命令。团队示例：`/techdebt` 命令找重复代码，一个将 Slack/GDrive/Asana/GitHub 同步到一个上下文的命令，写 dbt 模型的分析代理。
> 
>   

  

**解读：** Skills 是更复杂的可复用工作流，比 Slash Command 更强大。

**Skill 示例（.claude/skills/techdebt.md）：**

```markdown
1 ---
2 name: techdebt
3 description: 找出 3-5 个高杠杆的技术改进点
4 disable-model-invocation: true
5 ---
6  
7 在当前代码库中找出 3-5 个高杠杆的技术债务改进点：
8 - 重复代码
9 - 过时的依赖
10 - 缺失的测试覆盖
11 - 可以简化的复杂逻辑
```

  
---

### 5. Claude 自己修复大多数 Bug

> 把 Slack 的 bug 讨论线程粘贴给 Claude，然后说"fix"。或者说"去修复失败的 CI 测试"。不要微观管理怎么做。你也可以让 Claude 查看 docker logs 来排查分布式系统问题。

**解读：** 信任 Claude 的问题解决能力，给它原始信息让它自己找方案。

**有效提示词示例：**

```markdown
 去修复失败的 CI 测试。运行测试套件，找到根本原因，应用最小修复，保持测试通过。
```

  

**不要：** “看看 test_user.py 第 42 行，我觉得可能是…”（过度指导）

---
### 6. 提升提示词技巧

> 挑战 Claude——说"审查这些改动，不让我通过你的测试就不要创建 PR"。在一个平庸的修复后，说"基于你现在知道的一切，推翻这个方案，实现优雅的解决方案"。写详细的规格说明并减少模糊性——越具体，输出越好。

**解读：** 高质量输入产生高质量输出。几个强力提示词模式：

|场景|提示词|
|---|---|
|确保质量|“审查这些改动，除非我通过你的测试否则不要创建 PR”|
|要求重做|“基于你现在知道的一切，推翻这个，实现优雅的方案”|
|验证工作|“证明给我看这能工作。比较 main 分支和这个分支的行为（测试/日志/输出差异）”|

---

### 7. 终端和环境配置

> 团队喜欢 Ghostty。使用 `/statusline` 显示上下文使用量和 git 分支。给终端标签页设置颜色编码。使用语音输入——你说话的速度是打字的 3 倍（macOS 上按 fn 两次）。

**解读：** 好的工具链能显著提升效率。

**推荐配置：**

- **终端**：Ghostty（团队多人推荐）或 iTerm2
- **Statusline**：`/statusline` 实时显示上下文窗口使用率和当前分支
- **标签颜色**：每个任务/worktree 一个颜色
- **语音输入**：macOS 按 `fn` 两次激活，详细描述需求比打字快 3 倍

---

### 8. 善用 Subagents

> 当你想让 Claude 投入更多算力解决问题时，说"use subagents"。把任务交给子代理可以保持主上下文窗口干净。你也可以通过 hook 将权限请求路由到 Opus 4.5 自动批准安全的请求。

**解读：** Subagent 适合那些会消耗大量上下文的任务。

**触发方式：** 在提示词末尾加 “use subagents”

**适用场景：**

- 大量测试输出分析
- 海量日志搜索
- 深度代码库扫描
- 并行调查多个问题

---
### 9. 数据和分析工作

> 使用 Claude 配合 `bq` CLI（或任何数据库 CLI/MCP/API）来拉取和分析指标。Boris 说他已经 6 个多月没写过一行 SQL 了。

**解读：** Claude 擅长数据查询和分析，让它直接操作数据库工具。

**示例交互：**

```
1 用户：过去 7 天的日活用户趋势如何？
2 Claude：[调用 bq CLI 执行查询，分析结果，生成洞察]
```

---

### 10. 使用 Claude 学习

> 在 `/config` 中启用"Explanatory"或"Learning"输出风格，让 Claude 解释其改动背后的原因。你也可以让 Claude 生成可视化 HTML 演示，绘制代码库的 ASCII 图，或构建间隔重复学习技能。

**解读：** Claude 不仅是生产力工具，还是学习工具。

**学习模式配置：**

```markdown

/config2 → 输出风格：Explanatory 或 Learning
```

**学习用途：**

- 让 Claude 解释陌生代码的架构
- 生成协议/系统的 ASCII 图解
- 创建 HTML 演示来理解复杂概念
- 构建个人的间隔重复学习系统

---
## 总结：Boris 工作流的核心理念

Boris 的工作流令人惊讶的地方不是他用了什么特殊配置，而是他**没有**用什么：

✅ **他用的：**

- 并行化（15-20 个会话）
- Plan Mode
- CLAUDE.md
- 验证循环
- 团队协作

❌ **他没用的：**

- 花哨的定制
- 复杂的 hack
- 危险的权限跳过
**核心公式：**
```
多会话并行 + 好的计划 + 持续的 CLAUDE.md 积累 + 可靠的验证 = 高效产出
```

正如 Boris 所说：

> “Claude Code 开箱即用效果就很好，所以我个人并没有做太多定制。”

---

## 快速入门检查清单
如果你是 Claude Code 新手，按以下顺序逐步采用这些实践：
- [ ] 学会使用 Plan Mode（Shift+Tab 两次）
- [ ] 创建项目的 CLAUDE.md 文件
- [ ] 设置一个常用的 Slash Command
- [ ] 配置 `/permissions` 白名单
- [ ] 尝试并行运行 2 个 Claude 会话
- [ ] 为你的领域建立验证机制
- [ ] 探索 Git Worktrees 实现更多并行
- [ ] 逐步添加 Skills 和 Subagents

  

记住：从小处开始，经常迭代，让"证明它能工作"成为完成的定义。
 
    无限 token 的用法
 
    哈哈，触发痛点，和国内的 kimi glm 结合着用，也可以无限续杯
    
 