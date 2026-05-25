# 既定任务链 Prompts

当 owner 已经有一条确定任务链，希望 Codex 按 CURRENT_ONLY_KISS_V1 逐张 Claude
card 执行时，使用这些 prompt。

## Codex Prompt

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

逐卡规则：
1. 每次只发布一张可执行 Claude task card。
2. 覆盖写 CURRENT.md，SEQ = 当前 BELL.seq + 1。
3. 覆盖写 BELL.json，使用同一个 seq。
4. 设置 holder=claude。
5. 设置 status=READY_FOR_CLAUDE。
6. 明确 taskId、taskCardPath、allowed files、forbidden scope、validation、stop conditions。
7. Claude 完成后由 Codex review，再决定是否发布下一张。

不要使用历史日志、旧 report、terminal recap、timestamp、event id、mtime 判断当前状态。

任务链如下：
<在这里粘贴任务链>
```

## Claude Code Prompt

```text
启动 CURRENT_ONLY_KISS_V1 persistent watcher。

通信真相只看：
1. docs/operations/agent-coordination/auto/BELL.json
2. docs/operations/agent-coordination/auto/CURRENT.md

每轮先读 BELL.json，再读 CURRENT.md，并确认：
BELL.seq == CURRENT.md 的 SEQ

如果文件不可读、格式错误、疑似半写、缺必需字段、或 seq 不一致，报告 PROJECTION_DRIFT，不执行任务，60 秒后重读。

只有当 holder=claude 且 status=READY_FOR_CLAUDE 时，执行 CURRENT.md 指向的当前 task card。只执行这一张，不自选下一张。

完成后：
1. 写 report。
2. 写回前重新读取 BELL.json 和 CURRENT.md，确认仍是自己刚执行的同一个 seq/taskId，且 holder=claude、status=READY_FOR_CLAUDE。
3. 如果确认失败，报告 PROJECTION_DRIFT，不覆盖 CURRENT.md 或 BELL.json，继续 60 秒 watcher。
4. 覆盖写 CURRENT.md 为 report packet，SEQ = 旧 seq + 1。
5. 覆盖写 BELL.json 为同一个 seq，holder=codex，status=READY_FOR_CODEX_REVIEW。
6. 继续 60 秒 watcher。
7. 等待时不要写 heartbeat 文件。
```
