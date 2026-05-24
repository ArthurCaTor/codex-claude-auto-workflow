# Heartbeat

## Codex Heartbeat

每次 heartbeat，Codex：

1. 先读 `messages.ndjson`。
2. 对齐 `state.json`。
3. 读取 `BOARD.md`。
4. 如果 status 是 `READY_FOR_CODEX_REVIEW`，读取 Claude report 和 local diff。
5. 验证 allowed files 和 stop lines。
6. 运行安全 validation。
7. 写 Codex review。
8. 追加一个 Codex event。
9. 更新 projections。
10. 停下，或向 owner 报告状态。

## Claude Watcher

Claude Code：

1. 读取 `messages.ndjson`，然后读取 `state.json` 和 `BOARD.md`。
2. 如果 status 不是 `READY_FOR_CLAUDE`，等待并重新读取。
3. 如果 status 是 `READY_FOR_CLAUDE`，读取 active task card。
4. 只执行这个 task card。
5. 写 report。
6. 追加一个 `REPORT_READY`、`OWNER_DECISION_REQUIRED`、`BLOCKED` 或 `ERROR` event。
7. 把 projections 更新到 `READY_FOR_CODEX_REVIEW`。
8. 等待 Codex review。
