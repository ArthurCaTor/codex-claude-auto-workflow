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

## Why This Update Exists

Earlier file-heartbeat versions had enough information, but the turn signal was
too implicit. Codex or Claude could see a report, a heartbeat, a recap, or a log
line and still disagree about whose turn it was.

Shared Bell Workflow v2 fixes that by separating the system into two lanes:

```text
Bell lane   = simple explicit handoff
Detail lane = task details, reports, reviews, validation, audit
```

The bell lane must stay intentionally simple. If one actor needs another actor
to act, it must rewrite `BELL.json` with an explicit `holder` and `status`.
Natural-language `summary`, Claude reports, Codex reviews, and terminal recap
text must never be used to infer whose turn it is.

## System Diagram

```text
                         +------------------+
                         |      Arthur      |
                         | owner decisions  |
                         +---------+--------+
                                   |
                         holder=arthur
                                   |
                                   v
+-------------------+     +-------------------------------+     +-------------------+
| Claude Code       |     | shared repo coordination       |     | Codex             |
| executes one task |<--->|                               |<--->| plans and reviews |
+-------------------+     | Line A: BELL.json             |     +-------------------+
                          | - holder                      |
                          | - status                      |
                          | - task/report pointers        |
                          | - no detailed reasoning       |
                          |                               |
                          | Line B: detailed files        |
                          | - inbox/TASK-*.md             |
                          | - reports/*.md                |
                          | - codex-reviews/*.md          |
                          | - messages.ndjson audit log   |
                          | - state.json / BOARD.md       |
                          +-------------------------------+

Rule: agents may discuss details through Line B, but they only decide whose
turn it is from Line A.
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
Codex needs Claude to work
  Detail lane: write task card; append TASK_READY audit event
  Bell lane:   set holder=claude, status=READY_FOR_CLAUDE
  Result:      Claude works; Codex waits

Claude needs Codex to review
  Detail lane: write report; append REPORT_READY audit event
  Bell lane:   set holder=codex, status=READY_FOR_CODEX_REVIEW
  Result:      Codex reviews; Claude watches

Codex needs Claude to fix the same task
  Detail lane: write Codex review; append TASK_READY with NEEDS_FIX
  Bell lane:   set holder=claude, status=READY_FOR_CLAUDE
  Result:      Claude fixes only the active task's allowed files

Either agent needs Arthur
  Detail lane: write the reason in report/review/log
  Bell lane:   set holder=arthur, status=OWNER_DECISION_REQUIRED
  Result:      both agents stop advancing

Never infer the next actor from summary text. The bell must say it explicitly.
```

## State Machine

```text
+----------------------------------+
| holder=claude                    |
| status=READY_FOR_CLAUDE          |
| action: Claude executes task     |
+----------------+-----------------+
                 |
                 v
+----------------------------------+
| holder=claude                    |
| status=CLAUDE_RUNNING            |
| action: Claude continues task    |
+----------------+-----------------+
                 |
                 v
+----------------------------------+
| holder=codex                     |
| status=READY_FOR_CODEX_REVIEW    |
| action: Codex reviews report     |
+----+---------------+-------------+
     |               |
     | accept        | needs same-task fix
     v               v
+----------------+  +----------------------------------+
| batch done     |  | holder=claude                    |
| holder=codex   |  | status=READY_FOR_CLAUDE          |
| or arthur      |  | action: Claude fixes active task |
| OWNER_REVIEW   |  +----------------------------------+
+----------------+
     |
     | owner-only decision / blocker / error
     v
+----------------------------------+
| holder=arthur                    |
| OWNER_DECISION_REQUIRED/BLOCKED  |
| action: Arthur decides           |
+----------------------------------+
```

State names can carry detailed meaning, but turn ownership must stay explicit:
the next actor is the `holder`, not the prose in a report, review, or summary.

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
