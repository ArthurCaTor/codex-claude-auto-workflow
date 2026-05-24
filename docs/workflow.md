# Workflow

Codex-Claude Auto Workflow is an automated file-heartbeat workflow for
coordinating Codex and Claude Code through project files.

Codex owns planning, task boundaries, heartbeat review, and acceptance. Claude
Code watches the coordination files, executes the single active task card, and
reports back. The human owner starts Claude Code manually and authorizes
owner-gated actions.

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

## Automation Rule

The workflow is designed for low-touch automation inside a finite run:

- Claude Code may keep watching the files for the active task.
- Codex heartbeat may independently review Claude reports and local diffs.
- Failed reviews may return to Claude only as a bounded report-only fix loop.
- The run must stop at `OWNER_REVIEW_REQUIRED` when the batch limit is reached.

Automated does not mean ownerless. High-risk actions such as schema changes,
dependency installation, deployments, commits, pushes, secrets, and external API
calls require explicit owner authorization.
