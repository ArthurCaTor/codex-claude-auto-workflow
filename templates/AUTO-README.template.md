# Mode B File-Heartbeat Coordination Protocol

STATUS: TEMPLATE - adapt before use.

## Purpose

Codex and Claude Code coordinate through files. The human owner starts Claude
Code manually and authorizes owner-gated actions.

## Truth / Projection

```text
messages.ndjson = coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

## State Machine

See `docs/state-machine.md` in the Mode B File Heartbeat project.

## Current Run

- Project slug: `<projectSlug>`
- Run id: `mode-b-<projectSlug>-<runSlug>`
- Active task: `<task-id>`
- Automation: `mode-b-<projectSlug>-<runSlug>-monitor`
- Max tasks: `1`
- Max report-only fix rounds: `2`

