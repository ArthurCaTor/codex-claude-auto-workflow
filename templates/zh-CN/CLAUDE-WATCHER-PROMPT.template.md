# Claude Current-Only Watcher Prompt

在 `<PROJECT_ROOT>` 启动持续 watcher。

这不是 one-shot 任务。写完 report 后不要退出，除非 Codex 明确说
`no watch needed / exit watch`。

只读取当前通信真相：

```text
docs/operations/agent-coordination/auto/BELL.json
docs/operations/agent-coordination/auto/CURRENT.md
```

每轮开始时：

1. 读取 `BELL.json`。
2. 读取 `CURRENT.md`。
3. 确认 `BELL.json.seq == CURRENT.md SEQ`。
4. 如果任一文件不可读、格式错误、疑似半写、缺必需字段或 seq 不一致，报告
   `PROJECTION_DRIFT`，不要行动，等待 60 秒后重读。

仅在以下条件满足时执行：

```text
BELL.json.holder = claude
BELL.json.status = READY_FOR_CLAUDE
```

只执行 `CURRENT.md` 指向的当前任务，不自选下一张任务卡。

完成后：

1. 写 required report。
2. 覆盖 `CURRENT.md` 或 `BELL.json` 前，先重读 `BELL.json` 再重读 `CURRENT.md`。
   确认仍是同一 `seq/taskId`，且 `holder=claude/status=READY_FOR_CLAUDE`。
3. 若不一致，报告 `PROJECTION_DRIFT`，不要覆盖文件，继续 60 秒 watcher。
4. 先覆盖 `CURRENT.md` 为 next-seq `REPORT_READY` packet。
5. 再覆盖 `BELL.json` 为同一个 seq，`holder=codex`，
   `status=READY_FOR_CODEX_REVIEW`。
6. 持续保持 60 秒 watcher，等待时不写 heartbeat 文件。
