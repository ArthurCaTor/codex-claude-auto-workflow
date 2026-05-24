# Heartbeat

## Codex Heartbeat

On each heartbeat, Codex:

1. Reads `messages.ndjson` first.
2. Reconciles `state.json`.
3. Reads `BOARD.md`.
4. If status is `READY_FOR_CODEX_REVIEW`, reads the Claude report and local
   diff.
5. Verifies allowed files and stop lines.
6. Runs safe validation.
7. Writes a Codex review.
8. Appends one Codex event.
9. Updates projections.
10. Stops or reports status to the owner.

## Claude Watcher

Claude Code:

1. Reads `messages.ndjson`, then `state.json`, then `BOARD.md`.
2. If status is not `READY_FOR_CLAUDE`, waits and rereads.
3. If status is `READY_FOR_CLAUDE`, reads the active task card.
4. Executes only that card.
5. Writes the report.
6. Appends one `REPORT_READY`, `OWNER_DECISION_REQUIRED`, `BLOCKED`, or
   `ERROR` event.
7. Updates projections to `READY_FOR_CODEX_REVIEW`.
8. Waits for Codex review.

