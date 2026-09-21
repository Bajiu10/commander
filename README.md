# codex-commander

适用于 Codex 的自适应指挥官工作流。它先锁定用户真实需求和范围，再按风险选择 Fast、Standard 或 Strict，并使用与行为相匹配的证据验收。

## 核心原则

- 用户明确要求优先于 Commander 推导和流程形式。
- 每条要求标记为 `USER`、`REPO` 或 `DERIVED`。
- 未确认的派生项不能静默改变接口、字段、数据库、权限、业务唯一性或容量限制。
- 显式调用 `$codex-commander` 不代表自动启动最重的多代理流程。
- 源码字符串检查只能作为 E4 结构证据，不能证明事务、权限、并发或端到端行为。
- 用户改变数据流或范围时递增 Spec Version，并停止旧版本派工。
- 代码中的非直观业务规则、权限/事务/并发约束和 workaround 必须有简洁的原因注释；自解释代码不添加复述式注释。

## 模式

| Mode | Typical use | Default route |
|---|---|---|
| Fast | 明确的小功能和集中修改 | Commander 直接实现与自检 |
| Standard | 跨模块或跨仓库，但边界清楚 | 一个 `gpt-5.6-luna max` Maker；具体未知项阻塞时才加 Researcher |
| Strict | 支付、核心权限、生产迁移、并发状态机或明确独立验收 | 有界研究、Maker、fresh-context Verifier |

模型、工程深度和团队规模分别选择。所有 Luna Researcher / Maker 固定使用 `gpt-5.6-luna max`；Commander 首选 Astra high，Verifier 默认 Sol high，并可在 Strict 中提升到 max。

每次运行都会在模式确定后询问用户本次 Commander、Researcher / Maker 和 Verifier 的模型选择，不沿用历史配置。推荐组合只作为首选项，用户可以逐角色自定义；当前任务无法切换 Commander 模型时，只有明确授权后才会创建新任务。

Standard 在首个纵向路径前有约 40 次工具调用和一次上下文压缩的检查点预算；到达预算仍没有跨必要边界的可运行结果时，必须停止探索、缩小范围或报告 blocker。同名旧状态文件不得复用，新的运行必须记录独立 Run Identity。

## 安装

将整个目录放到：

```text
~/.codex/skills/codex-commander/
```

然后使用：

```text
使用 $codex-commander 完成这个任务
```

## 运行产物

Fast 不创建状态文件。Standard / Strict 可以维护紧凑状态：

```text
.codex/commander/<task-slug>/
|-- STATE.md
|-- RESEARCH.md       可选
`-- VERIFICATION.md   可选
```

状态文件至少记录 Spec Version、Requirement Ledger、架构确认点、环境预检、进度预算、证据等级和最终结果。参考 [STATE sample](examples/STATE-sample.md)、[Verifier report](examples/verifier-report-sample.md) 和 [routing scenarios](examples/routing-scenarios.md)。

## 来源

本项目基于 Dennis Wei 的 [fable-commander](https://github.com/DennisWei9898/fable-commander) 思路重写，并参考 sanshao85 的 [codex-commander](https://github.com/sanshao85/codex-commander)。相关许可见 `LICENSE`、`NOTICE.md` 和 `licenses/`。
