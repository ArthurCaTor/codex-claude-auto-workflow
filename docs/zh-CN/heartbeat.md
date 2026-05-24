# Heartbeat

## Codex Heartbeat

每次 heartbeat，Codex：

1. 先读 `BELL.json`。
2. 用 `messages.ndjson` 和 `state.json` 校验 bell。
3. 必要时对齐 projections。
4. 读取 `BOARD.md`。
5. 如果 `holder = "codex"` 且 status 是 `READY_FOR_CODEX_REVIEW`，读取 Claude report 和 local diff。
6. 验证 allowed files 和 stop lines。
7. 运行安全 validation。
8. 写 Codex review。
9. 追加一个 Codex event。
10. 更新 `BELL.json`、`state.json` 和 `BOARD.md`。
11. 停下，或向 owner 报告状态。

## Claude Watcher

Claude Code：

1. 读取 `BELL.json`，再读取 `messages.ndjson`、`state.json` 和 `BOARD.md`。
2. 如果 `holder` 不是 `claude`，等待并重新读取。
3. 如果 `holder = "claude"` 且 status 是 `READY_FOR_CLAUDE`，读取 active task card。
4. 只执行这个 task card。
5. 写 report。
6. 追加一个 `REPORT_READY`、`OWNER_DECISION_REQUIRED`、`BLOCKED` 或 `ERROR` event。
7. 把 `BELL.json` 和 projections 更新到 `READY_FOR_CODEX_REVIEW`，并设置 `holder = "codex"`。
8. 等待 Codex review。
