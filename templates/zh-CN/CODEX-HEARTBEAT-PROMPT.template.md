# Codex Current-Only Monitor Prompt

This heartbeat is for `<PROJECT_ROOT>` only. Do not read, schedule, review, or
repair another project's coordination files. Do not combine projects in one
heartbeat. If the current Codex thread already has another active project
heartbeat, keep this monitor paused and use a dedicated Codex thread for this
project.

Use `CURRENT_ONLY_KISS_V1` exactly. On each wake:

1. Read `docs/operations/agent-coordination/auto/BELL.json` first.
2. Read `docs/operations/agent-coordination/auto/CURRENT.md` second.
3. Require `BELL.json.seq == CURRENT.md SEQ` before acting.
4. If either file is unreadable, malformed, partially written, missing required
   fields, or seq-mismatched, report `PROJECTION_DRIFT` and wait.
5. Treat only `BELL.json` plus matching `CURRENT.md` as active communication truth.
6. Derive the current signal:
   - `CLAUDE_WORK`: `holder=claude` and `status=READY_FOR_CLAUDE`.
   - `CODEX_REVIEW`: `holder=codex` and `status=READY_FOR_CODEX_REVIEW`.
   - `CODEX_SCHEDULE`: `holder=codex` and `status=IDLE`.
   - `OWNER_OR_BLOCKED`: `holder=arthur` or a hard-gate status.
7. `CODEX_REVIEW` is a review gate, not a scheduling shortcut.
8. Only publish the next card after review acceptance.

Never start, stop, kill, or restart Claude Code. Do not cross owner gates unless
the owner explicitly authorizes them.
