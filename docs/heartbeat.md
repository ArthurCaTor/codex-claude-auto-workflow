# Heartbeat

## Codex Heartbeat

On each heartbeat, Codex:

1. Reads `BELL.json` first.
2. Verifies the bell against `messages.ndjson` and `state.json`.
3. Reconciles projections if needed.
4. Reads `BOARD.md`.
5. If `holder = "codex"` and status is `READY_FOR_CODEX_REVIEW`, reads the Claude report and local
   diff.
6. Verifies allowed files and stop lines.
7. Runs safe validation.
8. Writes a Codex review.
9. Appends one Codex event.
10. Updates `BELL.json`, `state.json`, and `BOARD.md`.
11. Stops or reports status to the owner.

## Claude Watcher

Claude Code:

1. Reads `BELL.json`, then `messages.ndjson`, `state.json`, and `BOARD.md`.
2. If `holder` is not `claude`, waits and rereads.
3. If `holder = "claude"` and status is `READY_FOR_CLAUDE`, reads the active task card.
4. Executes only that card.
5. Writes the report.
6. Appends one `REPORT_READY`, `OWNER_DECISION_REQUIRED`, `BLOCKED`, or
   `ERROR` event.
7. Updates `BELL.json` and projections to `READY_FOR_CODEX_REVIEW` with
   `holder = "codex"`.
8. Waits for Codex review.
