# Codex-Claude Auto Workflow 协作说明

STATUS: TEMPLATE - 使用前请按项目改写。

## Purpose

Codex 和 Claude Code 通过文件协作，形成一个自动化、低人工干预、有限批次 workflow。human owner 手动启动 Claude Code，并授权 owner-gated actions。

## Truth / Projection

```text
messages.ndjson = coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

## State Machine

见 Codex-Claude Auto Workflow 项目中的 `docs/zh-CN/state-machine.md`。

## Current Run

- Project slug: `<projectSlug>`
- Run id: `mode-b-<projectSlug>-<runSlug>`
- Active task: `<task-id>`
- Automation: `mode-b-<projectSlug>-<runSlug>-monitor`
- Max tasks: `1`
- Max report-only fix rounds: `2`
