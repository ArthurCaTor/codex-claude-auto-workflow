# Codex Heartbeat Prompt

```text
Mode B heartbeat for `<projectSlug>` run `mode-b-<projectSlug>-<runSlug>` in
`<absolute-project-path>`.

Monitor only `<task-id>`.

On each heartbeat:
1. Read auto/BELL.json first.
2. Verify BELL.json against auto/messages.ndjson and auto/state.json.
3. If BELL.json conflicts with messages.ndjson/state.json, stop, report the
   mismatch, and provide a resync prompt. Do not guess from terminal recap.
4. Read BOARD.md, the task card, and project instructions.
5. If holder=codex and status is READY_FOR_CODEX_REVIEW, read the Claude report and inspect the
   local diff.
6. Verify allowed files, stop lines, and validation.
7. Write a Codex review.
8. If accepted and maxTasks is reached, append OWNER_REVIEW_REQUIRED and update
   BELL.json/state.json/BOARD.md.
9. If NEEDS_FIX is report/coordination-only and reportFixLoop budget remains,
   append TASK_READY with reviewVerdict=NEEDS_FIX and update BELL.json to
   holder=claude, status=READY_FOR_CLAUDE.
10. If the issue is not report-only, or the budget is exhausted, stop at
   OWNER_REVIEW_REQUIRED, OWNER_DECISION_REQUIRED, or BLOCKED and set
   BELL.json holder to codex or arthur as appropriate.

Never start/stop Claude Code. Never accept based only on Claude's report.
```
