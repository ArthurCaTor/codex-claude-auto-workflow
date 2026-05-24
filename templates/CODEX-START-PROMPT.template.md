# Codex Start Prompt

This is the same startup flow as `docs/one-prompt-start.md`, kept here as a
template for copying into other projects.

```text
Initialize Codex-Claude Auto Workflow in this project.

Do not implement business features yet. First learn this project and prepare a
finite Codex-managed / Claude-Code-executed workflow.

Use these rules:
- Codex plans, manages, and reviews.
- Claude Code executes exactly one active bounded task card.
- The human owner manually starts Claude Code and authorizes owner-gated actions.
- The workflow should run as an automated low-touch file-heartbeat loop inside
  one finite batch.
- messages.ndjson is append-only coordination truth.
- state.json and BOARD.md are projections.
- every run is finite and stops at OWNER_REVIEW_REQUIRED.
- names must include projectSlug and runSlug.
- failed reviews may return to Claude only as a bounded report-only fix loop.

First read project instructions, README, architecture docs if present, package
scripts, and git status.

Create:
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

Generate one low-risk first task card, a Codex heartbeat prompt, and a short
copy-paste Claude Code startup prompt.

Do not start/stop Claude Code, deploy, install dependencies, call external APIs,
use secrets, change schema/migrations, commit, push, merge, or continue into
business implementation.

Stop after preparation and ask for owner authorization before the first run.
```
