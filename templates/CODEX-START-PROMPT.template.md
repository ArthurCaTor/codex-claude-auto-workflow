# Codex Start Prompt

```text
Initialize Mode B in this project.

First learn the project rules and current git status. Do not implement business
features yet.

Use Mode B File Heartbeat:
- Codex plans and reviews.
- Claude Code executes one bounded task.
- messages.ndjson is append-only truth.
- state.json and BOARD.md are projections.
- every run is finite.
- names must include projectSlug and runSlug.
- report-only fix loops are bounded by maxReportFixRounds.

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

Stop after preparation and ask for owner authorization before the first run.
```

