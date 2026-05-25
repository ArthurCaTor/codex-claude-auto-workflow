# Mode B Board

STATUS: IDLE - reset pending; no Claude task is executable.

## 当前状态

- Protocol: `CURRENT_ONLY_KISS_V1`
- Seq: `1`
- Run: `<runId>`
- Active task: none
- Payload type: `RESET_PENDING`
- Status: `IDLE`
- Bell holder: `codex`
- Current packet: `docs/operations/agent-coordination/auto/CURRENT.md`
- Codex monitor automation: `<monitorId>` (`PAUSED`)

## Active Truth

```text
docs/operations/agent-coordination/auto/BELL.json
docs/operations/agent-coordination/auto/CURRENT.md
```

`BELL.json.seq` 必须等于 `CURRENT.md` 的 `SEQ`。

`messages.ndjson`、旧 task card/report/review、terminal recap、timestamp、
event id、file mtime 都只是 legacy/debug/audit context，不能决定当前 actor、
当前 task 或 turn order。
