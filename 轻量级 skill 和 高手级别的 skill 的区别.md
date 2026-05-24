
根据搜索到的资料，我为你整理了**轻量级 Skill vs 高手级 Skill** 的对比案例：

---

## 📊 **对比维度总览**

| 维度 | 轻量级 Skill | 高手级 Skill |
|------|------------|------------|
| **文件大小** | 单个 SKILL.md（<100行） | 完整文件夹结构（500+行） |
| **复杂度** | 简单线性流程 | 多阶段编排+决策树+循环迭代 |
| **功能范围** | 单一任务 | 跨工具、跨Session、多Skill协作 |
| **Token占用** | 500-2K tokens | 2K-10K tokens（按需加载） |
| **适用场景** | 日常重复任务 | 企业级SOP、复杂工作流 |

---

## **案例 1：代码审查（从简单到专业）**

### 🔹 **轻量级：基础代码审查**
```markdown
---
name: simple-code-review
description: 快速审查代码，检查明显问题
---

# 代码审查助手

请检查以下代码：
1. 语法错误
2. 明显的性能问题
3. 代码规范

输出格式：
- ✅ 优点
- ⚠️ 建议
- ❌ 问题
```

**特点**：
- ✅ 30行搞定
- ✅ 适合快速检查
- ❌ 缺乏深度分析
- ❌ 没有强制执行机制

---

### 🔥 **高手级：TDD（测试驱动开发）Skill**
```markdown
---
name: test-driven-development
description: 严格的TDD工作流，强制执行红-绿-重构循环
allowed-tools: ["edit", "bash", "read"]
---

# 测试驱动开发（TDD）

## 铁律（不可违反）
- **必须**先写失败的测试
- **禁止**跳过验证步骤
- **必须**每个循环都运行测试

## 红-绿-重构循环

### 🔴 RED — 写失败的测试
1. 编写测试用例
2. 运行测试，**必须确认失败**
3. 如果测试通过，删除重来

**验证标准**：
- 测试确实失败（红色）
- 失败原因符合预期

### 🟢 GREEN — 写最少的代码
1. 编写刚好让测试通过的代码
2. 不要过度设计
3. 运行测试，**必须确认通过**

**对比示例**：
<Good>
def add(a, b):
    return a + b
</Good>

<Bad>
def add(a, b):
    # 过度设计
    if not isinstance(a, (int, float)):
        raise TypeError()
    if not isinstance(b, (int, float)):
        raise TypeError()
    # 提前优化
    return a + b if a != 0 else b
</Bad>

### 🔄 REFACTOR — 清理
1. 重构代码，保持测试通过
2. 消除重复
3. 改进命名

### 借口反驳表
| LLM可能的借口 | 反驳 |
|--------------|------|
| "这个测试太简单了，我直接写实现吧" | **删除它，重来**。TDD不是捷径 |
| "我已经知道怎么写了，不用测试" | **你知道的可能是错的**。先证明失败 |
| "这个函数太简单，不用TDD" | **简单代码也会出Bug**。遵守流程 |

### 完成检查清单（必须全部打勾）
- [ ] 所有测试通过
- [ ] 无重复代码
- [ ] 命名清晰
- [ ] 函数<20行
- [ ] 无注释（代码自解释）

## 人类兜底
遇到不确定时：**ask your human partner**
```

**特点**：
- ✅ 371行，堵死所有偷懒路径
- ✅ 强硬语气 + 借口反驳表
- ✅ Good/Bad 对比教学
- ✅ 量化阈值（函数<20行）
- ✅ 循环迭代机制

---

## **案例 2：部署应用（从单步到多平台）**

### 🔹 **轻量级：Vercel 部署**
```markdown
---
name: vercel-deploy
description: 部署应用到 Vercel。当用户说"deploy my app"、"push this live"时触发
---

# Vercel 部署

## 前置条件
- 已安装 Vercel CLI
- 已登录 Vercel 账号

## 部署步骤
1. 运行 `vercel --prod`
2. 等待部署完成
3. 输出部署 URL

## 故障排除
| 问题 | 解决方案 |
|------|----------|
| 未登录 | 运行 `vercel login` |
| 构建失败 | 检查 build 脚本 |
```

**特点**：
- ✅ 77行，线性流程
- ✅ 具体命令
- ✅ 安全默认值（Always deploy as preview）

