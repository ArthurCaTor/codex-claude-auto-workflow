# <TASK-ID>

STATUS: READY_FOR_CLAUDE

## Goal

<One bounded outcome.>

## Finite Batch Boundary

- Project slug: `<projectSlug>`
- Run id: `mode-b-<projectSlug>-<runSlug>`
- Max autonomous tasks in this run: `1`
- Max report-only fix rounds inside this task: `2`
- Active task: `<TASK-ID>`
- No next task is pre-authorized.

## Authority

- Project instructions: `<path>`
- Current docs: `<paths>`

## Classification

- Layer:
- Module / area owner:
- Boundary type:
- Cross-module:
- Canonical truth ownership change:
- Docs sync required:
- Owner decision required:

## Allowed Files

Claude Code may edit only:

- `docs/operations/agent-coordination/reports/<TASK-ID>-claude-report.md`
- `docs/operations/agent-coordination/auto/BELL.json`
- `docs/operations/agent-coordination/auto/messages.ndjson`
- `docs/operations/agent-coordination/auto/state.json`
- `docs/operations/agent-coordination/auto/BOARD.md`

## Forbidden Scope

- Do not edit files outside allowed files.
- Do not advance to the next task.
- Do not deploy, install dependencies, call external APIs, use secrets, run
  operator-only steps, commit, push, merge, or open a PR.

## Validation

```text
<validation commands>
```

## Report

Write:

```text
docs/operations/agent-coordination/reports/<TASK-ID>-claude-report.md
```

After writing the report, append one `REPORT_READY` event and update
`BELL.json` and projections to `READY_FOR_CODEX_REVIEW` with
`holder = "codex"`.

## Bell Handoff

Before working, verify:

```json
{
  "holder": "claude",
  "status": "READY_FOR_CLAUDE",
  "taskId": "<TASK-ID>"
}
```

After reporting, hand the bell back:

```json
{
  "holder": "codex",
  "status": "READY_FOR_CODEX_REVIEW",
  "taskId": "<TASK-ID>"
}
```
