# Deepening

如何安全地深化一组浅 module，取决于其依赖关系。使用 [LANGUAGE.md](LANGUAGE.md) 中的术语 — **module**、**interface**、**seam**、**adapter**。

## 依赖分类

评估深化候选时，对其依赖进行分类。类别决定了深化后的 module 如何通过其 seam 进行测试。

### 1. 进程内（In-process）

纯计算、内存状态、无 I/O。总是可以深化 — 合并 module 并直接通过新 interface 测试。不需要 adapter。

### 2. 本地可替代（Local-substitutable）

有本地测试替身的依赖（如 PGLite 替代 Postgres、内存文件系统）。如果替身存在则可深化。深化后的 module 使用替身在测试套件中运行。Seam 是内部的；module 的外部 interface 不需要 port。

### 3. 远程但自有（Remote but owned）— Ports & Adapters

跨网络边界的自有服务（微服务、内部 API）。在 seam 处定义一个 **port**（interface）。深度 module 拥有逻辑；传输层作为 **adapter** 注入。测试使用内存 adapter。生产环境使用 HTTP/gRPC/队列 adapter。

推荐话术：*"在 seam 处定义 port，生产环境实现 HTTP adapter、测试环境实现内存 adapter，这样逻辑集中在一个深度 module 中，即使跨网络部署。"*

### 4. 真正外部（True external）— Mock

你无法控制的第三方服务（Stripe、Twilio 等）。深化后的 module 将外部依赖作为注入的 port；测试提供 mock adapter。

## Seam 纪律

- **一个 adapter 意味着假设的 seam。两个 adapter 意味着真正的 seam。** 不要引入 port，除非至少有两个 adapter 的正当理由（通常是生产 + 测试）。单 adapter 的 seam 只是间接层。
- **内部 seam vs 外部 seam。** 一个深度 module 可以有内部 seam（实现内部私有，供自身测试使用）和外部 seam（在其 interface 处）。不要仅因为测试使用了内部 seam 就通过 interface 暴露它们。

## 测试策略：替换，不要叠加

- 浅 module 上的旧单元测试，在深化后 module 的 interface 测试存在后变为废代码 — 删除它们。
- 在深化后 module 的 interface 处编写新测试。**Interface 就是测试面**。
- 测试断言通过 interface 可观察的结果，而非内部状态。
- 测试应能在内部重构后存活 — 它们描述行为，不描述实现。如果实现变了测试也得改，说明测试穿透了 interface。
