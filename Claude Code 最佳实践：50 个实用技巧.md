# Claude Code 最佳实践：50 个实用技巧



过去 24 小时里，AI Edge仔细研读了 Claude Code 的最新最佳实践文档，并从中提炼出 50  条核心技巧，还加入了一些个人实战经验，整理成这份终极指南。这份清单不仅包含最佳实践，还涵盖了各种 Claude Code  工具和学习资源。下面快速过一遍这些技巧。

## **最核心的技巧**

**1. 如何扩展 Claude Code**
Anthropic 的官方指南：https://code.claude.com/docs/en/features-overview

**2. 钩子**
最适合那些必须每次都执行、零例外的操作。

**3. Claude 超能力**
一个收集 Claude Code 超能力的 GitHub 仓库：https://github.com/obra/superpowers

**4. 慢工出细活**
慢慢来，尤其是在构建重要工作流程时。计划、计划、计划，然后才执行。

**5. 安全自主模式**
使用 `claude --dangerously-skip-permissions` 绕过所有权限检查，让 Claude 不间断工作。这对修复 lint 错误或生成样板代码等工作流程特别有效。

**6. Claude Code 最佳实践（文档）**
最新文档链接：https://code.claude.com/docs/en/best-practices

**7. Boris 的配置**
Claude Code 创建者如何最大化使用 Claude Code 的配置清单。

**8. Claude 课程**
来自 Coursera 的课程：https://www.anthropic.com/learn

**9. 实战学习 Claude Code**
Anthropic 的学习资源：https://www.anthropic.com/learn

**10. Notion 数据库**
把 Notion 数据库连接到 Claude，用来存储最佳和最常用的提示词。

## **调试、错误处理、常见失败模式**

**11. 上下文窗口管理**
Claude 的上下文窗口很快就会填满。发生这种情况时，Claude 可能开始忘记之前的指令。这个页面能帮助解决问题：https://code.claude.com/docs/en/costs[#reduce](javascript:;)-token-usage

**12. 调试项目**
创建一个专门用于调试代码的 AI 项目（Grok 4 Heavy 擅长调试）。

**13. 无限探索**
让 Claude"调查"某事但没有限定范围。Claude 读取数百个文件，填满上下文。解决办法：缩小调查范围或使用子代理，这样探索就不会占用主上下文。

**14. 逐步回放**
让 Claude 逐行解释它是如何生成答案的。

**15. 过度纠正**
Claude 做错了，纠正它，还是错，再纠正。上下文被失败的尝试污染了。解决办法：两次纠正失败后，运行 `/clear` 并结合学到的经验写一个更好的初始提示词。

**16. 不要犯这个错误**
开始做一个任务，然后问些不相关的问题，再回到第一个任务。上下文就充满了无关信息。解决办法：在不相关任务之间运行 `/clear`。

**17. CLAUDE.md 过度指定**
如果 CLAUDE.md 文件太长，Claude 会忽略掉一半内容，因为重要规则淹没在噪音中。解决办法：无情地精简。如果 Claude 在没有某条指令的情况下也能正确完成，就删掉它或者转换成钩子。

**18. 回滚提示词**
回到最后一个正常工作的提示词，然后逐步重新应用更改。

**19. 错误复现**
让 Claude 故意复现错误，以便理解问题所在。

**20. 步骤隔离**
只重新运行出错的步骤，而不是重新生成所有内容。

## **被低估的小技巧（大多数人不知道）**

**21. 运行多个会话**
有两种主要方式运行并行会话：Claude 桌面版可以可视化管理多个本地会话，每个会话都有独立的工作树；Claude 网页版在 Anthropic 的安全云基础设施上运行，使用隔离的虚拟机。

**22. 回退**
双击 ESC 或运行 `/rewind` 打开检查点菜单。

**23. 清空**
运行 `/clear` 开启全新会话。

