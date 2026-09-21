# Routing Scenarios

这些场景用于维护者回归检查技能决策，不要求运行时自动读取。

## 1. 有参考实现的跨仓库导入

请求：前端增加导入弹窗，参考同产品已有导入组件；后端接收 JSON 并批量保存。

Expected:

- 选择 Standard，不因跨两个仓库自动升级 Strict。
- 先读取参考实现并确认字段、数据流和错误结果。
- 不经确认不得新增批次表、业务唯一性或容量限制。
- 一个 Maker 完成纵向路径，Commander 做针对性复核。

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
