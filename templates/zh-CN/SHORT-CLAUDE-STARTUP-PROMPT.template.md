# Short Claude Startup Prompt

```text
在 `<PROJECT_ROOT>` 启动
docs/operations/agent-coordination/auto/CLAUDE-WATCHER-PROMPT.md
里的持续 watcher；通信真相只读 BELL.json + CURRENT.md，要求
BELL.seq == CURRENT.SEQ；仅在 holder=claude/status=READY_FOR_CLAUDE 时执行，
且只执行 CURRENT.md 指向的当前任务卡；写回 report packet 前重读
BELL.json + CURRENT.md 确认仍是同一 seq/taskId，不一致则报告 PROJECTION_DRIFT；
等待时不写 heartbeat 文件，并持续 60 秒 watcher loop。
```
