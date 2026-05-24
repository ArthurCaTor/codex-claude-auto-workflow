# One-Prompt Start

Paste this into Codex from inside the target project. It tells Codex to learn
the project, initialize the workflow, and produce the short Claude Code prompt.

```text
Initialize Codex-Claude Auto Workflow in this project.

Do not implement business features yet. First learn this project and prepare a
finite Codex-managed / Claude-Code-executed workflow.

Use these rules:

- Codex is the planner, project manager, task-card author, and reviewer.
- Claude Code is the bounded executor of exactly one active task card.
- The human owner manually starts Claude Code and authorizes owner-gated actions.
- Files are the coordination layer.
- BELL.json is the shared turn signal: holder=claude means Claude works,
  holder=codex means Codex reviews or prepares the next bounded task, and
  holder=arthur means owner decision required.
- The workflow should run as an automated low-touch file-heartbeat loop inside
  one finite batch.
- messages.ndjson is append-only coordination truth.
- state.json and BOARD.md are projections rebuilt from messages.ndjson if needed.
- Every run is finite and must stop at OWNER_REVIEW_REQUIRED.
- Names must include projectSlug and runSlug so multiple projects can run safely.
- Claude Code must never self-advance to another task.
- Codex must independently review Claude's report and local diff.
- Failed reviews may return to Claude only as a bounded report-only fix loop,
  with maxReportFixRounds set explicitly. Do not use the automatic loop for
  source code, schema, dependency, deployment, or architecture fixes.

First read:

- AGENTS.md or CLAUDE.md if present
- README.md
- architecture / design / operations docs if present
- package scripts or test-command documentation
- current git status

Then create or update:

- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

Use globally distinct names:

- projectSlug: lowercase kebab-case for this project
- runSlug: lowercase kebab-case for this finite batch
- runId: mode-b-<projectSlug>-<runSlug>
- automationName: mode-b-<projectSlug>-<runSlug>-monitor
- taskId: TASK-<PROJECTSLUG>-B01-T01-<SHORT-DESCRIPTION>

Create the first task as low risk:

- prefer read-only source sweep, docs-only inventory, guard inventory, or
  test-command inventory
- maxTasks = 1
- maxReportFixRounds = 2
- allowed files must be explicit
- forbidden scope must be explicit
- validation commands must be explicit
- report path must be explicit

Prepare:

1. The coordination files.
2. One bounded first task card.
3. A Codex heartbeat prompt for this exact run.
4. A short copy-paste Claude Code startup prompt for the owner.

Do not:

- start, stop, kill, or restart Claude Code
- deploy
- install dependencies
- call external APIs
- use secrets
- change schema or migrations
- commit, push, merge, or open a PR
- continue into business implementation

When finished, report:

- files created or changed
- projectSlug / runSlug / runId / automationName / taskId
- first task card path
- whether heartbeat automation was created or only proposed
- the short Claude Code startup prompt
- what owner authorization is required before running
```
