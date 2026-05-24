# Codex-Claude Auto Workflow Coordination

STATUS: TEMPLATE - adapt before use.

## Purpose

Codex and Claude Code coordinate through files as an automated, low-touch,
finite workflow. The human owner starts Claude Code manually and authorizes
owner-gated actions.

## Truth / Projection

```text
BELL.json       = shared turn signal
messages.ndjson = append-only audit truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

Read `BELL.json` first, then verify it against `messages.ndjson` and
`state.json`. If projections disagree with the append-only log, resync before
acting.

## Shared Bell

```text
holder=claude -> Claude Code works
holder=codex  -> Codex reviews or prepares the next bounded task
holder=arthur -> the owner decides
```

Do not create `CLAUDE_SIGNAL.json`, `CODEX_SIGNAL.json`, or other parallel
signal files. One shared bell is the turn signal.

## State Machine

See `docs/state-machine.md` and `docs/bell.md` in the Codex-Claude Auto
Workflow project.

## Current Run

- Project slug: `<projectSlug>`
- Run id: `mode-b-<projectSlug>-<runSlug>`
- Active task: `<task-id>`
- Bell holder: `claude`
- Automation: `mode-b-<projectSlug>-<runSlug>-monitor`
- Max tasks: `1`
- Max report-only fix rounds: `2`