**24. 频繁纠正**
经常性地调整 Claude 的方向。一旦它开始偏离轨道，立即停止（按 ESC 可以中途打断 Claude）。

**25. Claude 访谈**
对于大型项目，先让 Claude 来采访开发者。从一个最简单的提示词开始，让 Claude 使用 AskUserQuestion 工具进行提问。

**26. 在 Claude Code 中学习 Claude Code**
一个直接在 Claude Code 里教学的课程：https://ccforeveryone.com/

**27. 安装插件**
运行 `/plugin` 浏览市场。插件能添加技能、工具和集成功能，而且无需任何配置。

**28. 输出评分**
让 Claude 根据预设的成功标准给自己的答案打分。

**29. 创建自定义子代理**
在 `.claude/agents/` 目录下定义专门的助手，让 Claude 可以把独立任务委派给它们处理。

**30. 模型堆叠**
在打开 Claude Code 之前，先用其他大模型规划项目、生成高级超级提示词——这个策略还能节省计划模式的 token。

## **项目与技能使用**

**31. 项目记忆存储**
项目记忆可以存储在 `./CLAUDE.md` 或 `./.claude/CLAUDE.md` 文件中。

**32. Claude 技能库**
一个很酷的网站，提供即插即用的技能等资源：https://mcpservers.org/claude-skills

**33. Claude 技能仓库**
一个收录了 80,000+ Claude 技能的库：https://skillsmp.com/

**34. 项目上下文污染**
把不相关的工作流分到不同项目中，防止上下文相互干扰。

**35. 项目卫生**
定期清理记忆、文件和指令，防止内容漂移。

**36. 技能版本控制**
在优化工作流程时，复制并创建新版本的技能，而不是直接编辑正在使用的。

**37. 从示例创建技能**
把一个优秀的输出结果粘贴给 Claude，让它转化成可复用的技能。甚至可以上传截图让 Claude 照着复制，然后转化成技能——这是创建精英技能的简单方法。

**38. Claude 技能**
使用技能把重复性工作流程固化下来，避免反复输入提示词。

**39. 项目记忆**
编辑"记忆"标签页，精确控制 Claude 应该长期保留或忽略哪些内容（这在项目中也适用）。

**40. 项目指令**
使用项目级指令来定义长期行为模式，而不是每次都重复提示词。

## **基础核心技巧**

**41. Claude.md 技巧**
运行 `/init` 可以为当前项目生成一个初始的 Claude.md 文件。

**42. 丰富的上下文**
使用 @ 来链接文件、数据和图片。

**43. 假设零上下文**
假设 Claude 对项目一无所知，把它需要知道的所有信息都告诉它。

**44. 提供具体上下文**
指令越精确，效果越好。Claude 只能根据给出的信息进行推断。

**45. 先探索、再计划、后编码**
先做研究（这个过程可以借助其他大模型），然后进入计划模式，最后切回普通模式执行代码。

**46. Chrome 扩展妙用**
UI 变更可以用 Claude Chrome 扩展来验证。它会打开浏览器，测试界面，然后不断迭代直到代码正常运行。

**47. 提示词结构建议**
为了让前面几条建议更实用，推荐这个提示词结构：[角色] + [任务] + [上下文]

**48. 给 Claude 自我验证的方法**
提供测试用例、截图或预期输出，让 Claude 能够自己检查工作成果。这是性价比最高的做法，没有之一。

**49. 前置重要指令**
永远把最重要的指令放在提示词的最顶部。

**50. 明确任务框架**
开门见山地说清楚想让 Claude 做什么，别绕弯子。

这份完整的 50 条技巧清单涵盖了从基础到进阶的各个层面，无论是刚接触 Claude Code 的新手，还是想要提升效率的老手，都能从中找到有用的方法。关键是要根据实际项目需求灵活运用这些技巧，而不是生搬硬套。


# 50 条 Claude Code 小技巧，帮你用 Claude 构建更稳更快的项目，这些内容往往很多人从不提。


