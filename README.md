# Mode B File Heartbeat

Mode B File Heartbeat is a lightweight coordination protocol for running two
AI coding agents together:

```text
Codex      = planner, project manager, reviewer
Claude Code = bounded executor
Human owner = authorization gate
Files      = shared coordination state
```

It is for teams who want Codex to manage task slicing and review while Claude
Code executes one bounded task at a time, without requiring the human owner to
copy every intermediate result back and forth.

## What It Solves

The common manual workflow looks like this:

```text
Human -> Codex: plan the task
Human copies Codex task -> Claude Code
Claude Code executes
Human copies Claude report -> Codex
Codex reviews
Human copies fix or next task -> Claude Code
```

Mode B replaces most of that copy/paste with a file-based heartbeat:

```text
Codex writes task card + state
Claude Code watches files and executes only the active task
Claude Code writes report + event
Codex heartbeat reviews report + diff
Codex accepts, blocks, or sends a bounded report-only fix
```

## Key Guarantees

- Every run is finite.
- Claude Code executes only one active task card.
- `messages.ndjson` is append-only coordination truth.
- `state.json` and `BOARD.md` are projections.
- Codex independently reviews Claude reports and local diffs.
- Owner-gated actions stop for human approval.
- Report issues can loop back to Claude with a bounded report-only fix loop.
- Generic automation names are forbidden, so multiple projects can run safely.

## Quick Start

1. Read [docs/quickstart.md](docs/quickstart.md).
2. Copy the templates from [templates/](templates/).
3. Adapt the project-specific names from [docs/naming.md](docs/naming.md).
4. Create one low-risk task card.
5. Start Claude Code manually with the short startup prompt.
6. Let Codex heartbeat review the report.
7. Stop at `OWNER_REVIEW_REQUIRED`.

## Repository Layout

```text
docs/
  adoption-checklist.md
  heartbeat.md
  naming.md
  protocol.md
  quickstart.md
  report-only-fix-loop.md
  roles.md
  state-machine.md
examples/
  minimal-run/
templates/
  AUTO-README.template.md
  BOARD.template.md
  CODEX-HEARTBEAT-PROMPT.template.md
  CODEX-REVIEW.template.md
  CODEX-START-PROMPT.template.md
  SHORT-CLAUDE-STARTUP-PROMPT.template.md
  STATE.initial.json
  MESSAGES.initial.ndjson
  TASK-CARD.template.md
```

## Status

This is a documentation and process template. It does not provide a runtime
daemon. Codex and Claude Code remain separate tools; this protocol defines how
they coordinate through files.

## License

MIT. See [LICENSE](LICENSE).
