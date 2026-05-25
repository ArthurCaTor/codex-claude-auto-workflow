# Mode B Board

STATUS: IDLE - reset pending; no Claude task is executable.

## Current State

- Protocol: `CURRENT_ONLY_KISS_V1`
- Seq: `1`
- Run: `<runId>`
- Active task: none
- Payload type: `RESET_PENDING`
- Status: `IDLE`
- Bell holder: `codex`
- Current packet: `docs/operations/agent-coordination/auto/CURRENT.md`
- Task card: none
- Report path: none
- Codex monitor automation: `<monitorId>` (`PAUSED`)

## Active Truth

```text
docs/operations/agent-coordination/auto/BELL.json
docs/operations/agent-coordination/auto/CURRENT.md
```

`BELL.json.seq` must equal `CURRENT.md` `SEQ` before either agent acts.

`messages.ndjson`, old task cards, old reports, old reviews, terminal recaps,
timestamps, event ids, and file mtimes are legacy/debug/audit context only.
They must not decide current actor, current task, or turn order.

## Reset Boundary

No Claude task is executable until Codex republishes a fresh `TASK_READY`
packet in `CURRENT.md` and then updates `BELL.json` with the same seq.
