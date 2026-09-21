# Codex Model Routing

只在任务确实需要委派时读取。模型选择服务于任务，不决定任务规模。

## 可用角色

| Role | Preferred model | Default effort | Responsibility |
|---|---|---|---|
| Commander | `gpt-6-astra` | `high` | 需求决策、范围控制、审查和总验收 |
| Researcher / Maker | `gpt-5.6-luna` | `high` | 有界研究或实现 |
| Verifier | `gpt-5.6-sol` | `high` | fresh-context 范围与行为验收 |

Strict 模式或确有复杂推理、并发正确性、跨系统恢复语义时，可把 Researcher / Maker 或 Verifier 提升到 `max`。不要把所有读取、构建或简单修改默认设为 `max`。

备选组合可由 `gpt-5.6-sol` `max` 担任 Commander、`gpt-5.6-luna` 担任 Researcher / Maker、`gpt-6-astra` `high` 担任 Verifier。不得静默替换用户指定的精确模型。

## 按模式路由

### Fast

- 不委派；当前对话完成实现和针对性自检。
- 不为了角色名称创建 Commander、Researcher 或 Verifier。

### Standard

- 默认一个 Maker；仅在存在会改变方案且可独立回答的未知项时增加一个 Researcher。
- 是否使用 fresh-context Verifier 由风险决定。普通 CRUD 或已有参考实现的功能，可以由 Commander 做针对性复核。
- 同一阶段不要让 Commander 和 Maker 同时重复实现。

### Strict

- 默认 Commander、一个有界 Researcher 或 Maker、一个 fresh-context Verifier。
- 只有确实互不依赖的问题才并行研究；代理数量不是完整性的指标。

## 启动检查

1. 核实当前环境暴露的模型、推理强度和委派能力。
2. 核实当前任务是否能访问目标工作区和用户未提交内容。
3. 核实 Verifier 能读取 Maker 的真实 diff、产物或明确快照。
4. 核实委派是否会创建用户可见任务、产生额外费用或需要权限。

当前对话无法读取自身精确模型 ID 时，记录事实并继续履行 Commander 职责，不为满足标签另建任务。

## 独立性

优先级如下：

1. 原生、隔离、可指定模型且能访问真实产物的子代理；
2. 用户明确授权的独立 Codex 任务；
3. 当前任务内复核，并标记 `non-independent`。

Verifier 只能看到文字总结时，验证状态是 `BLOCKED`。独立性不能弥补错误规格；Verifier 必须同时获得用户原始要求和最新需求账本。
