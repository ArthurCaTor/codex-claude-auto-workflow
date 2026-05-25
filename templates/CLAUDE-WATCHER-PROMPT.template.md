# Claude Current-Only Watcher Prompt

Start a persistent watcher in `<PROJECT_ROOT>`.

This is not a one-shot task. Do not exit after writing a report unless Codex
explicitly says `no watch needed / exit watch`.

Only read active communication truth:

```text
docs/operations/agent-coordination/auto/BELL.json
docs/operations/agent-coordination/auto/CURRENT.md
```

At the start of every loop:

1. Read `BELL.json`.
2. Read `CURRENT.md`.
3. Confirm `BELL.json.seq == CURRENT.md SEQ`.
4. If either file is unreadable, malformed, partially written, missing required
   fields, or seq-mismatched, report `PROJECTION_DRIFT`, do not act, sleep 60
   seconds, then reread.

Only work when:

```text
BELL.json.holder = claude
BELL.json.status = READY_FOR_CLAUDE
```

Execute only the current task named by `CURRENT.md`. Do not self-select or
generate the next task card.

When finished:

1. Write the required report.
2. Before overwriting `CURRENT.md` or `BELL.json`, reread `BELL.json` first and
   `CURRENT.md` second. Confirm `holder=claude`, `status=READY_FOR_CLAUDE`,
   the same seq that you executed, and the same taskId.
3. If anything changed, report `PROJECTION_DRIFT`, do not overwrite
   `CURRENT.md` or `BELL.json`, and continue the 60-second watcher loop.
4. Overwrite `CURRENT.md` first with the next-seq `REPORT_READY` packet.
5. Overwrite `BELL.json` second with the same seq, `holder=codex`, and
   `status=READY_FOR_CODEX_REVIEW`.
6. Continue the hard 60-second watcher loop.

Waiting rules:

- Every idle loop runs again after 60 seconds.
- Every idle loop rereads `BELL.json` first, then `CURRENT.md`.
- Every idle/status loop prints:
  `WAIT_SIGNAL runId=<runId> taskId=<taskId-or-null> bellSeq=<BELL.json.seq> currentSeq=<CURRENT.md SEQ> bellHolder=<BELL.json.holder> status=<BELL.json.status> waitingFor=<CLAUDE_WORK|CODEX_REVIEW|CODEX_TASK_READY|TERMINAL_OR_OWNER> reason=<short reason> Next poll in 60s`
- Idle loops write no heartbeat files.
- If context compaction or summarization occurs, preserve this hard 60-second
  watcher rule, the current task id when one exists, and the BELL/CURRENT disk
  reread loop in the compacted context.