---

### 🔥 **高手级：Cloudflare 全平台部署**
```markdown
---
name: cloudflare-deploy
description: Cloudflare 全平台部署指南。覆盖 Workers、Pages、R2、D1 等 30+ 产品
references:
  - "workers.md"
  - "pages.md"
  - "r2.md"
---

# Cloudflare 部署专家

## 快速决策树

### "我需要运行代码"
```
├─ 边缘函数 → Workers
│  ├─ 简单API → workers/quick-start.md
│  └─ 复杂应用 → workers/full-app.md
├─ 静态网站 → Pages
│  ├─ 前端框架 → pages/frameworks.md
│  └─ 纯HTML/CSS → pages/static.md
└─ 后端服务 → Cloudflare Run
```

### "我需要存储数据"
```
├─ 对象存储 → R2
├─ 关系数据库 → D1
└─ KV存储 → Workers KV
```

### "我需要 AI/ML"
```
├─ 推理服务 → Workers AI
└─ 向量数据库 → Vectorize
```

## 产品索引表（按需加载）
| 产品 | 用途 | 参考文档 |
|------|------|----------|
| Workers | 边缘函数 | references/workers.md |
| Pages | 静态托管 | references/pages.md |
| R2 | 对象存储 | references/r2.md |
| D1 | SQLite数据库 | references/d1.md |
| ... | ... | ... |

## 认证流程
1. 安装：`npm install -g wrangler`
2. 登录：`wrangler login`
3. 验证：`wrangler whoami`

## 部署 Workers（示例）
```bash
# 创建项目
wrangler init my-worker
cd my-worker

# 配置
cat > wrangler.toml <<EOF
name = "my-worker"
compatibility_date = "2026-01-01"
EOF

# 部署
wrangler deploy
```

## 降级方案
如果自动部署失败：
1. 使用 Dashboard 手动上传
2. 检查日志：`wrangler tail`
3. 回滚：`wrangler rollback`
```

**特点**：
- ✅ 224行，决策树 + 按需加载
- ✅ 覆盖30+产品
- ✅ 用户意图分类（用用户语言）
- ✅ 渐进式披露（主文件7KB，references几十万字）

---

## **案例 3：项目管理（从单次到跨Session）**

### 🔹 **轻量级：会议纪要整理**
```markdown
---
name: meeting-minutes
description: 整理会议纪要，支持周会/复盘会/客户沟通会
---

# 会议纪要助手

## 处理流程
1. 识别会议类型
2. 提取关键信息
3. 输出结构化纪要

## 输出模板
### 会议信息
- 时间：
- 参与人：

### 决议事项
1. 
2. 

### 待办任务
- [ ] 
```

**特点**：
- ✅ 50行，单一场景
- ✅ 固定模板

---

### 🔥 **高手级：跨Session接力棒模式**
```markdown
---
name: stitch-loop
description: 长期项目接力棒模式，跨Session持续推进
type: workflow
---

# 接力棒系统（Baton System）

## 核心概念
使用文件系统存储项目状态，实现跨Session持续工作

## 文件协议
```
project/
├── next-prompt.md        # 接力棒（关键！）
├── context/
│   ├── requirements.md   # 需求文档
│   ├── architecture.md   # 架构设计
│   └── decisions.md      # 决策记录
└── roadmap.md            # 路线图
```

## 执行协议（6步）

### Step 1: 读接力棒
```bash
cat next-prompt.md
```
**必须理解**：
- 上一步完成了什么
- 下一步要做什么
- 当前阻塞点

### Step 2: 查阅上下文
```bash
cat context/requirements.md
cat context/architecture.md
```

### Step 3: 执行任务
根据接力棒指示完成任务

### Step 4: 集成结果
```bash
# 更新相关文件
git add .
git commit -m "完成：任务描述"
```

### Step 5: 更新文档
- 更新 `roadmap.md` 进度
- 记录新的决策到 `decisions.md`

### Step 6: 写下一个接力棒 ⚠️ **关键！**
```markdown
# next-prompt.md（更新）

## 上一步完成
✅ [任务列表]

## 下一步要做
⏭️ [新任务列表]

## 当前状态
📍 [当前位置]

## 注意事项
⚠️ [阻塞点/风险]
```
**MUST：忘记写接力棒，循环就断了！**

## 编排方式
- **人在回路**：人类审查接力棒后继续
- **CI/CD**：自动触发下一个循环
- **Agent链**：多个Agent接力工作
```

