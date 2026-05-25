# Codex Start Prompt

```text
在本项目中用 CURRENT_ONLY_KISS_V1 初始化 Codex-Claude Auto Workflow。

不要立刻实现业务功能。

先读取项目 instructions、当前 docs 和 git status，然后在
docs/operations/agent-coordination/auto/ 下创建或更新本地协作文件。

规则：
- active communication truth 只有 BELL.json + CURRENT.md
- 任何人行动前 BELL.seq 必须等于 CURRENT.md SEQ
- BELL.json 是极小机器信号
- CURRENT.md 是唯一当前详细通信包
- state.json 和 BOARD.md 只是 projections/debug
- messages.ndjson、旧 task card、旧 report、旧 review、terminal recap、
  timestamp、event id、file mtime 都只是 legacy/debug
- 文件不可读、半写、缺字段或 seq 不一致时，报告 PROJECTION_DRIFT，不行动
- Codex 发布任务时先写 CURRENT.md，再写 BELL.json
- Claude 报告时先写 report，再重读 BELL/CURRENT 确认同一 seq/taskId，然后先写 CURRENT.md、再写 BELL.json
- Claude 是持续 60 秒 watcher，不自选下一张 task
- READY_FOR_CODEX_REVIEW 必须先 review，Codex 不能直接发布下一张

初始化 reset-pending：holder=codex/status=IDLE/taskId=null。

最后输出文件清单、当前 seq、holder/status/taskId、heartbeat 状态、验证结果，以及一段简短 Claude Code watcher prompt。
```
