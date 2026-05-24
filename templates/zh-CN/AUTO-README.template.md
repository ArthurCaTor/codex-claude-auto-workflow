# Codex-Claude Auto Workflow 协作说明

STATUS: TEMPLATE - 使用前请按项目改写。

## Purpose

Codex 和 Claude Code 通过文件协作，形成一个自动化、低人工干预、有限批次 workflow。human owner 手动启动 Claude Code，并授权 owner-gated actions。

## Truth / Projection

```text
BELL.json       = shared turn signal
messages.ndjson = append-only audit truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

先读 `BELL.json`，再用 `messages.ndjson` 和 `state.json` 校验。如果
projection 与 append-only log 冲突，先 resync，再行动。

## Shared Bell

```text
holder=claude -> Claude Code 干活
holder=codex  -> Codex review 或准备下一个有限任务
holder=arthur -> owner 决策
```

不要创建 `CLAUDE_SIGNAL.json`、`CODEX_SIGNAL.json` 或其他并行 signal
文件。一个共享铃铛就是 turn signal。

## State Machine

见 Codex-Claude Auto Workflow 项目中的 `docs/zh-CN/state-machine.md` 和
`docs/zh-CN/bell.md`。

## Current Run

- Project slug: `<projectSlug>`
- Run id: `mode-b-<projectSlug>-<runSlug>`
- Active task: `<task-id>`
- Bell holder: `claude`
- Automation: `mode-b-<projectSlug>-<runSlug>-monitor`
- Max tasks: `1`
- Max report-only fix rounds: `2`
