# State Machine

CURRENT_ONLY_KISS_V1 keeps the active state small.

```text
RESET_PENDING / IDLE
  Codex may publish one TASK_READY packet.

READY_FOR_CLAUDE
  Claude may execute the one task named by CURRENT.md.

READY_FOR_CODEX_REVIEW
  Codex must review before any next task.

NEEDS_FIX
  Codex may return the same active task to Claude only inside allowed files and
  retry budget.

OWNER_DECISION_REQUIRED / BLOCKED
  Stop for owner.
```

Normal flow:

```text
codex/IDLE
  -> codex writes CURRENT TASK_READY
  -> codex writes BELL holder=claude/READY_FOR_CLAUDE
  -> claude executes one card
  -> claude writes report
  -> claude rechecks same seq/taskId
  -> claude writes CURRENT REPORT_READY
  -> claude writes BELL holder=codex/READY_FOR_CODEX_REVIEW
  -> codex reviews
  -> accept, bounded fix, next one-card packet, or owner stop
```

There is no unbounded backlog. Claude never self-advances.
