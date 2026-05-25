# Communication ASCII Gate

```text
                 +----------------------+
                 |  Codex heartbeat     |
                 +----------+-----------+
                            |
                            v
                 read BELL.json first
                 read CURRENT.md second
                            |
                            v
              BELL.seq == CURRENT.SEQ ?
                    | yes        | no
                    v            v
             derive signal     PROJECTION_DRIFT
```

Signals:

```text
holder=claude / READY_FOR_CLAUDE
  -> Claude watcher owns execution
  -> Codex waits

holder=codex / READY_FOR_CODEX_REVIEW
  -> Codex reviews report + diff + validation
  -> ACCEPT / NOTES / NEEDS_FIX

holder=codex / IDLE
  -> Codex schedules one safe bounded task
  -> writes CURRENT.md first
  -> writes BELL.json second

holder=arthur or hard gate
  -> stop for owner
```
