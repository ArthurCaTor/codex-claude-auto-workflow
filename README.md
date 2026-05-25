# Codex-Claude Auto Workflow

An automated, file-based workflow for Codex-managed Claude Code execution.

Language: [English](README.md) | [Simplified Chinese](README.zh-CN.md)

Codex-Claude Auto Workflow turns a manual copy/paste relay into a bounded,
low-touch automation loop:

```text
Codex       = planner, project manager, task-card author, reviewer
Claude Code = executor of exactly one active bounded task
Human owner = authorization gate for risk, scope, and release actions
Files       = shared coordination state
```

The current standard is **CURRENT_ONLY_KISS_V1**.

```text
BELL.json  = tiny machine signal: who should act now
CURRENT.md = the only active detailed communication packet
state.json = machine-readable projection/debug
BOARD.md   = human-readable projection/debug
```

`messages.ndjson`, old task cards, old reports, old reviews, terminal recaps,
timestamps, event ids, and file mtimes are legacy/debug only. They must not
decide the current task, freshness, or turn order.

## Why This Exists

The simplest reliable model is a bell:

```text
holder=claude / READY_FOR_CLAUDE       -> Claude Code works
holder=codex  / READY_FOR_CODEX_REVIEW -> Codex reviews
holder=codex  / IDLE                   -> Codex schedules one safe task
holder=arthur or hard gate             -> owner decides
```

Whenever one actor needs another actor to do something, it hands over the bell
with an explicit `holder` and `status`. Details stay in `CURRENT.md`, task
cards, reports, and reviews.

This prevents the two models from missing each other's signals or deadlocking
on stale terminal summaries.

## What It Can Do

- Run documentation audits and source sweeps.
- Delegate narrow implementation slices to Claude Code.
- Keep Codex in charge of sequencing, review, and acceptance.
- Run one fixed task chain one Claude card at a time.
- Return bounded same-task fixes to Claude after Codex review.
- Support multiple projects safely through project-specific names and one
  Codex heartbeat thread per project.
- Reduce manual copy/paste to one Codex startup prompt and one Claude watcher
  prompt.

## What It Does Not Do

- It is not a daemon or hosted service.
- It does not start, stop, or control Claude Code.
- It does not replace project-specific architecture rules.
- It does not authorize deployment, secrets, dependency installation, commits,
  pushes, migrations, schema changes, or external API calls by default.
- It does not let Claude Code self-select the next task.

## How It Works

Each project gets:

```text
docs/operations/agent-coordination/
  auto/
    BELL.json
    CURRENT.md
    state.json
    BOARD.md
    README.md
    COMMUNICATION_ASCII_GATE.md
    CODEX-HEARTBEAT-PROMPT.md
    CLAUDE-WATCHER-PROMPT.md
  inbox/
    TASK-*.md
  reports/
    TASK-*-claude-report.md
  codex-reviews/
    TASK-*-codex-review.md
```

The only freshness gate is:

```text
BELL.json.seq == CURRENT.md SEQ
```

If either file is unreadable, malformed, partially written, missing required
fields, or seq-mismatched, report `PROJECTION_DRIFT` and do not act.

## One-Prompt Start

Open Codex in the target project and paste:

[docs/one-prompt-start.md](docs/one-prompt-start.md)

Codex will prepare the local coordination files and print a short Claude Code
watcher prompt. Claude then stays alive in a 60-second watch loop and executes
only when `holder=claude/status=READY_FOR_CLAUDE`.

## Key Guarantees

- One Codex thread monitors one project.
- Codex writes `CURRENT.md` first, then `BELL.json`.
- Claude writes the report, rereads BELL/CURRENT to confirm it still owns the
  same `seq/taskId`, then writes `CURRENT.md` first and `BELL.json` second.
- `READY_FOR_CODEX_REVIEW` is a review gate, never a scheduling shortcut.
- Every task has allowed files, forbidden scope, validation, and stop
  conditions.
- Owner-gated actions stop for human approval.

## Repository Layout

```text
docs/
  zh-CN/
  adoption-checklist.md
  bell.md
  heartbeat.md
  naming.md
  one-prompt-start.md
  workflow.md
  quickstart.md
  report-only-fix-loop.md
  roles.md
  state-machine.md
  task-chain-prompts.md
examples/
  minimal-run/
templates/
  zh-CN/
  AUTO-README.template.md
  BELL.initial.json
  CURRENT.initial.md
  BOARD.template.md
  COMMUNICATION_ASCII_GATE.template.md
  CODEX-HEARTBEAT-PROMPT.template.md
  CLAUDE-WATCHER-PROMPT.template.md
  CODEX-START-PROMPT.template.md
  SHORT-CLAUDE-STARTUP-PROMPT.template.md
  STATE.initial.json
  TASK-CARD.template.md
  TASK-CHAIN-PROMPTS.md
```

## License

MIT. See [LICENSE](LICENSE).
