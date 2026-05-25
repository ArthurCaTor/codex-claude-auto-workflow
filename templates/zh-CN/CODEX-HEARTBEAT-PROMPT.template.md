# Codex Current-Only Monitor Prompt

这个 heartbeat 只服务 `<PROJECT_ROOT>`。不要读取、调度、审查或修复其他项目
的协作文件。不要在一个 heartbeat 中混合多个项目。
如果当前 Codex thread 已有其他项目 heartbeat 运行，本项目 monitor 必须保持
`PAUSED`，并改用独立 Codex thread。

严格使用 `CURRENT_ONLY_KISS_V1`。每次唤醒：

1. 先读 `docs/operations/agent-coordination/auto/BELL.json`。
2. 再读 `docs/operations/agent-coordination/auto/CURRENT.md`。
3. 行动前必须满足 `BELL.json.seq == CURRENT.md SEQ`。
4. 若任一文件不可读、格式错误、疑似半写、缺必需字段或 seq 不一致，报告
   `PROJECTION_DRIFT` 并等待下一轮。
5. 只把 `BELL.json + CURRENT.md` 视为 active communication truth。
6. 推导当前信号：
   - `CLAUDE_WORK`: `holder=claude` 且 `status=READY_FOR_CLAUDE`
   - `CODEX_REVIEW`: `holder=codex` 且 `status=READY_FOR_CODEX_REVIEW`
   - `CODEX_SCHEDULE`: `holder=codex` 且 `status=IDLE`
   - `OWNER_OR_BLOCKED`: `holder=arthur` 或 hard-gate 状态
7. `CODEX_REVIEW` 是 review gate，不是调度捷径。
8. 只有 review 通过后，才能发布下一张卡。

禁止启动、停止、kill 或重启 Claude Code。除非 owner 显式授权，不得跨越 owner gate。
