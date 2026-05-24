# <TASK-ID>

STATUS: READY_FOR_CLAUDE

## Goal

<一个有边界的结果。>

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

Claude Code 只能编辑：

- `docs/operations/agent-coordination/reports/<TASK-ID>-claude-report.md`
- `docs/operations/agent-coordination/auto/BELL.json`
- `docs/operations/agent-coordination/auto/messages.ndjson`
- `docs/operations/agent-coordination/auto/state.json`
- `docs/operations/agent-coordination/auto/BOARD.md`

## Forbidden Scope

- 不要编辑 allowed files 之外的文件。
- 不要推进到下一个 task。
- 不要 deploy、install dependencies、call external APIs、use secrets、run operator-only steps、commit、push、merge 或 open a PR。

## Validation

```text
<validation commands>
```

## Report

写入：

```text
docs/operations/agent-coordination/reports/<TASK-ID>-claude-report.md
```

写完 report 后，追加一个 `REPORT_READY` event，并把 `BELL.json` 和
projections 更新到 `READY_FOR_CODEX_REVIEW`，`holder = "codex"`。

## Bell Handoff

开始前确认：

```json
{
  "holder": "claude",
  "status": "READY_FOR_CLAUDE",
  "taskId": "<TASK-ID>"
}
```

写完 report 后，把铃铛交回：

```json
{
  "holder": "codex",
  "status": "READY_FOR_CODEX_REVIEW",
  "taskId": "<TASK-ID>"
}
```
