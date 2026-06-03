# Interface 设计

当用户想为选定的深化候选探索替代 interface 方案时，使用此并行子代理模式。基于 Ousterhout 的 "Design It Twice" 原则 — 你的第一个想法不太可能是最好的。

使用 [LANGUAGE.md](LANGUAGE.md) 中的术语 — **module**、**interface**、**seam**、**adapter**、**leverage**。

## 流程

### 1. 构建问题空间

在生成子代理之前，为用户写一段关于选定候选的问题空间说明：

- 新 interface 需要满足的约束
- 它依赖的依赖项及其所属类别（见 [DEEPENING.md](DEEPENING.md)）
- 一段粗略的示意性代码草图来具象化约束 — 不是提案，只是让约束变得具体

展示给用户，然后立即进入步骤 2。用户在阅读思考的同时，子代理并行工作。

### 2. 生成子代理

使用 Agent 工具并行生成 3+ 个子代理。每个必须产出**截然不同**的 interface 方案。

给每个子代理独立的技术简报（文件路径、耦合细节、[DEEPENING.md](DEEPENING.md) 中的依赖类别、seam 背后放什么）。给每个代理不同的设计约束：

- Agent 1："最小化 interface — 最多 1-3 个入口点。最大化每个入口点的 leverage。"
- Agent 2："最大化灵活性 — 支持多种用例和扩展。"
- Agent 3："优化最常见调用方 — 让默认用例变得极简。"
- Agent 4（如适用）："围绕 ports & adapters 设计，处理跨 seam 依赖。"

在简报中同时包含 [LANGUAGE.md](LANGUAGE.md) 术语和 CONTEXT.md 术语，确保每个子代理的命名与架构语言和项目领域语言一致。

每个子代理输出：

1. Interface（类型、方法、参数 — 加上不变量、顺序约束、错误模式）
2. 使用示例，展示调用方如何使用
3. 实现隐藏在 seam 背后的内容
4. 依赖策略和 adapter（见 [DEEPENING.md](DEEPENING.md)）
5. 权衡 — leverage 高的地方和薄的地方

### 3. 呈现与对比

依次展示各方案让用户逐一消化，然后用文字对比。从 **depth**（interface 处的 leverage）、**locality**（变更集中在哪里）和 **seam 位置** 三个维度对比。

对比后给出自己的推荐：哪个设计最强、为什么。如果不同方案的元素可以组合，提出混合方案。要有主见 — 用户要的是明确判断，不是菜单。
