# Task Coordination

只在实际协调子代理或独立 Codex 任务时读取。

## 身份与工作区

派工前核实：

1. 目标项目、真实根目录、Git 状态和用户未提交内容；
2. 当前 `Spec Version`、任务模式和允许修改范围；
3. worker 是否共享真实工作区，Verifier 是否能看到精确 diff 或快照；
4. 用户可见独立任务、额外费用和权限是否已获授权。

当前对话默认是 Commander。不要为满足角色名称创建另一个 Commander，也不要让 worker 递归招募团队。

每次运行的模型选择必须来自当前用户确认。历史状态、推荐表或上一次运行不能代替确认。当前任务不能切换到用户选择的 Commander 模型时，先说明需要新任务并取得明确授权；未授权时不得创建。

## Assignment Contract

每项 assignment 使用稳定 Task ID、Attempt 和 Spec Version，例如 `T003/A1/S2`。派工内容包含：

- 角色、目标项目、工作目录和输入版本；
- 精确模型和推理强度；`gpt-5.6-luna` 必须是 `max`；
- 对应 Requirement IDs 与 Acceptance Criteria；
- 当前阶段目标、工程深度和明确非目标；
- 允许修改文件、只读参考、依赖和禁止操作；
- 必须运行的检查及最低证据等级；
- 进度预算、停止条件和 Commander 回报目标；
- 完成或真正阻塞时只发送一次终态报告；
- 保留其他人的修改，不改团队规则、不扩大范围、不招募新任务。

只接受 Spec Version、Task ID 和 Attempt 完全匹配的回报。旧版本回报只能用于清点可复用内容，不能触发后续阶段。

派工工具返回的实际模型或强度与用户选择不一致时，assignment 不成立。停止执行并向用户报告真实可用项，不得用相近模型顶替。

## 用户新输入

用户消息到达时，Commander 先判断是补充还是替换：

- 不改变接口、字段、数据流或范围的补充，可以发送给当前 worker。
- 改变上述内容时，立即递增 Spec Version 并中断旧 assignment。
- 中断后检查真实 diff，列出 `keep / revise / remove`，再创建新 Attempt。
- 禁止让旧 worker 继续写，同时让新 worker 在同一文件上重构。

Attempt 增加不代表可以机械重试。新 Attempt 必须说明失败根因和本轮策略变化；没有策略变化时停止。

## 进度与等待

- 优先使用原生 wait/callback，不短间隔重复读取整个任务。
- 每次等待前记录期望的 Task ID、Attempt、Spec Version、观察信号和停止条件。
- 连续两次等待，或约十分钟没有文件变化、命令结果、明确 blocker 或完成报告时，视为停滞。
- 停滞后只选一种动作：Commander 接管、缩小阶段、用有实质变化的策略重派，或报告 blocker。
- Standard 默认最多一次重派；Strict 默认最多两次。代理崩溃可以替换，但仍需先核对已写入内容。
- 不因 worker 静默推断完成，也不为等待制造无意义检查。
- Standard 在首个纵向路径前达到约 40 次工具调用或发生上下文压缩时，强制停止探索并检查真实 diff。没有跨必要边界的可运行链路时，只能立即完成它、缩小范围或报告 blocker。
- 同一 Spec Version 内不重复完整读取未变化文件；需要复查时读取精确行段或 diff。

## 回报与证据

生命周期为：

```text
planned -> dispatched -> reported -> accepted
                         |-> blocked
                         `-> rejected -> revised attempt
```

`reported` 不是 `accepted`。Commander 必须检查真实产物和证据。回报至少包括：

- 变更文件和关键行为；
- 实际命令、退出码和失败摘要；
- 未完成项、环境 blocker 和假设；
- 是否触及任何 `PROPOSED DERIVED` 项。

限制回报和命令输出体积。长日志写入文件并提供摘要；不要把完整构建日志、依赖树或大 diff 反复注入主上下文。

源码存在性、字符串匹配或文件存在只能作为 E4。worker 不得用 E4 声称事务、权限、并发、回滚或端到端流程通过。

## 独立任务特例

创建用户可见独立任务必须获得明确授权。创建可能先返回临时 ID；临时 ID 不能用于读取、发送或等待，不要因为初始化未完成而重复创建。

不同 worktree 或主机不表示文件自动共享。依赖任务只能在前置产物被 Commander 接受后开始；Verifier 无法访问真实产物时必须报告 `BLOCKED`。
