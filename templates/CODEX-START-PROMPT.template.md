# Codex Start Prompt

```text
Use CURRENT_ONLY_KISS_V1 to initialize this project's Codex-Claude Auto
Workflow.

Do not implement business features yet.

Read project instructions, current docs, git status, and then create or update
the local coordination files under docs/operations/agent-coordination/auto/.

Rules:
- active communication truth is only BELL.json + CURRENT.md
- BELL.seq must equal CURRENT.md SEQ before anyone acts
- BELL.json is the small machine signal
- CURRENT.md is the only active detailed packet
- state.json and BOARD.md are projections/debug
- messages.ndjson, old task cards, old reports, old reviews, terminal recaps,
  timestamps, event ids, and file mtimes are legacy/debug only
- if files are unreadable, partial, missing fields, or seq-mismatched, report
  PROJECTION_DRIFT and do not act
- Codex publishes tasks by writing CURRENT.md first, then BELL.json
- Claude reports by writing the report, rereading BELL/CURRENT to confirm the
  same seq/taskId, then writing CURRENT.md first and BELL.json second
- Claude is a persistent 60-second watcher and does not self-select the next
  task
- READY_FOR_CODEX_REVIEW must be reviewed before Codex publishes another card

Initialize reset-pending with holder=codex/status=IDLE/taskId=null.

Output the file list, current seq, holder/status/taskId, heartbeat status,
validation results, and one short Claude Code watcher prompt.
```
