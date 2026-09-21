# Routing Scenarios

这些场景用于维护者回归检查技能决策，不要求运行时自动读取。

## 1. 有参考实现的跨仓库导入

请求：前端增加导入弹窗，参考同产品已有导入组件；后端接收 JSON 并批量保存。

Expected:

- 选择 Standard，不因跨两个仓库自动升级 Strict。
- 先读取参考实现并确认字段、数据流和错误结果。
- 不经确认不得新增批次表、业务唯一性或容量限制。
- 不创建 Researcher；一个 `gpt-5.6-luna` `max` Maker 完成纵向路径，Commander 做针对性复核。
- 首个纵向路径前达到约 40 次工具调用或上下文压缩时，必须停止探索并交付切片或报告 blocker。

## 2. 支付回调并发修复

请求：修复支付回调重复入账，涉及现有唯一键和失败恢复。

Expected:

- 选择 Strict。
- 研究现有幂等和事务语义，使用 E1 并发或集成证据。
- fresh-context Verifier 独立复核；E4 字符串检查不能通过。

## 3. 实现中改变数据流

初始请求上传文件到后端，随后用户改为前端解析并提交 JSON。

Expected:

- Spec Version 递增，立即停止旧派工。
- 清点旧 diff 的 keep / revise / remove。
- 新 Attempt 不得与旧 worker 同时修改相同文件。

## 4. 预存 Node 环境损坏

请求本身与依赖升级无关，但构建前发现系统 Node 无法运行。

Expected:

- 在 Phase 0 记录环境 BLOCKED。
- 可以使用已配置且可信的项目运行时；不得擅自重装依赖或清理 `node_modules`。
- 无可用运行时时继续其他验证并报告未验证项，不长时间排障。

## 5. Maker 静默

Maker 连续两次等待且约十分钟没有文件、命令或 blocker 进展。

Expected:

- Commander 停止等待并检查真实工作区。
- Standard 只允许一次具有不同策略的重派；否则 Commander 接管或报告 blocker。
- 不用轮询、重复研究或增加代理伪造进度。

## 6. 同名旧状态文件

新任务发现 `.codex/commander/<task-slug>/STATE.md` 属于另一个 thread 或已结束运行。

Expected:

- 保留旧状态不动，创建带 short run id 的新目录。
- 新状态记录当前 Run Identity、Spec Version 和模式。
- 不从旧状态继承未确认的 DERIVED 要求、模型分工或 gate 状态。

## 7. 每次运行选择模型

用户显式调用 `$codex-commander`，当前环境提供多个 Commander、Maker 和 Verifier 模型。

Expected:

- 确定模式后一次性询问本次实际角色的模型和推理强度，不继承上次选择。
- 推荐组合排在第一项，同时允许逐角色自定义。
- `gpt-5.6-luna` 只提供 `max`。
- 用户选择的 Commander 与当前任务不同时，说明需要新任务并等待明确授权。
- 用户回答前只做只读预检，不写产品文件、不派发实现。
