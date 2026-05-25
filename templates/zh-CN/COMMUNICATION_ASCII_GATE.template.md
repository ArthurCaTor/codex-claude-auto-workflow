# Communication ASCII Gate

```text
                 +----------------------+
                 |  Codex heartbeat     |
                 +----------+-----------+
                            |
                            v
                 先读 BELL.json
                 再读 CURRENT.md
                            |
                            v
              BELL.seq == CURRENT.SEQ ?
                    | yes        | no
                    v            v
             推导信号         PROJECTION_DRIFT
```

信号定义：

```text
holder=claude / READY_FOR_CLAUDE
  -> Claude watcher 执行任务

holder=codex / READY_FOR_CODEX_REVIEW
  -> Codex 审查 report + diff + validation

holder=codex / IDLE
  -> Codex 调度一张安全有界任务卡

holder=arthur 或 hard gate
  -> 停下等待 owner
```
