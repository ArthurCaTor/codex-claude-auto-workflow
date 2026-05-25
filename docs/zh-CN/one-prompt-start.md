# 一个 Prompt 启动

在目标项目的 Codex 中使用。

```text
在本项目中，用 CURRENT_ONLY_KISS_V1 初始化 Codex-Claude Auto Workflow。

不要立刻实现业务功能。先准备一个有限的 Codex 管理、Claude Code 执行的工作流。

规则：
- active communication truth 只有 BELL.json + CURRENT.md
- BELL.json 是极小机器信号
- CURRENT.md 是唯一当前详细通信包
- 任何一方行动前，BELL.seq 必须等于 CURRENT.SEQ
- state.json 和 BOARD.md 只是 projection/debug
- messages.ndjson、旧 task card、旧 report、旧 review、terminal recap、
  timestamp、event id、file mtime 都只是 legacy/debug
- 一个项目使用一个 Codex heartbeat thread
- Codex 先写 CURRENT.md，再写 BELL.json
- Claude 报告时先写 report，再重读 BELL/CURRENT 确认同一 seq/taskId，然后先写 CURRENT.md、再写 BELL.json
- Claude 是持续 60 秒 watcher，不是 one-shot runner
- Claude 不得自选下一张任务
- READY_FOR_CODEX_REVIEW 必须先 review，Codex 不能直接发下一张

创建或更新：
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/CURRENT.md
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/COMMUNICATION_ASCII_GATE.md
- docs/operations/agent-coordination/auto/CODEX-HEARTBEAT-PROMPT.md
- docs/operations/agent-coordination/auto/CLAUDE-WATCHER-PROMPT.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

初始化 reset-pending：
- holder=codex
- status=IDLE
- taskId=null
- payloadType=RESET_PENDING

不要启动/停止 Claude Code，不要部署、安装依赖、调用外部 API、使用 secrets、
修改 schema/migration、commit、push、merge、开 PR，或继续进入业务实现。

最后输出文件清单、当前 seq、holder/status/taskId、heartbeat 状态、验证结果，以及一段简短 Claude Code watcher prompt。
```
