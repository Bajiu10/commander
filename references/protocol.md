# Codex Commander Protocol

## Phase 0: 有限澄清与 Plan v0

先读取项目中相关的 `AGENTS.md`、已有计划、配置和当前任务记录，确认真实项目根目录与已有工作。只检查与本任务有关的内容，不搜索无关对话、凭据或私人记录。

进行有限度需求澄清：环境事实优先自己查；只询问会改变本次交付的决定。优先澄清当前用户、核心行为、非目标、运行条件、失败后果、时间/成本约束和验收方式。用户可以随时结束访谈；此时记录未解决风险，不要继续穷举未来功能。

为当前交付选择最低但足够的工程化档位，且不要把档位与代理数量或模型推理强度绑定：

| Level | 中文标签 | 本次交付含义 |
|---|---|---|
| Prototype | 轻量验证 | 跑通一个完整有用路径，保留必要的数据与安全保护，明确限制 |
| Maintainable | 实用维护 | 适合持续使用和修改，有适量回归测试、错误处理和可复现说明 |
| Production | 正式交付 | 满足真实用户所需的可靠性、安全、数据完整性、部署恢复和可观测性 |

不要因为选择较高档位就扩展功能，也不要因为用户要求简单就删除当前风险所需的保护。任务很小但生产环境重要时，仍可由单个 Maker 完成并做针对性复核。

讨论或仅制定方案时不要创建状态文件。用户已授权执行且 Phase -1 通过后，在项目中创建 `.codex/commander/<task-slug>/STATE.md`。`task-slug` 使用简短的小写连字符名称，并避免覆盖已有目录。

状态文件至少包含：

```markdown
# <Task name>

Status: planning
Current phase: 0
Current gate: none
Current iteration: 0

## Goal
## In Scope
## Out of Scope
## Assumptions
## Known Unknowns

## Language Contract
- Conversation language:
- Default document language:
- Per-artifact overrides:

## Delivery Agreement
- Engineering depth: Prototype / Maintainable / Production
- Team route: solo / delegated
- Explicit non-goals:

## Model Assignment
- Commander:
- Researcher / Maker:
- Verifier:
- Verification independence: pending

## Acceptance Criteria
| ID | Criterion | Verification method | Required evidence | Status |
|---|---|---|---|---|
| AC-01 | ... | ... | ... | PENDING |

## Phases and Gates
| Phase | Deliverable | Gate | Status |
|---|---|---|---|

## Decision Log
## Iteration Log
## Final Result
```

验收标准必须描述可观察结果。优先使用已有测试、构建、类型检查、静态分析、数据校验和可复现命令。将不明确但可安全推断的内容写入 `Assumptions`；只有会显著改变范围、成本、数据安全或外部行为的问题才暂停询问用户。

如果新增用户可见文档或报告，把已约定的语言列为对应产物的验收条件。语言不匹配是交付要求失败，不是单纯风格意见；代码标识符、第三方许可证和用户明确排除的现有文档不受此 gate 影响。

## Phase 1: 确认角色与授权

按 [model-routing.md](model-routing.md) 检查模型和委派能力。需要独立 Codex 任务时，用一次简洁确认覆盖以下内容：

- 选择默认组合还是备选组合；
- 是否允许为 Researcher / Maker 和 Verifier 创建独立任务；
- 代码状态使用共享工作区、工作树还是明确 commit/ref 交接；
- 是否允许为交接创建 checkpoint commit。

若用户不授权独立任务，继续单任务模式并记录 `Verification independence: non-independent`。

组织方式只在 `solo`、复用已核实 worker、或用户明确授权的最小独立任务团队中选择。团队是手段，不是默认交付物；只有可独立验收的产物、上下文隔离或独立复核能抵消协调成本时才组队。

## Phase 2: Luna max 研究

把 `Known Unknowns` 中会影响方案的条目交给一个或多个 `gpt-5.6-luna` `max` 研究任务。可以安全并行的只读问题并行处理。

每个研究任务都必须包含：

