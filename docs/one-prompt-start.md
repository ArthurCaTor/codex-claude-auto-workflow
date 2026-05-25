# One-Prompt Start

Use this in Codex inside a target project.

```text
Initialize Codex-Claude Auto Workflow in this project under CURRENT_ONLY_KISS_V1.

Do not implement business features yet. First prepare a finite Codex-managed /
Claude-Code-executed workflow.

Use these rules:
- active communication truth is only BELL.json + CURRENT.md
- BELL.json is the small machine signal
- CURRENT.md is the only active detailed communication packet
- BELL.seq must equal CURRENT.SEQ before anyone acts
- state.json and BOARD.md are projections/debug only
- messages.ndjson, old task cards, old reports, old reviews, terminal recaps,
  timestamps, event ids, and file mtimes are legacy/debug only
- one project uses one Codex heartbeat thread
- Codex writes CURRENT.md first, then BELL.json
- Claude reports by writing the report, rereading BELL/CURRENT to confirm the
  same seq/taskId, then writing CURRENT.md first and BELL.json second
- Claude is a persistent 60-second watcher, not a one-shot runner
- Claude must not self-select the next task
- READY_FOR_CODEX_REVIEW must be reviewed before Codex publishes another card

Create or update:
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/CURRENT.md
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/COMMUNICATION_ASCII_GATE.md
- docs/operations/agent-coordination/auto/CODEX-HEARTBEAT-PROMPT.md
- docs/operations/agent-coordination/auto/CLAUDE-WATCHER-PROMPT.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

Initialize reset-pending:
- holder=codex
- status=IDLE
- taskId=null
- payloadType=RESET_PENDING

Do not start/stop Claude Code, deploy, install dependencies, call external
APIs, use secrets, change schema/migrations, commit, push, merge, open PRs, or
continue into business implementation.

Output the file list, current seq, holder/status/taskId, heartbeat status,
validation results, and one short Claude Code watcher prompt.
```
