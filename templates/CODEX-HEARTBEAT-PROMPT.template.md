# Codex Heartbeat Prompt

```text
Mode B heartbeat for `<projectSlug>` run `mode-b-<projectSlug>-<runSlug>` in
`<absolute-project-path>`.

Monitor only `<task-id>`.

On each heartbeat:
1. Read auto/messages.ndjson first, then state.json, BOARD.md, the task card,
   and project instructions.
2. If status is READY_FOR_CODEX_REVIEW, read the Claude report and inspect the
   local diff.
3. Verify allowed files, stop lines, and validation.
4. Write a Codex review.
5. If accepted and maxTasks is reached, append OWNER_REVIEW_REQUIRED and update
   projections.
6. If NEEDS_FIX is report/coordination-only and reportFixLoop budget remains,
   append TASK_READY with reviewVerdict=NEEDS_FIX and update projections to
   READY_FOR_CLAUDE.
7. If the issue is not report-only, or the budget is exhausted, stop at
   OWNER_REVIEW_REQUIRED, OWNER_DECISION_REQUIRED, or BLOCKED.

Never start/stop Claude Code. Never accept based only on Claude's report.
```