- 一个边界清晰的问题；
- 可使用的数据源、代码路径与禁止触碰的范围；
- 只返回事实、证据位置、置信度和未解决项；
- 禁止修改计划与实现；
- 查不到时明确报告未知，禁止猜测。
- 对话语言、默认文档语言和该产物的语言覆盖规则。

Commander 汇总研究，不让研究任务直接编辑 `STATE.md`。内容过长时写入同目录的 `RESEARCH.md` 并由状态文件链接。Commander 至少独立复核一项关键外部事实和一项关键代码假设（若适用）。

## Phase 3: Commander 定稿 Plan v1

Commander 根据证据更新验收标准、阶段和风险：

1. 检查每个 Known Unknown 是否有答案；未回答的移入风险。
2. 将每个 gate 改写成可独立重跑的 PASS / FAIL 判定。
3. 明确每阶段允许修改的文件、依赖和外部系统。
4. 将关键取舍写入 Decision Log。

可靠证据与用户目标冲突、必须扩大范围、需要付费或不可逆外部操作时，暂停并询问用户。其他情况按现有项目模式中最保守的方案继续。

## Phase 4: Luna max Maker

一次只给 `gpt-5.6-luna` `max` Maker 一个阶段。Maker prompt 必须包含：

- 当前阶段目标和允许修改的范围；
- 相关验收标准 ID；
- 必须运行的检查；
- 禁止操作和授权边界；
- 返回变更摘要、命令结果、diff 或产物位置；
- 遇到范围冲突、缺失权限或验收标准矛盾时立即停止。
- 对话与产物语言要求，以及不得翻译的标识符和现有内容。

Maker 不得自行放宽验收标准，也不得仅凭自己的测试结果把 gate 标记为 PASS。阶段完成后，Commander 将实际变更和证据写入 Iteration Log。

若本 Skill 在一个已经被派工的 worker 任务中触发，worker 只执行当前有边界的 assignment 并回报 Commander；不得重新访谈用户、重写全局计划或招募团队，除非派工明确授权。

## Phase 5: Fresh-context Verifier

使用角色组合中指定的 Verifier 创建全新上下文。只提供：

- 用户原始目标；
- `STATE.md` 与相关 `RESEARCH.md`；
- Maker 的真实 diff、commit/ref 或产物路径；
- 可独立执行的验收命令。

不要提供 Maker 的自我评价、推理过程或期望结论。

Verifier 必须：

1. 独立重跑适用检查，不复述 Maker 的输出。
2. 对每个验收标准返回 `PASS`、`FAIL` 或 `BLOCKED`。
3. 每项判定附命令、输出摘要、文件位置或可复现步骤。
4. 将风格和观点建议单列为 non-blocking suggestions。
5. 只报告，不修改实现。
6. 对新增用户可见产物核对 Language Contract，但不翻译或重写既有无关内容。

Verifier 报告写入同目录的 `VERIFICATION.md`，或由 Commander 原样记录后写入。无法看到真实产物时必须判 `BLOCKED`。

失败后的控制规则：

- 只把失败 ID、证据和允许修复范围交回 Luna max Maker。
- 每个 gate 最多 3 轮。
- 同一失败连续 2 轮没有可测量改善，立即停止空转。
- 达到上限仍失败时保持 `FAIL`，不得用“最高分”“大部分通过”或主观判断放行。
- 需要凭据、外部系统或用户感知判断时使用 `BLOCKED`，并给出最小下一步。

如果没有 fresh context，Commander 可自行重跑 gate，但必须记录 `Verification independence: non-independent`。

## Phase 6: Commander 总验收

Commander 抽查关键证据，确认状态文件与真实工作区一致，然后更新 Final Result：

- 已完成范围；
- 每项验收标准的最终状态；
- 实际运行的检查及结果；
- 残留风险与阻塞；
- 使用的模型分工；
- 验证是否真正独立；
- 用户必须亲自查看的关键 diff、报告或感知样本。
- Language Contract 是否在新增产物与回报中得到遵守。

只有全部阻塞性标准通过时才能宣告完成。感知质量任务必须等待用户完成对应人工 gate；自动指标通过不能代替用户验收。

记录本次流程的教训，但不要自行修改 Skill。只有用户明确要求改进技能时，才把经过验证且可泛化的经验回写。
