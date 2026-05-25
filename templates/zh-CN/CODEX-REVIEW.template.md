# <TASK-ID> Codex 审查

Verdict: `<ACCEPT | ACCEPT_WITH_NOTES | NEEDS_FIX | BLOCKED>`

## 已检查范围

- Task card:
- Claude report:
- 本地 diff:
- Validation:

## 发现

- `<填写问题，或 "无阻塞问题。">`

## 边界审查

- 仅修改 allowed files:
- 已遵守 stop lines:
- 未执行 owner-gated 动作:

## 验证摘要

```text
<命令与关键结果摘要>
```

## 下一状态

- `ACCEPT` / `ACCEPT_WITH_NOTES` 且有下一张已授权卡：发布下一张单卡 packet，置为 `READY_FOR_CLAUDE`
- `ACCEPT` / `ACCEPT_WITH_NOTES` 且无下一张卡：停在 `IDLE` 或 `OWNER_REVIEW_REQUIRED`
- `NEEDS_FIX` 且同一任务范围内且预算未耗尽：发布同一任务修复 packet，置为 `READY_FOR_CLAUDE`
- `NEEDS_FIX` 超范围或预算耗尽：`OWNER_DECISION_REQUIRED` 或 `BLOCKED`
