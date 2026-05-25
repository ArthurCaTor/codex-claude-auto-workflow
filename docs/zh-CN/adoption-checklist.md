# 迁移检查清单

- [ ] 替换 `<PROJECT_ROOT>`、`<PROJECT_NAME>`、`<PROJECT_SLUG>`、
      `<RUN_ID>`、`<MONITOR_ID>` 和所有 task/report/review 路径。
- [ ] 创建 `auto/BELL.json`。
- [ ] 创建 `auto/CURRENT.md`。
- [ ] 创建 `auto/state.json`。
- [ ] 创建 `auto/BOARD.md`。
- [ ] 创建 `auto/README.md`。
- [ ] 创建 `auto/COMMUNICATION_ASCII_GATE.md`。
- [ ] 创建 Codex 和 Claude prompt 文件。
- [ ] 不为 active turn truth 创建 `messages.ndjson`。
- [ ] 初始化 reset-pending：`holder=codex/status=IDLE/taskId=null`。
- [ ] 确认 `BELL.seq == CURRENT.md SEQ`。
- [ ] 确认 Claude watcher 使用强制 60 秒循环。
- [ ] 确认等待中的 Claude 不写 idle heartbeat 文件。
- [ ] 确认 Claude 写 report packet 前会重读并确认同一 `seq/taskId`。
- [ ] 确认 Codex 在 `READY_FOR_CODEX_REVIEW` 时先 review，再调度。
- [ ] 确认一个项目只使用一个 Codex heartbeat thread。
- [ ] 确认没有其他项目名残留。
- [ ] 运行 `git diff --check`。
