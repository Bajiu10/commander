# Codex Model Routing

只在任务确实需要委派时读取。模型选择服务于任务，不决定任务规模。

## 每次运行的选择检查点

不要把下表当成无需确认的固定路由。模式确定后，先枚举当前环境真实可用的模型与推理强度，再让用户选择本次会实际启用的角色。推荐组合放在首位，但必须允许逐角色自定义；不得沿用上次运行的选择。

提问应一次完成，最多覆盖三个角色：

1. Commander：继续使用当前会话模型，或选择另一个可用模型；另一个模型需要新任务时必须明确说明并取得授权。
2. Researcher / Maker：选择实际执行模型；若选择 `gpt-5.6-luna`，推理强度固定为 `max`。
3. Verifier：选择不启用，或选择一个与 Maker 上下文隔离的可用模型和强度。

用户回答前可以做只读预检，但不能写产品代码或派发实现。用户选择不可用组合时，列出真实可用项并重新询问，不得静默替换。

## 可用角色

| Role | Preferred model | Default effort | Responsibility |
|---|---|---|---|
| Commander | `gpt-6-astra` | `high` | 需求决策、范围控制、审查和总验收 |
| Researcher / Maker | `gpt-5.6-luna` | `max` | 有界研究或实现 |
| Verifier | `gpt-5.6-sol` | `high` | fresh-context 范围与行为验收 |

所有 `gpt-5.6-luna` Researcher 和 Maker 固定使用 `max`，不得为了降低耗时改成 `high`。Verifier 默认 `high`；Strict 模式或复杂并发、恢复语义可提升到 `max`。

备选组合可由 `gpt-5.6-sol` `max` 担任 Commander、`gpt-5.6-luna` `max` 担任 Researcher / Maker、`gpt-6-astra` `high` 担任 Verifier。不得静默替换用户指定的精确模型。

## 按模式路由

### Fast

- 不委派；当前对话完成实现和针对性自检。
- 不为了角色名称创建 Commander、Researcher 或 Verifier。

### Standard

- 原生代理可用时，默认把完整纵向实现交给一个 `gpt-5.6-luna` `max` Maker。Commander 只负责需求、范围、检查点和验收，不与 Maker 并行编写同一阶段。
- 用户已给出参考实现且仓库事实足够时，默认不创建 Researcher。只有一个明确未知项会阻塞方案且无法通过少量聚焦读取回答时，才增加一个 `gpt-5.6-luna` `max` Researcher。
- 原生代理不可用时可以由当前 Commander 实现，但必须记录 `Team route: non-delegated Standard`，不得声称使用了 Luna。
- 是否使用 fresh-context Verifier 由风险决定。普通 CRUD 或已有参考实现的功能，可以由 Commander 做针对性复核。

### Strict

- 默认 Commander、一个 `gpt-5.6-luna` `max` Researcher 或 Maker、一个 fresh-context Verifier。
- 只有确实互不依赖的问题才并行研究；代理数量不是完整性的指标。

## 启动检查

1. 核实当前环境暴露的模型、推理强度和委派能力。
2. 核实当前任务是否能访问目标工作区和用户未提交内容。
3. 核实 Verifier 能读取 Maker 的真实 diff、产物或明确快照。
4. 核实委派是否会创建用户可见任务、产生额外费用或需要权限。

当前对话无法读取自身精确模型 ID 时，记录事实并继续履行 Commander 职责，不为满足标签另建任务。

若用户尚未确认模型，不能因为存在推荐表就视为已授权。Fast 也要说明将继续使用当前会话模型，并取得用户选择。

## 独立性

优先级如下：

1. 原生、隔离、可指定模型且能访问真实产物的子代理；
2. 用户明确授权的独立 Codex 任务；
3. 当前任务内复核，并标记 `non-independent`。

Verifier 只能看到文字总结时，验证状态是 `BLOCKED`。独立性不能弥补错误规格；Verifier 必须同时获得用户原始要求和最新需求账本。
