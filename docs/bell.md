# Shared Bell Workflow v2

`BELL.json` is the small shared turn signal for one Codex session paired with
one Claude Code session.

It answers only one question:

```text
Who should act now?
```

Everything else belongs in the detailed files:

```text
task card     = what Claude should do
Claude report = what Claude did
Codex review  = whether Codex accepts it
messages log  = append-only audit trail
```

## System Diagram

```text
              +------------------+
              |     Arthur       |
              | starts Claude    |
              | authorizes gates |
              +---------+--------+
                        |
                        v
+-----------------------+--------------------------------+
| docs/operations/agent-coordination/                    |
|                                                        |
| auto/BELL.json       -> shared turn signal             |
| auto/messages.ndjson -> append-only audit truth        |
| auto/state.json      -> machine projection             |
| auto/BOARD.md        -> human projection               |
| inbox/TASK-*.md      -> detailed task instructions     |
| reports/*.md         -> Claude detailed reports        |
| codex-reviews/*.md   -> Codex detailed reviews         |
+-------------+---------------------------+--------------+
              |                           |
              v                           v
      +-------+--------+          +-------+--------+
      | Claude Code    |          | Codex          |
      | executes task  |          | reviews/plans  |
      +----------------+          +----------------+
```

## Bell Fields

```json
{
  "version": 1,
  "holder": "claude",
  "status": "READY_FOR_CLAUDE",
  "runId": "mode-b-my-project-docs-sweep-b01",
  "taskId": "TASK-MY-PROJECT-B01-T01-DOCS-SWEEP",
  "taskCardPath": "docs/operations/agent-coordination/inbox/TASK-MY-PROJECT-B01-T01-DOCS-SWEEP.md",
  "reportPath": "docs/operations/agent-coordination/reports/TASK-MY-PROJECT-B01-T01-DOCS-SWEEP-claude-report.md",
  "waitingFor": "claude_execution",
  "lastEventId": "2026-01-01T00:00:01Z-codex-task-ready",
  "updatedAt": "2026-01-01T00:00:01Z",
  "summary": "Codex handed the active bounded task to Claude Code."
}
```

## Holder Rule

```text
holder=claude -> Claude Code works; Codex waits.
holder=codex  -> Codex reviews, closes out, or prepares the next bounded task.
holder=arthur -> Arthur decides; both agents stop advancing.
```

Allowed holder/status combinations:

```text
claude + READY_FOR_CLAUDE
claude + CLAUDE_RUNNING
codex  + READY_FOR_CODEX_REVIEW
codex  + CODEX_REVIEWING
codex  + OWNER_REVIEW_REQUIRED when delegated continuation is recorded
arthur + OWNER_REVIEW_REQUIRED when not delegated
arthur + OWNER_DECISION_REQUIRED
arthur + BLOCKED
arthur + ERROR
```

## Data Flow

```text
Codex writes task card
  -> Codex appends TASK_READY to messages.ndjson
  -> Codex writes BELL holder=claude
  -> Claude reads BELL, then verifies state/log
  -> Claude executes active task
  -> Claude writes report
  -> Claude appends REPORT_READY to messages.ndjson
  -> Claude writes BELL holder=codex
  -> Codex reads BELL, report, diff, and log
  -> Codex writes review
  -> Codex accepts, returns bounded fix, blocks, or hands a new task to Claude
```

## State Machine

```text
holder=claude
READY_FOR_CLAUDE
  |
  v
holder=claude
CLAUDE_RUNNING
  |
  v
holder=codex
READY_FOR_CODEX_REVIEW
  |
  +--> ACCEPTED -> OWNER_REVIEW_REQUIRED or next READY_FOR_CLAUDE
  |
  +--> NEEDS_FIX -> holder=claude / READY_FOR_CLAUDE same task
  |
  +--> BLOCKED or OWNER_DECISION_REQUIRED -> holder=arthur
```

## Two-Lane Rule

Keep the lanes separate:

```text
Lane A: BELL.json
  Small, latest-turn projection. Used to avoid missed signals.

Lane B: task/report/review/log files
  Detailed instructions, evidence, validation, review, and audit history.
```

Do not put detailed implementation instructions into `BELL.json`. Put them in
the task card. Do not put long report evidence into `BELL.json`. Put it in the
Claude report.

## Conflict Rule

Read order:

```text
1. BELL.json
2. messages.ndjson
3. state.json
4. BOARD.md
5. active task card or report as needed
```

`BELL.json`, `state.json`, and `BOARD.md` are projections. If they conflict
with `messages.ndjson`, stop and resync before acting. Never guess from terminal
recap text when repo files disagree.

## Forbidden Signal Files

Do not create role-specific signal files such as:

```text
CLAUDE_SIGNAL.json
CODEX_SIGNAL.json
NEXT_TASK.json
LATEST_STATUS.json
```

One shared bell is enough for one Codex session and one Claude Code session.
