# Codex-Claude Auto Workflow

An automated, file-based workflow for Codex-managed Claude Code execution.

Codex-Claude Auto Workflow turns a manual copy/paste relay into a bounded,
auditable, low-touch automation flow:

```text
Codex       = planner, project manager, task-card author, reviewer
Claude Code = executor of exactly one active bounded task
Human owner = authorization gate for risk, scope, and release actions
Files       = shared coordination state
```

The workflow is useful when you want Codex to manage a project and review work,
while Claude Code performs implementation, documentation, validation, or audit
tasks in a visible terminal. It keeps the collaboration running automatically
inside a finite task batch, while still stopping at owner gates.

## Purpose

This workflow exists to make multi-agent development safer, more automated, and
less tiring.

Without it, the owner often has to copy messages between Codex and Claude Code:

```text
Owner asks Codex to plan
Owner copies Codex instructions into Claude Code
Claude Code executes
Owner copies Claude report back into Codex
Codex reviews
Owner copies the next fix or task back into Claude Code
```

Codex-Claude Auto Workflow replaces most of that manual relay with a small
file-heartbeat state machine:

```text
Codex writes a bounded task card
Codex initializes a finite run
Claude Code watches the coordination files
Claude Code executes only the active task
Claude Code writes a report and appends an event
Codex heartbeat reviews the report and local diff
Codex accepts, blocks, or returns a bounded report-only fix
The run stops for owner review
```

The goal is automated flow, not unlimited autonomy. Codex and Claude can keep a
bounded run moving with minimal owner copy/paste, including limited
report-only fix loops, but deployment, secrets, schema changes, dependency
installs, commits, pushes, and scope expansion still require explicit owner
authorization.

## What It Can Do

- Run documentation audits.
- Run source sweeps and inventory tasks.
- Delegate narrow implementation slices to Claude Code.
- Keep Codex in charge of task sequencing and acceptance.
- Preserve a durable audit trail of handoff, report, review, and owner gates.
- Support multiple projects at the same time through unique run/task names.
- Reduce human copy/paste to one startup prompt for Codex and one short startup
  prompt for Claude Code.

## What It Does Not Do

- It is not a daemon or hosted service.
- It does not start or control Claude Code.
- It does not remove the need for project-specific architecture rules.
- It does not authorize deployment, secrets, dependency installation, commits,
  pushes, migrations, or external API calls by default.
- It does not let Claude Code self-advance through an unbounded backlog.

## How It Works

Each project gets a small coordination directory:

```text
docs/operations/agent-coordination/
  auto/
    README.md
    messages.ndjson
    state.json
    BOARD.md
  inbox/
    TASK-*.md
  reports/
    TASK-*-claude-report.md
  codex-reviews/
    TASK-*-codex-review.md
```

The central truth rule:

```text
messages.ndjson = append-only coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

If the files disagree, `messages.ndjson` wins.

## One-Prompt Start

To start the whole setup in a new project, open Codex in that project and paste
the prompt from:

[docs/one-prompt-start.md](docs/one-prompt-start.md)

Codex will then:

1. Read the target project's rules and git status.
2. Pick project-specific names.
3. Create the coordination directories and files.
4. Create the first low-risk task card.
5. Prepare a Codex heartbeat prompt.
6. Print a short Claude Code startup prompt for the owner to paste into
   Claude Code.
7. Stop and ask for owner authorization before execution.

## Key Guarantees

- Every run is finite.
- Every task has allowed files, forbidden scope, validation, report path, and
  stop conditions.
- Claude Code executes only one active task card.
- Codex independently reviews Claude reports and local diffs.
- Owner-gated actions stop for human approval.
- Report issues can loop back to Claude with a bounded report-only fix loop.
- Generic automation names are forbidden, so multiple projects can run safely.

## Quick Start

Read:

- [docs/one-prompt-start.md](docs/one-prompt-start.md)
- [docs/quickstart.md](docs/quickstart.md)
- [docs/workflow.md](docs/workflow.md)
- [docs/naming.md](docs/naming.md)

Then copy the templates from [templates/](templates/).

## Repository Layout

```text
docs/
  adoption-checklist.md
  heartbeat.md
  naming.md
  one-prompt-start.md
  workflow.md
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

## License

MIT. See [LICENSE](LICENSE).
