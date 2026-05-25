# 状态机

CURRENT_ONLY_KISS_V1 把 active state 保持得很小。

```text
RESET_PENDING / IDLE
  Codex 可以发布一张 TASK_READY packet。

READY_FOR_CLAUDE
  Claude 可以执行 CURRENT.md 指向的一张 task。

READY_FOR_CODEX_REVIEW
  Codex 必须先 review，不能直接发下一张。

NEEDS_FIX
  Codex 只能在同一 active task、allowed files 和 retry budget 内回给 Claude。

OWNER_DECISION_REQUIRED / BLOCKED
  停下等待 owner。
```

正常流：

```text
codex/IDLE
  -> Codex 写 CURRENT TASK_READY
  -> Codex 写 BELL holder=claude/READY_FOR_CLAUDE
  -> Claude 执行一张卡
  -> Claude 写 report
  -> Claude 重读确认同一 seq/taskId
  -> Claude 写 CURRENT REPORT_READY
  -> Claude 写 BELL holder=codex/READY_FOR_CODEX_REVIEW
  -> Codex review
  -> accept、bounded fix、下一张单卡、或 owner stop
```

没有无限 backlog。Claude 永远不能自推进。
