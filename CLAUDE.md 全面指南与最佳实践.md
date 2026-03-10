如果你正在使用 Claude Code（或者 Cursor、Zed 等支持类似机制的 AI 编程工具），可能会遇到一个常见痛点：**AI 总是记不住你的项目规范**。它不知道你对于命名导出和默认导出的偏好，也不知道你的测试命令是 `pnpm test:e2e`，于是每次执行编程任务时它只能一遍又一遍的去读取你的项目代码以获取上述知识，这不仅会增加任务执行的耗时，还会浪费不少 token。

于是工程侧推出了 `CLAUDE.md` 的方案，它是一个 Markdown 文件，角色类似于项目的系统设定，Claude 会在每次会话开始时自动读取它，从而形成对你项目的第一印象。

## 如何创建？

最快的方法是运行命令：

`/init   `

Claude 会根据你的项目结构自动生成一个初版。

**建议**：以 `/init` 为起点，然后大胆删除默认内容，只留下真正重要的信息，因为 Context Window（上下文窗口）是宝贵的，`CLAUDE.md` 里的每一行字都会占用 token。

### 文件位置

你可以把文件放在以下几个地方：

1. **项目根目录**：最常见，推荐提交到 Git，可以让团队其他人共享。
    
2. `.claude/CLAUDE.md`：利于保持根目录整的洁。
    
3. `~/.claude/CLAUDE.md`：用户级全局配置，适用于所有项目。
    
4. `CLAUDE.local.md`：用于存放**不希望提交到 Git** 的个人偏好（比如你个人的编辑器习惯），但要记得把它加到 `.gitignore` 里。
    

> **注意**：文件名区分大小写，必须严格是 `CLAUDE.md`。

## 应该怎么写？

一个优秀的 `CLAUDE.md` 应该包含以下四个核心部分：

1. **项目简介 (Project Context)**一句话讲清楚这个项目是做什么的。
    
    > "这是一个使用 Next.js App Router、Stripe 支付和 Prisma ORM 的电商应用。"
    
2. **代码风格 (Code Style)**明确你的风格偏好，不要只说“代码要规范”，“要具体”这种正确的废话。
    

- TypeScript strict mode，禁止使用 `any`
    
- 必须使用 Named Exports
    
- CSS 仅使用 Tailwind utility classes
    

4. **常用命令 (Commands)**告诉 Claude 怎么干活。
    

- `npm run dev`: 启动开发服务器
    
- `npm run test`: 运行 Jest 测试
    
- `npm run db:migrate`: 执行数据库迁移
    

6. **避坑指南 (Gotchas)**项目里那些特殊的规则和警告。
    

- 认证模块有特殊的重试逻辑，不要改动
    
- API 端点必须包含特定的 Header
    

### 完整示例 (Next.js 项目)

```
# Project: ShopFront
Next.js 14 e-commerce application with App Router, Stripe payments, and Prisma ORM.
## Code Style
- TypeScript strict mode, no `any` types
- Use named exports, not default exports
- CSS: Tailwind utility classes, no custom CSS files
## Commands
- `npm run dev`: Start development server (port 3000)
- `npm run test`: Run Jest tests
- `npm run lint`: ESLint check
- `npm run db:migrate`: Run Prisma migrations
## Architecture
- `/app`: Next.js App Router pages
- `/components/ui`: Reusable UI components
- `/lib`: Utilities and shared logic
## Important Notes
- NEVER commit .env files
- The Stripe webhook handler must validate signatures
- Product images are stored in Cloudinary, not locally
```

## 进阶技巧：保持文件整洁

### 1. 使用 `@imports` 这里引用

不要把所有文档都塞进 `CLAUDE.md`。你可以使用 `@path/to/file` 语法引用其他文件：
See @README.md for project overview   
See @docs/api-patterns.md for API conventions   `

这样不仅让主文件保持清爽，还能按需加载上下文。

### 2. 模块化规则 (`.claude/rules/`)

对于大型项目，你可以在 `.claude/rules/` 目录下创建多个 `.md` 文件（如 `style.md`, `security.md`）。Claude 会自动加载这个目录下的所有规则，无需手动 import。这非常适合不同团队（前端、安全组）维护各自的规范。

### 3. 子目录级配置

如果你的项目是 Monorepo，你可以在子目录（如 `/packages/ui` 或 `/api`）里放单独的 `CLAUDE.md`。Claude 只有在处理该目录下的文件时才会读取它们。

## 维护之道：持续迭代

`CLAUDE.md` 并不是一次性写完就丢的文件，它需要随着项目不断演进。

1. **边做边加 (Organic Growth)：**当 Claude 犯错时（比如用错了日志库），不要只是纠正它一次。告诉它：“Add to CLAUDE.md: always use the logger instead of console.log.” 这样它以后就不会再犯了。
    
2. **从 Code Review 中学习：**PR 里的评论是最好的素材。如果你在 Review 别人代码时指出了一个规范问题，或者别人指出了你的问题，就把这个规则加到 `CLAUDE.md` 里，这能形成一个良性循环：**Code Review 发现问题 -> 更新文档 -> 避免问题再次发生**。
    
3. **定期检查：**每隔几周，让 Claude 自己检查一下这个文件：“Review to `CLAUDE.md` and suggest improvements.” 删除过时的，合并重复的。
    

## 延伸探讨：CLAUDE.md vs AGENTS.md

在 AI 编程领域，你可能还听说过 `AGENTS.md`。它们看起来很像，但定位略有不同。

### 1. 核心区别

- `CLAUDE.md`：是 **Anthropic Claude Code CLI** 的专用配置文件。它针对 Claude Code 的工作流进行了优化（例如支持 `/init` 命令、`.claude/rules/` 自动加载机制等）。
    
- `AGENTS.md`：是一个**开放标准**（由 Agentic AI Foundation 维护）。它的目标是成为所有 AI 编程代理（如 Cursor, Windsurf, Devin, GitHub Copilot 等）通用的“说明书”。
    

### 2. 生态支持

- 如果你的团队全员使用 Claude Code，`CLAUDE.md` 是最佳选择，因为它是原生的，整合度最高。
    
- 如果你的项目是开源的，或者团队成员使用不同的 AI 工具（有人用 Cursor，有人用 Windsurf），那么 **`AGENTS.md`** 更具通用性。目前许多工具都开始支持读取根目录下的 `AGENTS.md` 作为上下文。
    

### 3. 如何共存？

你完全可以两者兼得。一种简单的做法是维护一份核心文档，然后通过软链接或者引用的方式，让两个文件共享同一份内容。这样无论你的协作者使用什么 AI 工具，都能获得一致的项目上下文。

## 总结

`CLAUDE.md` 可以固化你的项目上下文信息以帮助 claude 更高效的理解你的项目，在 claude code 中你可以在项目根目录运行 `/init` 就可以直接生成一份初始化文件，把它当成给你分配的一名实习生：你给它的入职文档越清晰，它干活就越靠谱。

## 参考

- https://www.builder.io/blog/claude-md-guide
    
- https://agents.md/