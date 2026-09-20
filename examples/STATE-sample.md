# Monthly Revenue Pipeline

Status: verifying
Current phase: 5
Current gate: AC-02
Current iteration: 2

## Goal

抓取近 24 个月的月营收数据，生成 parquet 与数据质量报告。

## Language Contract

- Conversation language: 简体中文
- Default document language: 简体中文
- Per-artifact overrides: parquet schema 字段名保持英文

## Delivery Agreement

- Engineering depth: Maintainable / 实用维护
- Team route: delegated
- Explicit non-goals: 不部署服务，不扩展到实时数据

## Model Assignment

- Commander: `gpt-6-astra`, reasoning `high`
- Researcher / Maker: `gpt-5.6-luna`, reasoning `max`
- Verifier: `gpt-5.6-sol`, reasoning `max`
- Verification independence: independent fresh context

## Acceptance Criteria

| ID | Criterion | Verification method | Required evidence | Status |
|---|---|---|---|---|
| AC-01 | 官方来源抽样数字一致 | 随机抽 5 家公司 x 3 个月对照 | 请求快照与差异表 | PASS |
| AC-02 | 输出 schema 和行数正确 | 运行 schema 与行数校验 | 命令输出、除牌清单 | PASS |
| AC-03 | 主键无重复 | 对公司代码和月份分组 | 最大分组计数为 1 | PASS |

## Phases and Gates

| Phase | Deliverable | Gate | Status |
|---|---|---|---|
| P1 | 数据源接通 | AC-01 | PASS, round 1 |
| P2 | 清洗与落地 | AC-02, AC-03 | PASS, round 2 |
| P3 | QA 报告 | 全部 AC | PENDING |

## Decision Log

- 使用默认模型组合：Astra high -> Luna max -> Sol max。
- 官方接口只能回溯 5 年；本任务仅需 24 个月，无范围冲突。

## Iteration Log

| Task / Attempt | Gate | Result | Evidence |
|---|---|---|---|
| T002/A1 | AC-02 | FAIL | 缺少 3 家已除牌公司的 72 行记录 |
| T002/A2 | AC-02 | PASS | 接入除牌清单后行数与预期一致 |

## Final Result

Pending Phase 6 commander audit.
