# Protocol

Mode B is a file-heartbeat protocol.

## Coordination Files

```text
docs/operations/agent-coordination/auto/
  README.md
  messages.ndjson
  state.json
  BOARD.md
```

## Truth And Projections

```text
messages.ndjson = append-only coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

If the files disagree, `messages.ndjson` wins.

## Message Log

Each line in `messages.ndjson` is one JSON object.

Minimum fields:

```json
{
  "id": "2026-01-01T00:00:00Z-codex-my-project-b01-task-ready",
  "from": "codex",
  "to": "claude",
  "projectSlug": "my-project",
  "runId": "mode-b-my-project-docs-sweep-b01",
  "taskId": "TASK-MY-PROJECT-B01-T01-DOCS-SWEEP",
  "type": "TASK_READY",
  "summary": "Task card ready.",
  "createdAt": "2026-01-01T00:00:00Z"
}
```

Common event types:

```text
INIT
TASK_READY
CLAUDE_STARTED
REPORT_READY
CODEX_REVIEW_STARTED
REVIEW_ACCEPTED
REVIEW_NEEDS_FIX
REVIEW_BLOCKED
OWNER_DECISION_REQUIRED
OWNER_REVIEW_REQUIRED
BOARD_UPDATED
HEARTBEAT
ERROR
```

## Write Rules

- Never edit old `messages.ndjson` lines.
- Append correction events instead of rewriting history.
- Update `state.json` and `BOARD.md` with temp-file plus atomic rename when
  possible.
- One actor should append at most one action event per heartbeat.

## Finite Run Rule

Every run declares:

```json
{
  "batch": {
    "enabled": true,
    "maxTasks": 1,
    "completedTasks": 0,
    "stopAfterMaxTasks": true,
    "terminalStateAfterBatch": "OWNER_REVIEW_REQUIRED"
  }
}
```

No unbounded backlog is authorized.

