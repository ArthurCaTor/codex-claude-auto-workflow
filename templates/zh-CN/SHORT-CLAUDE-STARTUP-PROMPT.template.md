# Short Claude Startup Prompt

```text
In `<PROJECT_ROOT>`, start the persistent watcher from
docs/operations/agent-coordination/auto/CLAUDE-WATCHER-PROMPT.md; read only
BELL.json + CURRENT.md, require BELL.seq == CURRENT.SEQ, execute only when
holder=claude/status=READY_FOR_CLAUDE, execute only the current task card named
by CURRENT.md, reread BELL.json + CURRENT.md before writing the report packet
to confirm the same seq/taskId, report PROJECTION_DRIFT if it changed, do not
write heartbeat files while waiting, and continue the 60-second watcher loop.
```
