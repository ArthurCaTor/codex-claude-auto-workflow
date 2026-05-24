# Short Claude Startup Prompt

```text
In `<absolute-project-path>`, run the Mode B watcher.

Read first:
- AGENTS.md / CLAUDE.md if present
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/<task-id>.md

Handle only run `mode-b-<projectSlug>-<runSlug>` and active task `<task-id>`.

Loop:
- Read messages.ndjson first, then state.json and BOARD.md.
- Work only when status = READY_FOR_CLAUDE; otherwise wait 20 seconds and reread.
- Execute only the active task card.
- Write only the allowed files: Claude report plus auto/messages.ndjson, auto/state.json, auto/BOARD.md.
- After the report, append one REPORT_READY event and update state/BOARD to READY_FOR_CODEX_REVIEW.
- If Codex sends reviewVerdict=NEEDS_FIX, fix only report/coordination files. Do not edit source.
- Stop on OWNER_REVIEW_REQUIRED / OWNER_DECISION_REQUIRED / BLOCKED / ERROR.

Forbidden: do not start/stop services, edit outside allowed files, install dependencies, deploy, call external APIs, use secrets, commit/push/merge, or advance to another task.
```

