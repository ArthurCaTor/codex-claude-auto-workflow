# Report-Only Fix Loop

Mode B can automatically return a failed Codex review to Claude Code only when
the problem is report-only.

## Allowed Automatic Fixes

Examples:

- missing validation summary in the Claude report
- unclear file inventory
- missing risk section
- wrong projection state after report
- `BOARD.md` not updated after `REPORT_READY`
- report formatting problem

## Forbidden Automatic Fixes

Stop for owner review if the fix requires:

- source code edits
- tests
- schema or migrations
- architecture docs
- business docs
- dependency installation
- deployment
- external API calls
- secrets
- commits or pushes
- owner judgment

## State Field

```json
{
  "reportFixLoop": {
    "enabled": true,
    "maxReportFixRounds": 2,
    "currentReportFixRound": 0,
    "stopWhenBudgetExhausted": true,
    "scope": "same-active-task-report-and-coordination-files-only"
  }
}
```

When the budget is exhausted, stop at `OWNER_REVIEW_REQUIRED`,
`OWNER_DECISION_REQUIRED`, or `BLOCKED`.

