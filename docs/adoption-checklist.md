# Adoption Checklist

- [ ] Replace `<PROJECT_ROOT>`, `<PROJECT_NAME>`, `<PROJECT_SLUG>`,
      `<RUN_ID>`, `<MONITOR_ID>`, and all task/report/review paths.
- [ ] Create `auto/BELL.json`.
- [ ] Create `auto/CURRENT.md`.
- [ ] Create `auto/state.json`.
- [ ] Create `auto/BOARD.md`.
- [ ] Create `auto/README.md`.
- [ ] Create `auto/COMMUNICATION_ASCII_GATE.md`.
- [ ] Create Codex and Claude prompt files.
- [ ] Do not create `messages.ndjson` for active turn truth.
- [ ] Initialize reset-pending: `holder=codex/status=IDLE/taskId=null`.
- [ ] Confirm `BELL.seq == CURRENT.md SEQ`.
- [ ] Confirm Claude watcher uses a hard 60-second loop.
- [ ] Confirm waiting Claude writes no idle heartbeat files.
- [ ] Confirm Claude rechecks the same `seq/taskId` before writing report
      packets.
- [ ] Confirm Codex reviews `READY_FOR_CODEX_REVIEW` before scheduling.
- [ ] Confirm one project uses one Codex heartbeat thread.
- [ ] Confirm no other project names remain.
- [ ] Run `git diff --check`.
