# codex-commander

适用于 Codex 的自适应指挥官工作流。它先锁定用户真实需求和范围，再按风险选择 Fast、Standard 或 Strict，并使用与行为相匹配的证据验收。

## 核心原则

- 用户明确要求优先于 Commander 推导和流程形式。
- 每条要求标记为 `USER`、`REPO` 或 `DERIVED`。
- 未确认的派生项不能静默改变接口、字段、数据库、权限、业务唯一性或容量限制。
- 显式调用 `$codex-commander` 不代表自动启动最重的多代理流程。
- 源码字符串检查只能作为 E4 结构证据，不能证明事务、权限、并发或端到端行为。
- 用户改变数据流或范围时递增 Spec Version，并停止旧版本派工。

## 模式

| Mode | Typical use | Default route |
|---|---|---|
| Fast | 明确的小功能和集中修改 | Commander 直接实现与自检 |
| Standard | 跨模块或跨仓库，但边界清楚 | 至多一个 Researcher、一个 Maker，按风险复核 |
| Strict | 支付、核心权限、生产迁移、并发状态机或明确独立验收 | 有界研究、Maker、fresh-context Verifier |

模型、工程深度和团队规模分别选择。Strict 默认使用 Astra high 指挥、Luna max 执行、Sol max 验证；其他模式使用满足任务的最低有效推理强度。

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
