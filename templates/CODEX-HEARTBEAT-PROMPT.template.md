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
4. If either file is unreadable, malformed, partially written, or missing
   required fields, do not act. Report `PROJECTION_DRIFT` briefly and wait for
   the next heartbeat/poll.
5. Treat only `BELL.json` plus matching `CURRENT.md` as active communication
   truth. `messages.ndjson`, old task cards, old reports, old reviews,
   terminal recaps, timestamps, event ids, and file mtimes are legacy/debug
   only and must not decide turn order.
6. Derive the current signal:
   - `CLAUDE_WORK`: `holder=claude` and `status=READY_FOR_CLAUDE`.
   - `CODEX_REVIEW`: `holder=codex` and `status=READY_FOR_CODEX_REVIEW`.
   - `CODEX_SCHEDULE`: `holder=codex` and `status=IDLE`.
   - `OWNER_OR_BLOCKED`: `holder=arthur` or a true hard-gate status.
   `CODEX_REVIEW` is a review gate, not a scheduling shortcut.
7. If the signal is `CLAUDE_WORK`, wait.
8. If the signal is `CODEX_REVIEW`, inspect the report path named by
   `CURRENT.md`, inspect the local diff, verify Claude edited only allowed
   files, run safe validation commands, write the Codex review, then only after
   `ACCEPT` or `ACCEPT_WITH_NOTES` consider publishing the next packet.
9. If accepted and another safe bounded card is authorized, publish it through
   `CURRENT.md` first and `BELL.json` second.
10. If `NEEDS_FIX` is bounded to the same active task and allowed files, publish
    a same-task fix packet through `CURRENT.md` first and `BELL.json` second.
11. If the signal is `CODEX_SCHEDULE`, create or activate one safe bounded card,
    or report why only hard-gated work remains.

Never start, stop, kill, or restart Claude Code. Never install dependencies,
touch schema/migrations/package/lockfile/SDK/ADR/module docs/architecture docs,
call external APIs, deploy, use secrets, commit, push, merge, open PRs, publish
releases, or delete/move audit/history/evidence files unless a future active
packet and owner authorization explicitly allow that boundary.