过去 24 小时里，我把新版《Claude Code Best Practices》文档通读了一遍。

## Claude Code 最新最佳实践

我把文档里的最佳实践全部提炼出来，并结合一些个人经验补充，整理成这份“终极 Claude Code 最佳实践清单”。

清单里也包含若干 Claude Code 工具与学习资源。

快速连发风格——开始。

## 基础要点

**50. 清晰任务框定** —— 一开始就明确告诉 Claude：你要它具体做什么。  
**49. 关键指令前置** —— 永远把最重要的要求放在提示词最顶部。  
**48. 给 Claude 自检的方法** —— 提供测试、截图或期望输出，让它能验证自己是否做对。这是你能做的、杠杆最高的一件事。  
**47. 提示词结构建议** —— 为了让以上技巧真正落地，我常用这套结构：  
`[角色] + [任务] + [上下文]`  
**46. Chrome 扩展建议** —— UI 改动可以用 Claude 的 Chrome 扩展来验证：它会打开浏览器、测试 UI、迭代直到代码可用。  
**45. 先探索，再规划，再编码** —— 先调研（也可用其他 LLM 辅助），再进入 Plan Mode，再回到普通模式执行代码。  
**44. 在提示词里给足具体上下文** —— 你的指令越精确越好；Claude 只能基于你给的内容推断上下文。  
**43. 假设零上下文** —— 假设 Claude 对你的项目一无所知，把它“需要知道的一切”讲清楚。  
**42. 富上下文** —— 用 `@` 链接文件、数据和图片。  
**41. Claude.md 提示** —— 运行 `/init` 为当前项目生成一个起步版 `CLAUDE.md` 文件。

## Projects & Skills 的使用

**40. 项目级指令** —— 用项目级指令定义长期行为，不要在每次对话里重复同样的要求。  
**39. 项目记忆** —— 编辑 “Memory” 标签页，精确控制 Claude 在时间维度上要保留什么、忽略什么（项目中同样生效）。  
**38. Claude Skills** —— 把可重复的工作流做成 Skill，而不是反复重写提示词。  
**37. 从示例生成 Skill** —— 把一份很棒的输出贴给 Claude，让它改造成可复用 Skill。你甚至可以上传截图，让它复刻并沉淀为 Skill（创建“高阶技能”的捷径）。  
**36. Skill 版本管理** —— 随着工作流演进，复制并版本化 Skills，而不是直接修改线上正在用的那一个。  
**35. 项目卫生** —— 定期清理 memory、文件与指令，避免项目“漂移”。  
**34. 项目上下文串味（Context Bleed）** —— 不相关的工作流用不同项目隔离，防止上下文互相污染。  
**33. Claude Skills Repo** —— 一个包含 80,000+ Claude Skills 的库：  
https://skillsmp.com/  
**32. Claude Skills Library** —— 一个可即插即用的 Skills 网站（以及更多资源）：  
https://mcpservers.org/claude-skills  
**31. 项目记忆位置** —— Project memory 可存放在 `./CLAUDE.md` 或 `./.claude/CLAUDE.md`。

## 被低估的小技巧

