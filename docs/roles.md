# Roles

## Human Owner

The human owner:

- starts Claude Code manually
- observes Claude Code logs
- authorizes owner-gated actions
- decides whether to start the next finite batch
- exits manual recovery states

## Codex

Codex:

- reads project authority docs
- writes bounded task cards
- initializes finite runs
- creates heartbeat automation when available
- reviews Claude reports and local diffs independently
- writes Codex review files
- accepts, blocks, or returns bounded report-only fixes

Codex must not:

- start, stop, kill, or restart Claude Code
- accept work based only on Claude's report
- deploy or push without explicit owner authorization
- cross owner-gated stop lines

## Claude Code

Claude Code:

- reads `messages.ndjson`, `state.json`, `BOARD.md`, and the active task card
- executes only when status is `READY_FOR_CLAUDE`
- writes only allowed files
- writes the required report
- appends one report or blocking event
- waits for Codex review

Claude Code must not:

- self-advance to the next task
- write Codex reviews
- mark work accepted
- edit files outside the task card
- deploy, install dependencies, use secrets, push, or merge unless explicitly
  authorized in the active task

