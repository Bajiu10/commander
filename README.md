# codex-commander

适用于 Codex 的指挥官工作流：强模型负责计划与审查，`Luna max` 负责研究和实现，另一种强模型在 fresh context 中独立验收。

## Language comes first

工作语言以用户当前对话为准，而不是仓库、Skill 或 worker 回报的语言。中文用户会得到中文计划、派工说明、验证报告和总结，并尽量匹配简体或繁体。用户可为单个产物单独指定语言，例如中文沟通但英文写 README。

## 默认模型分工

| Role | Default | Alternative |
|---|---|---|
| Commander | `gpt-6-astra` + `high` | `gpt-5.6-sol` + `max` |
| Researcher / Maker | `gpt-5.6-luna` + `max` | 同左 |
| Verifier | `gpt-5.6-sol` + `max` | `gpt-6-astra` + `high` |

备选组合会交换 Commander 与 Verifier，确保 Maker 和 Verifier 不共享模型上下文。

## 与 fable-commander 的差异

- 使用 Codex 的模型 ID、推理强度和任务协作能力。
- 不依赖 Claude Code 的 `Agent`、`Workflow`、`AskUserQuestion`、`/model` 或 `/advisor`。
- 当前任务无法自动切换模型时会明确提示，不会伪装已切换。
- 没有 fresh-context 任务时允许降级，但会标注 `non-independent`。
- 不自动把单次经验回写到 Skill，避免未经验证的规则永久污染流程。
- 先做有限度需求澄清，只问会改变本次交付的决定。
- 将 Prototype、Maintainable、Production 工程档位与模型强度、团队数量分开选择。
- 独立任务使用 Task ID/Attempt，区分 reported 与 accepted，避免陈旧回报误触发后续工作。

## 安装

将整个目录放到：

```text
~/.codex/skills/codex-commander/
```

然后在 Codex 中使用：

```text
使用 $codex-commander 跑这个任务
```

也可以说“用指挥官模式做这个任务”。

## 运行产物

完整流程会在目标项目中维护：

```text
.codex/commander/<task-slug>/
|-- STATE.md
|-- RESEARCH.md       可选
`-- VERIFICATION.md   独立验证报告
```

每个 gate 最多 3 轮；同一问题连续 2 轮无改善即停止并请求用户裁决。

## 来源

本项目基于 Dennis Wei 的 [fable-commander](https://github.com/DennisWei9898/fable-commander) 思路重写，并参考 sanshao85 的 [codex-commander](https://github.com/sanshao85/codex-commander) 改进语言、需求澄清和跨任务协调规则。相关许可见 `LICENSE`、`NOTICE.md` 和 `licenses/`。
