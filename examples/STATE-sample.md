# Customer Order Import

Status: implementing
Spec Version: 2
Mode: Standard
Current phase: 3
Current gate: vertical-slice
Current iteration: 1

## Goal

在订单管理页按客户导入订单，前端解析静态 XLSX 模板并向后端提交结构化 JSON。

## Behavior Contract

- Entry: 订单列表的导入按钮。
- Input: 已授权客户、项目内静态模板、结构化 JSON 行。
- Success: 全部行校验通过后批量写入并返回数量。
- Failure: 不写入数据，错误包含 Excel 行号、字段和原因。
- Non-goals: 不导入附件，不新增业务唯一性，不执行迁移。

## Requirement Ledger

| Req ID | Source | Requirement | Proof | Status |
|---|---|---|---|---|
| R-01 | USER | 模板作为前端静态文件直接下载 | 构建产物和浏览器网络检查 | CONFIRMED |
| R-02 | USER | 前端解析 XLSX，仅提交 JSON | 请求检查和服务端接口测试 | CONFIRMED |
| R-03 | USER | 全量校验通过后使用现有批量写入模式 | 事务集成测试 | CONFIRMED |
| R-04 | REPO | 客户必须受当前组织数据范围约束 | 越权集成测试 | CONFIRMED |
| R-05 | DERIVED | 建立永久导入批次表实现重试幂等 | 用户选择或架构评审 | PROPOSED |

## Architecture Checkpoint

- R-05 未确认，不进入实现或阻塞验收标准。
- 沿用现有接口权限模式；若需要新的授权传播规则，先确认。

## Environment Preflight

| Check | Result |
|---|---|
| PHP runtime and lint | PASS |
| Project Node runtime | PASS |
| Existing frontend build | PASS |
| Working tree reviewed | PASS, unrelated changes preserved |

## Delivery Agreement

- Engineering depth: Maintainable
- Team route: one Maker, Commander verification
- First useful slice target: about 20 minutes
- Maker retry budget: 1, only with a changed strategy
- Stall threshold: two unchanged waits or about 10 minutes

## Acceptance Criteria

| ID | Req IDs | Criterion | Evidence | Status |
|---|---|---|---|---|
| AC-01 | R-01, R-02 | 下载静态模板，前端解析后请求中不包含文件 | E1 browser/request inspection | PENDING |
| AC-02 | R-03 | 任一行失败时无写入；全部通过时批量写入 | E1 transaction integration test | PENDING |
| AC-03 | R-04 | 无权客户不能导入 | E1 authorization integration test | PENDING |
| AC-04 | R-01, R-02 | 前端可构建，后端语法正确 | E3 build/lint | PENDING |

## Iteration Log

| Task / Attempt / Spec | Result | Evidence |
|---|---|---|
| T001/A1/S1 | STOPPED | 用户把数据流从文件上传改为前端解析 JSON |
| T001/A2/S2 | IN_PROGRESS | 旧上传代码已移除，保留字段映射 |

## Final Result

Pending.
