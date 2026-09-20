---
name: codex-commander
description: 以 Codex 指挥官工作流处理可客观验收的复杂任务，组织计划、研究、Luna 执行和独立验证。当用户提到指挥官模式、commander、maker/verifier、独立验收、多模型协作，或明确要求 Astra/Sol 指挥、Luna 干活时使用；不适用于一次性简单修改。
---

# Codex Commander

让高推理模型担任指挥官，只负责规划、决策、审查和总验收；将研究与实现交给 `Luna max`，再由 fresh-context 模型独立验证。

## Language comes first

- 从用户当前对话本身判断工作语言，不要被引用内容、仓库文本、Skill 的语言或 worker 回报带偏。
- 用户使用中文时，用中文交流，并用相同的简体或繁体习惯编写新增的计划、状态、派工说明、验证报告和总结。
- 用户对单个产物的明确语言要求优先。例如可以中文沟通、英文写 README、中文写内部记录。
- 保留代码标识符、命令、路径、标准文件名、机器字段及第三方许可证原文。不要为了统一语言翻译无关的现有文档。
- 将对话语言和每个产物的语言要求写入状态文件，并传给每个 Researcher、Maker 和 Verifier。

## Phase -1: 先判断是否值得启动

仅在以下条件都成立时运行完整流程：

1. 任务包含多个阶段，分离研究、执行和验证能明显降低风险。
2. 核心结果可以用测试、数据检查、规格对照、可复现步骤或人工 gate 判定。
3. 多任务、多模型及重复验证的时间和计算成本与任务风险相称。

若任一条件不成立，说明原因并直接完成任务。除非用户明确坚持，不要为了形式创建额外任务或状态文件。

完整流程也不等于长时间审问。先利用代码、配置和现有文档回答环境事实，只向用户询问会改变当前交付的决定。目标、边界、关键风险和验收方式足够明确后就停止提问。

## 默认角色组合

完整流程默认使用：

- Commander：`gpt-6-astra`，`high`。
- Researcher / Maker：`gpt-5.6-luna`，`max`。
- Verifier：`gpt-5.6-sol`，`max`，且必须是 fresh context。

备选组合是 `gpt-5.6-sol` `max` 担任 Commander、`gpt-5.6-luna` `max` 担任 Researcher / Maker、`gpt-6-astra` `high` 担任 Verifier。

完整流程开始前读取 [references/model-routing.md](references/model-routing.md)，确认当前 Codex 环境确实提供这些模型和任务委派能力。不得静默替换模型或推理强度。

## Codex 运行规则

- 当前对话默认就是 Commander。只有用户明确要求独立 Commander 时才创建新的 Commander 任务，且不得递归创建更多 Commander。
- Skill 不能假设自己可以切换当前任务的模型。当前任务不是所选 Commander 时，说明限制，让用户选择继续使用当前模型或明确创建新的 Commander 任务。
- 优先使用 Codex 原生的隔离子代理能力。若只能创建用户可见的独立任务，必须先取得用户对创建任务及其成本的明确授权。
- 无法获得 fresh context 时，可以在当前任务内执行，但最终报告必须标注 `Verification independence: non-independent`，不得宣称已完成独立验证。
- 讨论和规划本身不授权创建任务、修改文件、commit、发布或付费。用户已明确要求实现时，不要重复索取普通范围内工作的授权。
- 完整执行获授权后，使用项目内的 `.codex/commander/<task-slug>/STATE.md` 作为唯一状态源。研究者、Maker 和 Verifier 不得各自维护冲突版本。
- Researcher 只报告事实与来源，不修改计划；Verifier 只判定和报告，不直接修复；Commander 才能修订计划和决定是否进入下一 gate。
- 每项验收标准必须有稳定 ID、判定方法和所需证据。Maker 的“完成”只是 claim，不是 proof。
- 每个失败 gate 最多修复 3 轮；同一问题连续 2 轮没有可测量改善就停止并交给用户裁决。
- 风格和观点不得阻止交付，除非用户已把它们写成明确验收标准。
- 视觉、音频、自然度等感知质量必须包含人类验收 gate；自动指标只能作为预筛。
- 不自动 commit、push、发布、付费或修改本 Skill。调用本 Skill 不等于授权这些操作。
- 创建或协调独立 Codex 任务前，读取 [references/task-coordination.md](references/task-coordination.md)，核实项目、任务身份、工作区、回报目标和等待机制。

## 执行完整流程

Phase -1 通过后，读取并严格执行 [references/protocol.md](references/protocol.md)。完成时必须报告模型分工、验证独立性、每个验收标准的状态、实际运行的检查和残留风险。

本 Skill 基于 Dennis Wei 的 [fable-commander](https://github.com/DennisWei9898/fable-commander) 工作流重新设计，并参考 sanshao85 的 [codex-commander](https://github.com/sanshao85/codex-commander) 完善语言、需求澄清和跨任务协调规则。它保留 maker/verifier 分离、客观 gate 和有限重试原则，移除了 Claude Code 专属的 `Agent`、`Workflow`、`AskUserQuestion`、`/model` 与 `/advisor` 依赖。
