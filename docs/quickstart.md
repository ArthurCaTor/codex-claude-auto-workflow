# Quick Start

This guide initializes Codex-Claude Auto Workflow in a new project.

For the shortest path, paste the prompt from
[one-prompt-start.md](one-prompt-start.md) into Codex from inside your target
project. The rest of this document explains what that prompt causes Codex to
create.

## 1. Prepare The Project

In the target project, identify the project instruction files:

```text
AGENTS.md
CLAUDE.md
README.md
docs/architecture/*
package.json or equivalent test command source
```

If the project has no agent instruction file, create one before enabling the
workflow.

## 2. Create Coordination Directories

```text
docs/operations/agent-coordination/
  auto/
  inbox/
  reports/
  codex-reviews/
```

## 3. Pick Unique Names

Every run must use globally distinct names.

```text
projectSlug = my-project
runSlug     = docs-sweep-b01
runId       = mode-b-my-project-docs-sweep-b01
automation  = mode-b-my-project-docs-sweep-b01-monitor
taskId      = TASK-MY-PROJECT-B01-T01-DOCS-SWEEP
```

See [naming.md](naming.md).

## 4. Add Coordination Files

Copy and adapt:

```text
templates/AUTO-README.template.md -> docs/operations/agent-coordination/auto/README.md
templates/STATE.initial.json      -> docs/operations/agent-coordination/auto/state.json
templates/MESSAGES.initial.ndjson -> docs/operations/agent-coordination/auto/messages.ndjson
templates/BOARD.template.md       -> docs/operations/agent-coordination/auto/BOARD.md
templates/TASK-CARD.template.md   -> docs/operations/agent-coordination/inbox/<taskId>.md
```

## 5. Start With A Low-Risk Task

Good first tasks:

- read-only source sweep
- docs-only inventory
- test-command inventory
- guard inventory
- current-status report

Avoid first:

- schema changes
- migrations
- dependency install
- deployment
- external APIs
- secrets
- commits and pushes
- broad refactors

## 6. Start Codex Heartbeat

Use [../templates/CODEX-HEARTBEAT-PROMPT.template.md](../templates/CODEX-HEARTBEAT-PROMPT.template.md)
to create a Codex heartbeat automation for exactly one run.

## 7. Start Claude Code Manually

The human owner manually opens Claude Code in the target project directory and
pastes the adapted short prompt from
[../templates/SHORT-CLAUDE-STARTUP-PROMPT.template.md](../templates/SHORT-CLAUDE-STARTUP-PROMPT.template.md).

Do not ask Codex to start, stop, kill, or restart Claude Code.

## 8. Stop At Owner Review

When the finite batch completes, Codex sets:

```text
OWNER_REVIEW_REQUIRED
```

The owner decides whether to start another finite batch.
