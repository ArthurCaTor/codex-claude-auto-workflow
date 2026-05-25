# CURRENT_ONLY_KISS_V1 Task Chain Prompts

```text
我有一条已确定的任务链，要使用 CURRENT_ONLY_KISS_V1 自动交流方式执行。

请作为唯一编排者，不重新生成整套蓝图，不让 Claude 自选任务。你只负责把我提供的任务链转换成逐张 Claude task card，并通过 BELL.json + CURRENT.md 逐卡发布。

先读取：
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/CURRENT.md
- 我下面提供的任务链

先确认 BELL.json 可解析、CURRENT.md 可读取、必需字段存在，并且 BELL.seq == CURRENT.md 的 SEQ。若不一致、不可读、疑似半写、缺字段，停止并报告 PROJECTION_DRIFT。

只有在 holder=codex 且 status=IDLE 时，才发布第一张/下一张任务卡。
如果 holder=codex 且 status=READY_FOR_CODEX_REVIEW，先 review Claude report，不要发布新卡。
如果 holder=claude，等待 Claude，不要覆盖当前任务。
如果 holder=arthur 或触发 hard gate，停止等待 owner。

任务链如下：
<在这里粘贴任务链>
```

```text
启动 CURRENT_ONLY_KISS_V1 persistent watcher。

通信真相只看 BELL.json + CURRENT.md。确认 BELL.seq == CURRENT.md 的 SEQ。
只有 holder=claude/status=READY_FOR_CLAUDE 时执行 CURRENT.md 指向的当前 task card。

完成后先重读 BELL.json 和 CURRENT.md，确认仍持有同一 seq/taskId。如果不一致，报告 PROJECTION_DRIFT，不覆盖文件。然后写 CURRENT.md report packet，把 BELL.json 交回 codex，并继续 60 秒 watcher。
```
