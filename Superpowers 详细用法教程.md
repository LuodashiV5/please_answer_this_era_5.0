**项目地址**：[https://github.com/obra/superpowers](https://github.com/obra/superpowers)  
**作者**：Jesse Vincent (obra)  
**适用平台**：主要为 Claude Code（Claude 的编码环境），也支持 Codex、OpenCode 等编码代理工具。  
**核心理念**：这是一个“代理技能框架”（agentic skills framework），通过一系列可组合的“技能”（skills）强制 AI 编码代理遵循严格、可靠的软件开发方法论，而不是随意写代码。强调 TDD（测试驱动开发）、系统化调试、详细规划、子代理协作等，避免 AI 的常见问题（如跳过测试、过度复杂化）。

这个项目不是一个简单的系统提示词，而是一个插件化的技能系统。每个技能都是一个 Markdown 文件（SKILL.md），定义了特定场景下的严格规则。AI 在处理任务时必须先检查并调用相关技能。

**适合所有开发者**：无论你是初学者还是资深工程师，这个框架能让你的 AI 编码助手输出更可靠、生产级的代码。使用后，AI 不会直接冲上去写代码，而是先 brainstorm → plan → TDD 实施。

## 1. 安装指南

### Claude Code（最推荐，内置插件市场）

1. 打开 Claude Code。
2. 输入命令注册市场：
    
    ```bash
    /plugin marketplace add obra/superpowers-marketplace
    ```
    
3. 安装 Superpowers 插件：
    
    ```bash
    /plugin install superpowers@superpowers-marketplace
    ```
    
4. 重启 Claude Code（或新开会话）让插件生效。
5. 验证安装：
    
    ```bash
    /help
    ```
    
    你应该看到新命令，如：
    - `/superpowers:brainstorm` - 交互式设计精炼
    - `/superpowers:write-plan` - 创建实施计划
    - `/superpowers:execute-plan` - 批量执行计划

**更新插件**：

```bash
/plugin update superpowers
```

技能会自动更新。

### 其他平台（Codex / OpenCode）

- 对于 Codex：  
    在会话中告诉 AI：
    
    ```ruby
    Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md
    ```
    
- 对于 OpenCode：
    
    ```ruby
    Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.opencode/INSTALL.md
    ```
    

详细文档在仓库的 `docs/` 目录。

## 2. Superpowers 如何工作

- **技能发现与调用**：AI 在收到任何任务时，必须先检查是否有相关技能适用（即使只有 1% 可能）。使用 `Skill` 工具加载技能文件，然后严格遵循。
- **强制性**：技能不是建议，而是必须遵守的规则。违反规则（如先写代码再写测试）会被视为错误。
- **核心规则**（来自 `using-superpowers` 技能）：
    - 在任何响应前，先调用相关技能。
    - 如果技能有 checklist，就逐项创建 todo。
    - 多技能时优先级：过程技能（如 brainstorming） > 实现技能。

## 3. 基本工作流

典型开发流程（AI 会自动触发）：

1. **Brainstorming**：AI 先理解需求，通过一问一答精炼设计，探索多种方案，分段呈现设计供你确认。最终保存到 `docs/plans/`。
2. **创建工作空间**：使用 git worktrees 创建隔离分支，避免污染主分支。
3. **Writing Plans**：把设计拆成极小的任务（每个 2-5 分钟），每个任务包含精确文件路径、完整代码、测试命令、预期输出。
4. **执行计划**：
    - 子代理驱动（subagent-driven-development）：主 AI 派发子代理逐任务实施 + 双阶段审查。
    - 或批量执行（executing-plans）：在独立会话中批量跑，定期检查点。
5. **TDD 强制**：每个实现都必须 Red → Green → Refactor，且必须看到测试先失败。
6. **代码审查**：任务间自动请求审查。
7. **完成分支**：测试全通过后，提供合并/PR/丢弃选项。

AI 会自动检查技能，并在合适时机触发。

## 4. 关键技能详解与实例

仓库有 14 个核心技能（在 `skills/` 目录）。每个技能都是一个 SKILL.md 文件。下面挑选几个最重要的，完整贴出内容（作为“提示词实例”），让你明白技能的严格性。

### 4.1 using-superpowers（入门必读：如何使用技能系统）

这是第一个技能，建立整个框架规则。

**完整内容**：

```mipsasm
How to Access Skills
- In Claude Code: Use the Skill tool. ...
The Rule
Invoke relevant or requested skills BEFORE any response or action. Even a 1% chance...
Red Flags（常见借口表）
Skill Priority
...
```

（内容很长，核心是：任何任务前先调用技能，拒绝“简单问题就不用”的借口。）

### 4.2 brainstorming（需求精炼与设计）

**触发时机**：任何创意工作前（新功能、修改行为）。

**完整内容示例**（节选关键部分）：

```sql
Help turn ideas into fully formed designs...
Process:
- Check project state first
- Ask questions one at a time
- Propose 2-3 approaches with trade-offs
- Present design in 200-300 word sections, validate each
After Design:
- Save to docs/plans/YYYY-MM-DD-<topic>-design.md
- Ask if ready for implementation
Key Principles: YAGNI, explore alternatives, incremental validation
```

**使用实例**：  
你说：“帮我建一个 Todo List app”。  
AI 会先调用 brainstorming：

- 先问：“这是 Web app 还是命令行？用什么技术栈？”
- 提出 3 种方案（React + Flask、纯 Flask、Next.js 等）。
- 分段呈现设计，你确认后保存文档。

### 4.3 test-driven-development（TDD，超级严格）

**触发时机**：任何实现或 bugfix 前。

**完整内容示例**（包含代码实例）：

```css
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
Red-Green-Refactor 流程图...
Example: Bug Fix (empty email)
RED:
test('rejects empty email', () => { ... });
Verify RED: 运行看到失败
GREEN: 写 minimal code
...
Common Rationalizations（各种借口表）
Verification Checklist
```

**代码实例**（来自技能文档）：

```typescript
// RED：写失败测试
test('rejects empty email', async () => {
  const result = await submitForm({ email: '' });
  expect(result.error).toBe('Email required');
});

// Verify RED
$ npm test
// 预期：FAIL with "function not defined"

// GREEN：最小实现
function submitForm(data: FormData) {
  if (!data.email?.trim()) {
    return { error: 'Email required' };
  }
}

// Verify GREEN
$ npm test
// 预期：PASS
```

**为什么这么严格**：防止 AI 写“看起来对”的代码而不验证。

### 4.4 writing-plans（写实施计划）

**触发时机**：设计确认后。

**完整内容示例**：

```yaml
每个任务 2-5 分钟...
Plan Header 必须有...
Task Structure:
### Task N: ...
Files: Create/Modify/Test 精确路径
Step 1-5: 写测试 → 运行失败 → 写代码 → 运行通过 → commit
Execution Handoff: 提供子代理或并行会话选项
```

**计划文档实例**（AI 会生成类似）：

````markdown
# Todo List Implementation Plan

**Goal:** Build a simple command-line todo app

### Task 1: Add command parsing

**Files:**
- Create: src/cli.py
- Test: tests/test_cli.py

**Step 1: Write failing test**
```python
def test_add_command():
    result = parse_args(['add', 'buy milk'])
    assert result.command == 'add'
````

**Step 2: Run test...** (包含命令和预期)  
...

```markdown

## 5. 实际使用技巧与常见坑

- **开始新项目**：直接告诉 AI 你的想法，它会自动触发 brainstorming。
- **并行开发**：AI 会用 git worktrees 创建隔离分支，支持多任务并行。
- **子代理模式**：适合长时间任务，AI 可以自主工作几小时。
- **常见坑**：
  - 别试图绕过技能（AI 会拒绝）。
  - 如果 AI 没触发技能，手动用 `/superpowers:xxx` 命令。
  - 技能更新后，用 `/plugin update` 刷新。
- **扩展**：想加新技能？Fork 仓库，按 `writing-skills` 技能指南创建。

## 6. 更多资源

- 博客详解（含实际转录示例）：https://blog.fsck.com/2025/10/09/superpowers/
- 贡献：Fork → 新分支 → 按 writing-skills 创建技能 → PR
- 支持作者：GitHub Sponsors

用这个框架后，你的 AI 编码助手会从“随便写”变成“专业工程师级别”。强烈推荐安装试试，特别是 Claude Code 用户！

# Superpowers 项目详细用法教程（重点：大量提示词实例）

**项目地址**：https://github.com/obra/superpowers  
**作者**：Jesse Vincent (obra)  
**最新更新**（截至 2026 年 1 月）：共有 14 个核心技能，框架强调自动触发技能，实现严格的软件工程流程（brainstorming → planning → TDD → subagent execution → review）。  
**核心优势**：AI 不会直接写代码，而是强制先精炼需求、写计划、测试驱动、子代理执行、代码审查。适合任何开发者使用 Claude Code 等工具提升代码可靠性。

本版教程重点增加**大量提示词实例**（用户发给 AI 的消息），包括：
- 自然语言提示（AI 自动触发技能）
- 手动命令触发（/superpowers:xxx）
- 不同场景下的完整对话流程示例

这些实例基于项目 README、技能描述和工作流逻辑，让你直接复制使用。

## 1. 安装指南（Claude Code 最推荐）

1. 打开 Claude Code（或 Claude 的代码模式会话）。
2. 注册市场：
```

/plugin marketplace add obra/superpowers-marketplace

```markdown
3. 安装插件：
```

/plugin install superpowers@superpowers-marketplace

```markdown
4. 更新技能：
```

/plugin update superpowers

```markdown
5. 验证：输入 `/help`，看到以下命令：
- `/superpowers:brainstorm` – 交互式需求精炼
- `/superpowers:write-plan` – 生成实施计划
- `/superpowers:execute-plan` – 批量执行计划

**其他平台**（Codex / OpenCode）：告诉 AI 加载对应 INSTALL.md 文件（详见仓库 docs/）。

## 2. 如何使用：提示词核心机制

- **自动触发**：你用自然语言描述任务，AI 会**先检查并调用相关技能**（using-superpowers 技能强制此规则）。不会直接写代码。
- **手动触发**：用 `/superpowers:xxx` 命令强制调用某个技能。
- **工作流顺序**：brainstorming → git worktrees → writing-plans → TDD + subagent execution → code review → finish branch。

## 3. 技能列表（最新 14 个）

- **brainstorming**：需求精炼、探索方案
- **writing-plans**：拆分成 2-5 分钟小任务
- **executing-plans**：批量执行 + 检查点
- **subagent-driven-development**：子代理自主开发 + 双阶段审查
- **test-driven-development**：严格 RED-GREEN-REFACTOR
- **systematic-debugging**：系统化调试
- **requesting-code-review** / **receiving-code-review**：代码审查
- **using-git-worktrees**：隔离分支开发
- **finishing-a-development-branch**：完成分支、合并选项
- **dispatching-parallel-agents**：并行代理
- **verification-before-completion**：验证前不结束
- **using-superpowers**：技能系统入门
- **writing-skills**：创建新技能

## 4. 大量提示词实例（核心内容）

下面是**10+ 个实际可复制的提示词实例**，覆盖从新项目到调试、审查的全流程。每个实例包括：
- 用户提示词
- 预期 AI 行为（触发技能）
- 后续建议

### 实例 1：新项目起步（自然语言，自动触发 brainstorming）

**用户提示词**：
```

我想用 Python 写一个命令行 Todo List 应用，支持 add、list、done、delete 命令，用 JSON 文件存数据。

```markdown

**预期 AI 行为**：
- 自动触发 **brainstorming** 和 **using-superpowers**。
- AI 先问问题精炼需求（一问一答）：
  - “这是纯命令行还是有 Web 界面？”
  - “需要优先级或截止日期吗？”
  - 提出 2-3 种架构方案（argparse vs click，内存 vs 文件持久化）。
  - 分段呈现设计文档，等待你确认每个部分。
- 确认后保存到 `docs/plans/`，问是否进入规划阶段。

**后续用户提示**（确认后）：
```

设计看起来不错，继续吧。

```markdown

### 实例 2：手动强制 brainstorming

**用户提示词**：
```

/superpowers:brainstorm  
我有一个粗糙的想法：建一个 React 的天气 App，用免费 API 显示当前天气和预报。

```markdown

**预期 AI 行为**：
- 直接进入交互式精炼：问技术栈（Vite? Tailwind?）、功能细节、边缘情况（无网络、位置权限）。
- 探索替代方案（如用 Next.js SSR）。
- 最终输出完整设计文档。

### 实例 3：生成实施计划（writing-plans）

**用户提示词**（在 brainstorming 确认设计后）：
```

设计批准了，请生成实施计划。

```undefined

或手动：
```

/superpowers:write-plan  
基于上面的天气 App 设计，生成详细计划。

```markdown

**预期 AI 行为**：
- 触发 **writing-plans** 和 **using-git-worktrees**（先创建隔离分支）。
- 输出 Markdown 计划：每个任务 2-5 分钟，包含精确文件路径、失败测试代码、运行命令、预期 RED/GREEN 输出。
- 示例任务片段：
```

### Task 1: 添加 API Key 配置测试

Files: tests/test_config.py  
Code: 写一个会失败的测试...

```markdown

### 实例 4：执行计划（executing-plans 或 subagent）

**用户提示词**：
```

计划看起来好，去执行吧。

```undefined

或手动选择模式：
```

用 subagent-driven-development 执行计划，让它自主工作。

```undefined

或：
```

/superpowers:execute-plan  
开始批量执行，每 5 个任务后暂停让我检查。

```markdown

**预期 AI 行为**：
- 触发 **subagent-driven-development**（推荐，自主 + 双审查）或批量执行。
- 每个任务严格 TDD：先写失败测试 → 看到 RED → 最小代码 → GREEN → refactor。
- 任务间自动 **requesting-code-review**，报告问题严重度。
- 长任务可自主跑几小时，只在检查点暂停。

### 实例 5：修复 Bug（触发 debugging + TDD）

**用户提示词**：
```

我的代码在添加任务时偶尔崩溃，帮我调试。下面是代码： [贴代码]

```markdown

**预期 AI 行为**：
- 触发 **systematic-debugging** 和 **test-driven-development**。
- 先复现问题（4 阶段根因分析）。
- 强制写失败测试捕获 bug → 看到 RED → 修复 → GREEN。
- 拒绝直接 patch 代码。

**手动强化**：
```

/superpowers:brainstorm 先分析可能的根因。

```markdown

### 实例 6：请求代码审查

**用户提示词**（任务完成后）：
```

这个功能写完了，请做代码审查。

```undefined

或自动在任务间触发，或手动：
```

/superpowers:request-code-review  
审查下面这些变更： [描述或 diff]

```markdown

**预期 AI 行为**：
- 检查是否符合计划、YAGNI、DRY、最佳实践。
- 按严重度报告问题（critical 阻塞）。

### 实例 7：并行开发大项目

**用户提示词**：
```

这个项目有前端和后端，可以并行开发吗？

```markdown

**预期 AI 行为**：
- 触发 **dispatching-parallel-agents**。
- 创建多个 git worktrees，派发子代理同时工作。

### 实例 8：完成分支

**用户提示词**（所有任务后）：
```

全部搞定，准备合并。

```markdown

**预期 AI 行为**：
- 触发 **finishing-a-development-branch**。
- 验证所有测试通过，提供合并/PR/保留/丢弃选项，清理 worktree。

### 实例 9：简单任务（AI 会拒绝跳过技能）

**用户提示词**：
```

快速写一个 Python 函数反转字符串。

```markdown

**预期 AI 行为**：
- **拒绝直接写**！触发 **using-superpowers** 和 **brainstorming**。
- 回应：“即使简单，也需要先精炼需求和 TDD。你确定不需要测试覆盖吗？”
- 引导进入正规流程。

**绕过方式**（如果真想快速）：但不推荐，框架设计就是防这个。

### 实例 10：更新或学习技能系统

**用户提示词**：
```

告诉我怎么用 superpowers 系统。

```markdown

**预期 AI 行为**：
- 触发 **using-superpowers** 技能，详细解释规则和红旗（常见借口表）。

## 5. 使用技巧

- **新手建议**：从一个小项目开始，用自然语言描述，观察 AI 自动触发技能。
- **强制流程**：如果 AI 没触发，用 `/superpowers:xxx` 手动调用。
- **长项目**：让 subagent 自主运行，你只在检查点介入。
- **常见坑**：别试图说服 AI “这很简单不用测试”，它会引用红旗拒绝。

用这些提示词实例，直接在安装好的 Claude Code 中试试！你会看到 AI 从“随意生成代码”变成“专业工程师团队”。
```

本人公众号：比特财商 本人精通java高并发，DDD，微服务等技术实践，专注java,rust技术栈。 本人Eric，坐标深圳，前IBM架构师、咨询师、敏捷开发技术教练，前IBM区块链研究小组成员、十多年架构设计工作经验，《区块链核心技术与应用》作者之一，前huobi机构事业部|矿池defi部技术主管。 现聚焦于：AI+Crypto。 工作微信&QQ：360369487，区块链创投与交易所资源对接，加我注明：博客园+对接，技术咨询和顾问，加我注明：博客园+顾问。想学习golang和rust的同学，也可以加我微信，备注：博客园+golang或博客园+rust,谢谢！