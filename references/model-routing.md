# Codex Model Routing

## 固定角色映射

使用当前 Codex Desktop 暴露的精确模型 ID 和推理强度：

| Display role | Model ID | Reasoning | Responsibility |
|---|---|---|---|
| Astra high | `gpt-6-astra` | `high` | 默认 Commander：计划、关键决策、研究复核、总验收 |
| Sol max | `gpt-5.6-sol` | `max` | 默认 Verifier；也可作为备选 Commander |
| Luna max | `gpt-5.6-luna` | `max` | Researcher 和 Maker：查资料、实现、修复 |

默认组合：

```text
Commander  Astra high
    |
    +-- Researcher / Maker  Luna max
    `-- Verifier            Sol max (fresh context)
```

备选组合：

```text
Commander  Sol max
    |
    +-- Researcher / Maker  Luna max
    `-- Verifier            Astra high (fresh context)
```

Verifier 使用另一种 Commander 级模型，是为了同时获得上下文独立性和模型差异性。不要让 Maker 与 Verifier 共享同一段任务对话。

## 启动检查

1. 检查当前环境列出的模型、推理强度和委派工具。
2. 检查当前任务是否已经运行在用户选定的 Commander 配置上。
3. 检查 fresh-context Verifier 是否能读取 Maker 的真实产物、diff 或提交，而不仅是 Maker 的文字总结。
4. 检查独立任务是否会产生额外费用、用户可见任务或需要新的权限。

当前对话默认担任 Commander，不要仅为满足角色名称自动创建另一个 Commander，也不要让新 Commander 递归招募 Commander。

如果当前 Commander 模型不匹配，不要声称已经切换。说明当前限制并让用户选择：

- 在当前任务继续，把实际模型记录到状态文件；或
- 明确授权创建一个使用目标模型的新 Commander 任务，并把计划与状态交接过去。

如果精确模型不可用，不得静默选“相近”模型。列出实际可用项，让用户确认替代映射；确认后把替代方案写入 `STATE.md` 的 Decision Log。

## 委派方式优先级

1. 原生、隔离且可指定模型的子代理。
2. 用户明确授权的独立 Codex 任务。
3. 当前任务内执行，验证标记为 `non-independent`。

创建用户可见的独立任务前，一次性说明将创建哪些角色、各自模型和是否需要工作树或 checkpoint commit。用户批准后再创建。

模型、工程化档位和团队规模是三个独立决策。高推理强度不自动意味着正式交付，也不自动要求更多 worker；生产级的小改动也可能只需要一个 Maker 和一次独立复核。

对代码任务，fresh-context Verifier 必须能访问 Maker 的精确代码状态：共享只读工作区、明确 commit/ref，或其他可复现快照均可。若只能看到文字报告，验证状态是 `BLOCKED`，不是 `PASS`。

## 轻量模式

任务较长但不值得完整多任务流程时，由当前 Commander 直接执行，并在交付前做一次证据驱动的自检。轻量模式不是独立验证，必须明确标注这一点。Codex 版不模拟或声称存在 Claude Code 的 `/advisor` 功能。
