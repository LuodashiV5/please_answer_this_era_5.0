

上篇文章介绍了Vibe Coding编程的基本流程与使用，本篇主要关于如何Vibe Coding对产品进行升级，通过对 NewsFlow 这个新闻聚合应用进行升级来连接AI工具的使用。

要实现 UX 升级、RSS 集成、智能搜索、视觉创新四大模块，按传统开发节奏至少需要一个月。这次全程使用 **Claude Code** 作为结对编程伙伴。

今天分享的不是 AI 代替开发者的神话，而是介绍**人机协作的真实工作流**——哪些事交给 AI，哪些事需要自己把控，以及如何让 Claude Code 发挥最大价值。

![](https://mmbiz.qpic.cn/mmbiz_png/NsgGHwibEE1BrY8FwltP6cEspYN0mOG1BmLJd5yfXPWibqqr69lmFXCDPbWMf9v8oq8zrvfE4HnPVXnJQJVqAe5rTNHUhlzcTjKWWVrf2ialF4/640?wx_fmt=png&from=appmsg)

## 1🛠️ Claude Code 是什么？

简单说，它是 Anthropic 推出的**命令行 AI 编程助手**。

- 与 IDE 插件不同，Claude Code 直接在终端运行
    
- 可以读写文件、执行命令、运行测试、启动服务
    
- 支持**多 Agent 并行执行**——可以同时让几个 AI Agent 干不同的活
    
- 有 Plan 模式，可以先让 AI 做完整的技术方案，审核后再动手
    

## 2💡工作流一：Plan 模式，让 AI 先“过脑子”

**传统开发的痛点**： 我们通常会先写设计文档（Design Doc），但人脑很难记全整个项目的历史代码。可能会出现**文档写得完美，一写代码全废** ——因为忽略了某个角落里的遗留依赖，导致中途必须推倒重来。

**Claude Code 方式**：进入 /plan 模式，AI 实时扫描代码库 → 发现隐蔽依赖 → 生成基于现状的可执行方案，还可以人为调整方案。

```markdown
我: /plan 给 NewsFlow 添加动态词云功能

Claude Code:📋 Plan Mode正在分析代码库...正在设计实现方案...
计划概要:
- 使用 d3
-cloud 实现词云布局
- 后端添加 /api/news/wordcloud 端点
- 前端创建 WordCloud 组件
- 支持点击筛选、深色主题适配
- 预估工作量: 2 天
详细步骤 [点击展开]...
[批准计划] 
[修改建议] 
[取消]
```

**Plan 模式的价值**：

- 开发前做好计划
    
- AI 读代码库，了解现有架构
    
- 提出的方案遵循现有代码风格
    
- 可以在 AI 写一行代码之前就发现方向性问题
    

## 3🤝 工作流二：并行执行，效率翻倍

Claude Code 可以**多 Agent 并行执行**，目前还升级了 **Agent Teams** 可以让团队成员无缝协同工作。

比如开发 UX 升级模块时，让三个 Agent 同时工作：

```markdown
我: 启动并行任务

Agent 1 (后端):
正在扩展 /api/news 端点，添加 since 参数...
✓ 添加 /api/news/latest 端点
✓ 更新 NewsResponse 模型
✓ 添加缓存逻辑

Agent 2 (前端组件):
正在创建 SkeletonLoader 组件...
✓ 实现 shimmer 动画
✓ 复用 NewsCard 布局结构
✓ 添加可配置数量参数

Agent 3 (测试):
正在编写 E2E 测试...
✓ 骨架屏测试
✓ 主题切换测试
✓ 无限滚动测试

All agents completed in parallel ✅

```

**这相当于同时有三个工程师在干活**，而且它们之间不会产生代码冲突。

## 4🧩 工作流三：AI 写代码，我来 Code Review

Claude Code 生成的代码不是直接提交的，而是进入 Code Review 流程：

```markdown
Agent: 代码已生成，正在启动 code-reviewer Agent...
code-reviewer:
审查文件: frontend/src/components/WordCloud.tsx
发现 1 个 CRITICAL 问题:
- 词云颜色未适配深色主题

发现 2 个 HIGH 优先级问题:
- 缺少错误边界处理
- 未添加 loading 状态
  
修复建议 [点击应用]
```

对于 code-review 出现的问题可以选择：

1. 让 AI 自动修复
    
2. 自己手动修改
    
3. 标记为"可接受"
    

**这种工作流让开发者保持对代码质量的掌控**，AI 是加速器，不是黑盒。

## 5📊 实战案例：RSS 集成模块

这是四个模块中最复杂的一个，涉及后端解析、配置系统、前端重构。NewsFlow 需要聚合来自不同源（少数派、36Kr、个人博客等）的 RSS，痛点是格式标准不一（RSS 2.0）。

以前写这种解析器，需要手动去调试每个字段的映射。而 Claude Code 具有数据归一化（Normalization）能力，它理解了“聚合阅读器”的业务本质。

来看看 Claude Code 是怎么完成的。

### Phase 1: 架构设计（Plan 模式）

```markdown
Claude Code 分析结果:
当前架构问题:
- NewsItem.source 是硬编码字面量类型
- 添加新数据源需要修改 7 个文件
- 无统一配置管理
建议方案:
✓ 使用 JSON 配置文件管理数据源
✓ feedparser 解析 RSS
✓ 渐进式重构，保持向后兼容
✓ 分 4 个 Wave 并行执行
```

### Phase 2: 并行开发

```markdown
Wave 1 (并行):
├── Agent A: 安装 feedparser 依赖
├── Agent B: 创建 data_sources.json 配置
├── Agent C: 实现 RSS 抓取器
└── Agent D: 编写 RSS 单元测试
Wave 2 (Wave 1 完成后启动):
├── Agent E: 重构数据模型
├── Agent F: 重构 API 端点
├── Agent G: 编写 API 测试
└── Agent H: 更新 config.py
```

### Phase 3: 集成测试

```markdown
Agent: 启动集成测试
✓ 向后兼容性测试通过
✓ 标签筛选功能正常
✓ Top 5 统计显示正确
✓ 已读变色功能工作
E2E 测试报告: 16/16 通过
```

## 6🎨 视觉创新模块：词云组件的诞生

动态词云是这次升级的"颜值担当"，功能横跨了后端（Python）和前端（React），是检验 AI 全栈能力的试金石。业务流程：后端提取关键词 -> 前端 D3 可视化 -> 点击交互联动。

### Phase 1: Plan 模式确定方案

```
我: /plan 给 NewsFlow 添加动态词云功能
Claude Code (planner):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# 实现计划：动态热度词云
## 技术选型- 词云布局: d3-cloud（本地实现，无需大模型 API）
- 中文分词: jieba
- 后端 API: GET /api/news/wordcloud
- 前端组件: WordCloud.tsx
## 实现阶段
1. 后端词频统计 API（2h）
2. 前端依赖安装（0.5h）
3. 词云组件开发（4h）
4. 点击筛选交互（2h）
5. 错误处理和优化（2h）
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
我: yes，按此计划执行
```

### Phase 2: 按计划执行

```
Agent (执行):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
正在执行 Phase 1: 后端词频统计 API
✓ 添加 GET /api/news/wordcloud 端点
✓ 集成 jieba 中文分词✓ 添加 TTLCache 缓存（300秒）
✓ 支持 source 和 tags 参数过滤

正在执行 Phase 2: 前端依赖
✓ 安装 d3-cloud, d3, d3-selection, d3-scale

正在执行 Phase 3: 词云组件
✓ 创建 WordCloud.tsx（700+ 行）  
• 骨架屏加载动画（shimmer effect）  
• D3 过渡动画（渐入 + 浮动）  
• 错误状态 UI（重试按钮）  
• 响应式布局（移动端适配）  
• 主题色适配（浅色/深色）

正在执行 Phase 4: 点击筛选交互
✓ 实现点击词语触发新闻筛选
✓ 与现有筛选器集成（AND 逻辑）

正在执行 Phase 5: 错误处理和优化
✓ 添加错误边界
✓ React.memo 优化重渲染
✓ 加载状态管理
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Phase 3: Code Review

```
code-reviewer Agent:
审查文件: frontend/src/components/WordCloud.tsx
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✓ TypeScript 类型安全
✓ React.memo 优化重渲染
✓ 错误边界完善
✓ 无障碍性标签完整
✓ 性能优化
✓ 主题色正确适配
代码质量: 优秀
建议: 无需修改
```

## 7🎯 核心经验总结

### ✅ Claude Code 擅长的

1. **重复性代码** - 数据模型、API 端点、CRUD 操作
2. **标准化任务** - 单元测试、E2E 测试、配置文件
3. **并行执行** - 多个独立模块同时开发
4. **代码审查** - 第一轮筛选，我聚焦于架构和业务逻辑

### ⚠️ 需要人工把控的

1. **架构决策** - Plan 模式给出方案。开发人员来拍板。是做单体还是微服务？数据怎么流转？
2. **业务逻辑** - AI 不懂产品，需求必须我明确。
3. **代码审查** - 最终审核权在人手里，Code Review 成了核心工作。
4. **调试疑难问题** - AI 能帮忙，需要开发者引导。

**具体、带上下文、有参考**， Claude Code 的输出质量会高很多。

## 8结语

把AI用好了，Agent就是你的团队成员。**AI 不是替代开发者，而是放大开发者的能力**。

- 开发者花更多时间在**架构设计/审核**和**需求把控**上
    
- Claude Code 负责**代码实现**和**重复劳动**
    
- Code Review 保持**代码质量**在可控范围内
    

    让AI先"过脑子"是指开发前启用Plan模式：AI先扫描整个代码库，理解现有架构和隐藏依赖，生成贴合实际的实施方案。开发者审核确认后再执行，能避免"文档写得完美，一写代码全废"的窘境，在AI写代码前就发现方向性问题，确保每一步都踩在实处。

    文章分享了使用Claude Code升级NewsFlow新闻聚合应用的实战经验，重点介绍三大工作流：Plan模式（AI先分析代码库生成可行方案）、并行执行（多Agent同时处理不同模块）和Code Review（人机协作把控质量）。核心结论是AI擅长处理重复性代码和标准化任务，而开发者应专注架构决策和业务逻辑，实现人机高效协作而非替代。
  