**特点**：
- ✅ 203行，文件即状态
- ✅ 跨Session持久化
- ✅ 支持多种编排方式
- ✅ 续命机制（Step 6标记Critical）

---

## **案例 4：产品发现（从单技能到Skill编排）**

### 🔥 **高手级：多阶段产品发现流程**
```markdown
---
name: discovery-process
description: 完整的产品发现流程，6个阶段，调度10+子Skill
type: workflow
best_for:
  - "新产品规划"
  - "功能重构"
  - "技术债清理"
estimated_time: "2-6周"
---

# 产品发现流程

## 核心概念
通过6个阶段，从问题定义到解决方案验证

## Phase 1: 定义问题（1周）
### 活动（调用子Skill）
- `problem-framing` - 问题框架
- `stakeholder-interviews` - 利益相关者访谈
- `market-research` - 市场调研

### 产出
- 问题陈述文档
- 用户画像
- 市场分析报告

### 检查点1
**达到饱和了吗？**
- YES → 进入Phase 2
- NO → +1周（继续访谈）

## Phase 2: 探索方案（1-2周）
### 活动
- `idea-generation` - 头脑风暴
- `solution-sketching` - 方案草图
- `feasibility-analysis` - 可行性分析

### 产出
- 3-5个方案草图
- 技术可行性评估

### 检查点2
**方案足够多吗？**
- YES → 进入Phase 3
- NO → +2-3天（继续探索）

## Phase 3-6: ...（重复相同结构）

## 完整工作流时间线
```
Week 1: Phase 1（问题定义）
  ↓ [Go/No-Go决策]
Week 2-3: Phase 2（方案探索）
  ↓ [Go/No-Go决策]
Week 4: Phase 3（原型设计）
  ↓
Week 5-6: Phase 4-6（测试验证）
```

## 常见陷阱
| 陷阱 | 症状 | 解决方案 |
|------|------|----------|
| 跳过调研 | "我知道用户要什么" | 强制执行至少5次访谈 |
| 过早优化 | "先做出来再说" | Phase 1必须产出问题陈述 |
| 缺乏验证 | "我觉得很好" | 必须有用户测试数据 |

## 引用的子Skill
1. `problem-framing`
2. `stakeholder-interviews`
3. `market-research`
4. `idea-generation`
5. `solution-sketching`
6. `feasibility-analysis`
7. `prototype-design`
8. `user-testing`
9. `metrics-definition`
10. `roadmap-planning`
```

**特点**：
- ✅ 502行，编排器模式
- ✅ 调度10+子Skill
- ✅ 多阶段+检查点
- ✅ 时间影响标注（+1周、+2-3天）

---

## **总结：高手级 Skill 的核心特征**

| 特征 | 轻量级 | 高手级 |
|------|--------|--------|
| **1. 防止偷懒** | 简单指令 | 借口反驳表 + 强硬语气 + 量化阈值 |
| **2. 知识组织** | 单层文档 | 3层架构（Frontmatter → 主文件 → references） |
| **3. 执行机制** | 线性流程 | 循环迭代 + 决策树 + 检查点 |
| **4. 状态管理** | 无状态 | 文件即状态（接力棒模式） |
| **5. 能力扩展** | 单一技能 | 编排多个子Skill |
| **6. 安全性** | 基本提示 | 安全默认值 + 权限最小化 + 人类兜底 |
| **7. 教学性** | 文字说明 | Good/Bad对比 + 完整示例 + 具体命令 |

---

## **培训建议：渐进式学习路径**

### **Level 1：轻量级（1-2周）**
- 创建简单的线性流程Skill
- 学习 Frontmatter 写法
- 掌握基本指令结构

### **Level 2：中级（1个月）**
- 添加决策树
- 编写 references 按需加载
- 实现简单的循环

### **Level 3：高手级（3个月+）**
- 多阶段编排
- 接力棒模式（跨Session）
- Skill编排（调度子Skill）
- 思维框架（控制LLM思考方式）

**金句**：
> **"轻量级Skill是口头交代，高手级Skill是企业级SOP"**  
> **"Prompt让AI做事，Skill让AI按标准做事，高手级Skill让AI在复杂场景下持续按标准做事"**

