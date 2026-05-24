# 状态机

允许状态：

```text
IDLE
READY_FOR_CLAUDE
CLAUDE_RUNNING
READY_FOR_CODEX_REVIEW
CODEX_REVIEWING
ACCEPTED
NEEDS_FIX
BLOCKED
OWNER_DECISION_REQUIRED
OWNER_REVIEW_REQUIRED
ERROR
```

正常路径：

```text
IDLE
-> holder=claude / READY_FOR_CLAUDE
-> holder=claude / CLAUDE_RUNNING
-> holder=codex  / READY_FOR_CODEX_REVIEW
-> holder=codex  / CODEX_REVIEWING
-> ACCEPTED
-> holder=codex or arthur / OWNER_REVIEW_REQUIRED
```

可选 report-only fix 路径：

```text
CODEX_REVIEWING
-> holder=claude / READY_FOR_CLAUDE  (same task, report-only fix, budgeted)
-> holder=claude / CLAUDE_RUNNING
-> holder=codex  / READY_FOR_CODEX_REVIEW
```

Manual recovery states：

```text
OWNER_REVIEW_REQUIRED
OWNER_DECISION_REQUIRED
BLOCKED
ERROR
```

只有 human owner 可以退出 manual recovery states。
