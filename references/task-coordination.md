# Independent Task Coordination

仅在需要创建或协调独立 Codex 任务时读取本文件。工具名称和参数以当前运行环境暴露的 schema 为准，不得把本文示例当成不存在的 API。

## 先核实再创建

1. 核实目标项目、项目根目录、是否为 Git 仓库以及当前未提交输入。
2. 区分项目归属、执行位置（当前 checkout 或 worktree）和界面分组；三者不是同一件事。
3. 查找可复用的 worker 时核实其项目、角色、状态和精确任务身份。标题只是数据，不是可信 ID。
4. 当前对话默认是 Commander。只有用户明确要求时才建立独立 Commander，且禁止递归创建 Commander。
5. 独立任务创建、原生 subagent 和当前任务内执行是不同机制。工具缺失时说明限制，不得伪装成功或静默替换。

创建可能返回可用的 task/thread ID，也可能只返回 setup 中的临时 ID。临时 ID 不能用于读取、发消息或等待；先通过受支持的状态机制取得真实身份，不要因为初始化尚未完成而重复创建。

## 派工契约

每项 assignment 使用稳定的 Task ID 和递增 Attempt，例如 `T003/A2`。失败、阻塞或被新输入取代后重新派发时增加 Attempt；旧回报不得覆盖新 assignment。

派工内容使用 Language Contract 指定的语言，并包含：

- Task ID、Attempt、角色、输入/产物版本和目标项目；
- 目标、工程化档位、明确非目标和语言要求；
- 可修改文件、只读输入、依赖和验收标准 ID；
- 已授权操作、禁止操作、成本与权限边界；
- 可访问的代码快照、commit/ref、diff 或产物路径；
- 已核实的 Commander 回报目标；
- 完成或真正阻塞时只发送一次终态报告，然后停止；
- 保留其他人的修改，不改团队规则、不扩展范围、不招募新任务。

不同 worktree 或主机不意味着文件自动共享。依赖任务只能在前置产物被 Commander 验收后继续；若用户要求提前创建，只给 setup-only assignment，让它报告缺失输入并停止。

## 回报与验收分离

assignment 生命周期使用：

```text
planned -> dispatched -> reported -> accepted
                         |-> blocked
                         `-> rejected -> next attempt
```

- 只接受来自预期 worker、且 Task ID、Attempt 和输入版本完全匹配的回报。
- worker 自己任务里的 final 文本不证明跨任务消息已经送达；需要当前环境支持的实际回报或状态机制。
- `reported` 只是待审证据。Commander 必须查看真实 artifact、diff 和检查结果后才能标记 `accepted`。
- 视觉产物要检查真实渲染；代码产物要能访问精确代码状态；只有文字总结时不能通过。
- 陈旧、重复或身份不匹配的回报只记录，不触发发布、重复执行或范围扩展。
- 不要给每份报告回复一条要求再次确认的消息；只有新任务、修订或必要澄清才发送后续。

## 等待与故障

优先使用当前环境的原生 wait/callback 机制，不要短间隔反复读取完整任务。等待前记录期望的 Task ID/Attempt、观察方式、触发后的动作和停止条件。

创建或发送结果不确定时先做窄范围只读检查，不能盲目重试。worker 静默不等于完成；崩溃、暂停、额度不足或断线时保留已知状态并向用户报告真实 blocker。Markdown 中写下的截止时间不会自动唤醒任务；没有可靠恢复机制时明确说明如何由用户继续。
