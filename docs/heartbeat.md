# Codex Heartbeat

The Codex app heartbeat is a wake-up mechanism. It is not communication truth.
The active truth is only matching `BELL.json + CURRENT.md`.

## Isolation

One Codex heartbeat thread monitors one project root.

Do not create a dual-project monitor. If the current Codex thread already has
another active heartbeat, keep the new monitor paused and use a dedicated Codex
thread for the new project.

## Wake Logic

On each wake:

```text
1. Read BELL.json.
2. Read CURRENT.md.
3. Require BELL.seq == CURRENT.SEQ.
4. Derive holder/status.
5. Act only for the current signal.
```

`READY_FOR_CODEX_REVIEW` means review first. Codex must inspect the report,
local diff, allowed files, and validation before accepting, requesting a bounded
fix, or publishing another card.

`IDLE` means Codex may publish exactly one safe bounded card.

`READY_FOR_CLAUDE` means Claude owns the turn; Codex waits.

## Forbidden By Default

Do not start, stop, kill, or restart Claude Code. Do not install dependencies,
touch schema or migrations, deploy, use secrets, call external APIs, commit,
push, merge, open PRs, publish releases, or move/delete audit evidence unless
the owner explicitly authorizes that boundary.