**30. 模型叠加（Model Stacking）** —— 在打开 Claude Code 前，用其他 LLM 先做项目规划、生成“高级 mega prompt”；这也能节省 Plan Mode 的 token。  
**29. 自定义子代理（Subagents）** —— 在 `.claude/agents/` 里定义专用助手，Claude 可把隔离任务委派给它们执行。  
**28. 输出打分** —— 让 Claude 按你预先定义的成功标准给自己的回答打分。  
**27. 安装插件** —— 运行 `/plugin` 浏览市场；插件能在无需配置的情况下添加技能、工具与集成。  
**26. 在 Claude Code 里学 Claude Code** —— 一个“直接在 Claude Code 内教学”的课程：  
https://ccforeveryone.com/  
**25. Claude 访谈** —— 面对大型项目，先让 Claude 采访你：用最小提示启动，并让它用 AskUserQuestion 工具对你提问。  
**24. 频繁纠偏** —— 经常纠正 Claude。一旦开始跑偏就立刻停止（按 `ESC` 可在中途打断动作）。  
**23. 清屏** —— 运行 `/clear` 开启一个干净会话。  
**22. 回退** —— 双击 `ESC` 或运行 `/rewind` 打开检查点菜单。  
**21. 多会话并行** —— 并行会话主要有两种方式：
- **Claude Desktop**：可视化管理多本地会话；每个会话拥有隔离的 worktree。
- **Claude Web**：在 Anthropic 的安全云基础设施中，以隔离 VM 运行。

## 调试、错误处理与常见失败模式

**20. 步骤隔离** —— 只重跑出问题的那一步，不要把整条链路全部重生成。  
**19. 复现错误** —— 让 Claude “刻意复现失败”，以便理解问题本质。  
**18. 回滚提示词** —— 回到上一次已知可用的 prompt，然后一次只改一个变量。  
**17. 过度冗长的 CLAUDE.md** —— 如果 `CLAUDE.md` 太长，Claude 往往会忽略一半内容，因为关键规则被噪声淹没。  
**修复：** 无情裁剪。若没有该指令 Claude 也能做对，就删掉；或把它改成 hook。  
**16. 别犯这个错** —— 你从一个任务开始，又问了无关问题，再回到第一个任务；此时上下文塞满了无效信息。  
**修复：** 不相关任务之间使用 `/clear`。  
**15. 过度纠正（Over-Correcting）** —— Claude 做错，你纠正；仍错，再纠正……上下文里堆满失败尝试。  
**修复：** 连续两次纠正仍失败，就 `/clear`，把你学到的东西融入一份更好的初始 prompt。  
**14. 逐行回放** —— 让 Claude 逐行讲解它是如何生成答案的。  
**13. 无限探索陷阱** —— 你让 Claude “去调查”却不设边界；它会读几百个文件，把上下文塞爆。  
**修复：** 把调查范围收紧；或用 subagents，让探索不消耗主上下文。  
**12. 专用调试项目** —— 建一个专门用于调试代码的 AI 项目（Grok 4 Heavy 擅长调试）。  
**11. 上下文窗口管理** —— Claude 的上下文窗口很快会被填满，随后可能开始遗忘早期指令。这个页面能帮你消除该问题：  
https://code.claude.com/docs/en/costs#reduce-token-usage

## 结尾建议

**10. Notion 数据库** —— 把 Notion 数据库连接到 Claude，用于沉淀你最常用、最有效的 prompts。 
**9. 观看 Claude Code 的实战学习** —— Anthropic 的学习资源：  
https://www.anthropic.com/learn  
**8. Claude 课程** —— Coursera 上的课程入口：  
https://www.anthropic.com/learn  
**7. Boris 的配置** —— Claude Code 创建者如何把 Claude Code 用到极致：  
Boris' Claude Code Setup Cheatsheet  
**6. Claude Code Best Practices（文档）** —— 最新官方文档链接：  
https://code.claude.com/docs/en/best-practices  
**5. 安全的自动化模式** —— 用 `claude --dangerously-skip-permissions` 绕过所有权限检查，让 Claude 不间断工作；适用于修复 lint 错误、生成样板代码等流程。  
**4. 慢即是快** —— 尤其在搭建严肃工作流时：慢下来。规划、规划、再规划，然后再执行。  
**3. Claude 超能力** —— 一个收集 Claude Code 超能力的 GitHub 仓库：  
https://github.com/obra/superpowers  
**2. Hooks** —— 适合那些“每次都必须发生、零例外”的动作。  
**1. 如何扩展 Claude Code** —— Anthropic 的官方指南：  
https://code.claude.com/docs/en/features-overview