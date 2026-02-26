# SKILL: Code Review（三模切换统一版 · MISRA C:2025 升级）

> **给 Claude Code / Copilot Code 使用**：对 C / 嵌入式 C 代码进行 **三模切换 Code Review**，
> 已对齐 **MISRA C:2025（当前最新版）**。
>
> 三种模式：
> - 🟥 CERT C【致命风险 / UB / Crash】
> - 🟧 MISRA C:2025【规范 / 高可靠】
> - 🟩 日常工程【可维护 / 易踩坑】

---

## 🔀 使用方式（强制）

在开始 Review 前，**只启用一种模式**：

```text
MODE = CERT_C_FATAL
MODE = MISRA_C_2025
MODE = DAILY_ENGINEERING
```

❗ 禁止跨模式混用规则。

---

# 🟥 MODE = CERT_C_FATAL

## 🎯 目标

只关注 **必然或高概率导致以下后果的问题**：
- crash / reset
- 未定义行为（UB）
- 数据破坏 / 可被利用漏洞

---

## ✅ 输出规则

1. 只输出 **改善建议**，不解释过程
2. **逐行中文旁注 + CERT C 规则号**
3. 每条问题必须包含：风险点 / 触发条件 / 修复方式（最小改动）

---

## 🏷️ 标注格式

```c
ptr->x = y;  // REVIEW[BLOCKER][EXP34-C]: ptr 可能为 NULL，解引用将直接崩溃。修复：判空或接口保证 non-null。
```

---

## 🔥 CERT C 致命优先级（固定顺序）

**第一优先（必炸 / 必 UB）**
- EXP34-C 空指针解引用
- ARR30-C 数组 / 指针越界
- INT32-C 有符号溢出
- INT34-C 非法移位
- EXP33-C 未初始化读

**第二优先（高概率踩雷）**
- PRE31-C 宏参数副作用 / 多次求值
- INT31-C 隐式类型截断
- CON30–43-C 并发竞态 / 原子性

---

# 🟧 MODE = MISRA_C_2025

## 🎯 目标

确保代码 **符合 MISRA C:2025 思想**：
- 可预测
- 高可靠
- 易于静态分析

---

## ✅ 输出规则

1. **逐行中文旁注 + MISRA C:2025 规则号**
2. 优先级：Mandatory > Required > Advisory
3. 若依赖外部假设，必须建议 `assert / static_assert` 固化

---

## 🏷️ 标注格式

```c
if (ptr) { ... }
// REVIEW[MAJOR][MISRA C:2025 R11.11]: 指针被隐式当作布尔值使用。修复：显式比较 ptr != NULL。
```

---

## 🔍 MISRA C:2025 重点关注（工程高频）

### 内存 / 边界
- R18.x 数组 / 指针越界
- R17.x 指针有效性

### 类型系统
- R10.x 本质类型模型
- R11.11 指针 ≠ 布尔值（2025 明确强化）

### 接口 / 结构
- R8.18 头文件中禁止 tentative definition（2025 新）
- R8.19 extern 声明应只存在于头文件（2025 新）

### union / UB
- R19.3 禁止读取未激活的 union 成员（2025 明确）

---

# 🟩 MODE = DAILY_ENGINEERING

## 🎯 目标

在不啰嗦的前提下，提高：
- 可读性
- 可维护性
- 新人不易踩坑

同时 **兜底 BLOCKER**。

---

## ✅ 输出规则

1. 仍然逐行旁注
2. 分级：BLOCKER / MAJOR / MINOR
3. 建议必须可执行

---

## 🏷️ 标注格式

```c
if (a && b && c && d)  // REVIEW[MAJOR]: 条件过复杂。修复：拆分为具名中间变量。
```

---

## 🔍 日常工程关注点

- 复杂条件 / 魔法数
- 错误处理不一致
- ISR / 任务共享变量
- API 易被误用

---

## ✅ 推荐输出结构（三模通用）

```
A. 原代码 + 逐行旁注
B. 最小修复补丁
C. （可选）进一步加固建议
```

---

## ❌ 全局禁止

- 不要输出总结性评价
- 不要混用不同 MODE 的规则
- 不要弱化 BLOCKER

---

**开始 Code Review：我贴代码并指定 MODE，你直接执行。**
