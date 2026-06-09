---
name: deepen
description: 发现代码库中的深化机会 — 将浅模块重构为深度模块，提升可测试性与 AI 可导航性。触发场景：改善架构、寻找重构机会、整合紧耦合模块、提升可测试性。
license: MIT
metadata:
  author: Alex
  version: "1.0"
---

# Deepen — 架构深化

> 发现架构摩擦点，提出**深化机会（deepening opportunities）** — 将浅模块重构为深度模块。目标是可测试性和 AI 可导航性。

**Announce:** "使用 deepen skill 进行架构深化分析"

## 术语

使用 [LANGUAGE.md](LANGUAGE.md) 中定义的术语，不要替换为 "component"、"service"、"API" 或 "boundary"。一致性语言是本技能的核心。

关键术语（完整定义见 [LANGUAGE.md](LANGUAGE.md)）：

- **Module** — 任何有 interface 和 implementation 的东西（函数、类、包、切片）
- **Interface** — 调用方使用该 module 需要知道的一切
- **Implementation** — module 内部的代码
- **Depth** — interface 处的杠杆率：小 interface 背后承载大量行为。**Deep** = 高杠杆。**Shallow** = interface 几乎和 implementation 一样复杂
- **Seam** — interface 所在的位置；可以在不修改原处代码的情况下改变行为的地方
- **Adapter** — 在 seam 处满足 interface 的具体实现
- **Leverage** — 调用方从 depth 中获得的收益
- **Locality** — 维护方从 depth 中获得的收益：变更、bug、知识集中在一处

核心原则（完整列表见 [LANGUAGE.md](LANGUAGE.md)）：

- **删除测试（Deletion test）**：想象删除该 module。如果复杂度消失了，说明它是 pass-through；如果复杂度在 N 个调用方重新出现，说明它在发挥作用
- **Interface 就是测试面**
- **一个 adapter = 假设的 seam。两个 adapter = 真正的 seam**

本技能参考项目的领域模型（如果存在）。领域语言为好的 seam 命名；ADR 记录了本技能不应重新讨论的决策。

---

## 流程

### 1. 探索

如果项目有 `CONTEXT.md` 领域术语表，先读取。如果有 ADR 存放在 `docs/adr/`，也一并读取。都不存在则直接继续。

然后使用 Agent 工具（`subagent_type=Explore`）漫游代码库。不要机械套用规则 — 有机地探索，记录你感受到的摩擦：

- 哪里理解一个概念需要在多个小 module 之间跳转？
- 哪里的 module 是**浅的** — interface 几乎和 implementation 一样复杂？
- 哪里为了可测试性提取了纯函数，但真正的 bug 藏在调用方式里（没有 **locality**）？
- 哪里紧耦合的 module 跨 seam 泄漏？
- 哪些部分没有测试，或者通过当前 interface 难以测试？

对你怀疑是浅的 module 应用**删除测试**：删掉它会集中复杂度，还是仅仅转移它？"会集中"才是你要找的信号。

### 2. 呈现候选

列出编号的深化机会清单。每个候选包含：

- **Files** — 涉及的文件/module
- **Problem** — 当前架构为何造成摩擦
- **Solution** — 用自然语言描述会改变什么
- **Benefits** — 用 locality 和 leverage 解释收益，以及测试会如何改善

如果项目有 `CONTEXT.md`，使用其词汇描述领域。使用 [LANGUAGE.md](LANGUAGE.md) 词汇描述架构。

**ADR 冲突**：如果某个候选与现有 ADR 矛盾，仅当摩擦大到值得重新审视该 ADR 时才提出。明确标注（如 _"与 ADR-0007 矛盾 — 但值得重新讨论，因为…"_）。不要列举 ADR 禁止的每个理论性重构。

此时**不要提出 interface 设计**。询问用户："你想探索哪个候选？"

### 3. 盘问循环

用户选定候选后，进入盘问对话。与用户一起走设计树 — 约束、依赖、深化 module 的形态、seam 背后放什么、哪些测试保留。

决策明确时，内联执行副作用：

- **深化的 module 命名了一个 `CONTEXT.md` 中没有的概念？** 将该术语添加到 `CONTEXT.md` — 见下方 [CONTEXT.md 格式](#contextmd-格式)。文件不存在则懒创建。
- **对话中 sharpen 了某个模糊术语？** 立即更新 `CONTEXT.md`。
- **用户用一个关键理由拒绝了候选？** 提出 ADR — 见下方 [ADR 格式](#adr-格式)。仅在理由对未来探索者有实际参考价值时才提出 — 跳过临时性理由（"现在不值得"）和不言自明的理由。
- **想为深化的 module 探索替代 interface 方案？** 见 [INTERFACE-DESIGN.md](INTERFACE-DESIGN.md)。

---

## CONTEXT.md 格式

### 结构

```md
# {上下文名称}

{一到两句话描述这个上下文是什么、为什么存在。}

## Language

**Order**:
{一到两句话描述该术语}
_Avoid_: Purchase, transaction

**Invoice**:
交付后发送给客户的付款请求。
_Avoid_: Bill, payment request
```

### 规则

- **要有主见。** 同一概念有多个词时，选最好的，其余列为避免使用的别名。
- **显式标记冲突。** 如果术语使用存在歧义，在"Flagged ambiguities"中指出并给出明确结论。
- **定义要精炼。** 最多一两句话。定义它是什么，不是它做什么。
- **展示关系。** 用粗体术语名，表达明显的基数关系。
- **只包含项目特有的术语。** 通用编程概念不属于此处。
- **自然分组。** 出现自然聚类时，用子标题分组。
- **写一段示例对话。** 开发者和领域专家之间的对话，展示术语如何自然交互。

### 单上下文 vs 多上下文仓库

- **单上下文（大多数仓库）：** 仓库根目录放一个 `CONTEXT.md`
- **多上下文：** 仓库根目录放 `CONTEXT-MAP.md`，列出各上下文的位置和关系

---

## ADR 格式

ADR 存放在 `docs/adr/`，使用顺序编号：`0001-slug.md`、`0002-slug.md` 等。

`docs/adr/` 目录懒创建 — 仅在第一个 ADR 需要时才创建。

### 模板

```md
# {决策简短标题}

{1-3 句话：背景是什么、决定了什么、为什么。}
```

就这样。ADR 可以只是一段话。价值在于记录做了什么决定和为什么 — 而不是填满章节。

### 可选章节

- **Status** 前置信息（`proposed | accepted | deprecated | superseded by ADR-NNNN`）
- **Considered Options** — 仅当被否决的替代方案值得记住时
- **Consequences** — 仅当非显而易见的下游影响需要指出时

### 编号

扫描 `docs/adr/` 找到最大编号，加一。

### 何时提出 ADR

以下三者必须同时成立：

1. **难以逆转** — 改变主意的代价是实质性的
2. **缺乏上下文会令人困惑** — 未来读者会纳闷"为什么这么干？"
3. **是真实权衡的结果** — 有真正的替代方案，你因特定理由选择了其中之一

### 符合条件的情况

- 架构形态（monorepo、event-sourcing 等）
- 上下文之间的集成模式
- 带有锁定效应的技术选型
- 边界和范围决策
- 对显而易见路径的刻意偏离
- 代码中不可见的约束
- 否决理由不明显时的被否决方案
