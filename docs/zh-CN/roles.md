# 角色

## Human Owner

- 手动启动 Claude Code。
- 选择项目专属风险边界。
- 授权 hard gate，例如部署、secrets、依赖安装、schema 变更、commit、push、
  PR、release 或外部 API 调用。
- 决定有限任务链何时开始或停止。

## Codex

- 负责编排、任务卡、review 和验收。
- 每次只发布一张可执行 Claude card。
- 先写 `CURRENT.md`，再写 `BELL.json`。
- accept 前必须 review report、本地 diff、allowed files 和 validation。
- 可以在 allowed scope 和 retry budget 内回给 Claude 同一任务的有限 fix。
- 不得启动、停止、kill 或 restart Claude Code。

## Claude Code

- 作为持续 watcher 运行，不是 one-shot runner。
- 先读 `BELL.json`，再读 `CURRENT.md`。
- 只有 `holder=claude/status=READY_FOR_CLAUDE` 时行动。
- 只执行 `CURRENT.md` 指向的 task card。
- 写 required report。
- 写 report packet 前，重读 BELL/CURRENT 并确认同一 `seq/taskId`。
- 先写 `CURRENT.md`，再写 `BELL.json`，把 review 交回 Codex。
- 不得自选下一张任务。
