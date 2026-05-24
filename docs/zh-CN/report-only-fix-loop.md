# Report-Only Fix Loop

只有当问题是 report-only 时，Mode B 才能把未通过的 Codex review 自动回给 Claude Code。

## 允许自动修复

例子：

- Claude report 缺少 validation summary
- file inventory 不清楚
- 缺少 risk section
- report 后 projection state 错误
- `REPORT_READY` 之后没有更新 `BOARD.md`
- report formatting problem

## 禁止自动修复

如果修复需要以下动作，必须停下等待 owner review：

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

预算耗尽后，停在 `OWNER_REVIEW_REQUIRED`、`OWNER_DECISION_REQUIRED` 或 `BLOCKED`。
