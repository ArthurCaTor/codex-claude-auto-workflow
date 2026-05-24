# Mode B Board

STATUS: READY_FOR_CLAUDE

## Current State

- Project slug: `<projectSlug>`
- Run: `mode-b-<projectSlug>-<runSlug>`
- Active task: `<task-id>`
- Bell holder: `claude`
- Batch task: `1 / 1`
- Report-only fix loop: `0 / 2`
- Automation: `mode-b-<projectSlug>-<runSlug>-monitor`
- Next expected actor: Claude Code

## Stop Lines

- schema or migration work
- dependency installation
- deployment
- external API calls
- production secrets
- commit or push
- owner-only decisions
- Claude process control

## Bell Rule

- `holder = "claude"`：Claude Code 干活。
- `holder = "codex"`：Codex review 或准备下一个有限任务。
- `holder = "arthur"`：需要 owner 决策。
