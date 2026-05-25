# Current Coordination Packet

SEQ: 1
PROTOCOL: CURRENT_ONLY_KISS_V1
RUN_ID: <runId>
TASK_ID: null
HOLDER: codex
STATUS: IDLE
PAYLOAD_TYPE: RESET_PENDING
TASK_CARD: null
REPORT_PATH: null

## 当前真相

这是 `<projectName>` 唯一当前详细通信包。

当前没有任何 Claude task 可执行。除非 Codex 在本文件发布新的 TASK_READY packet
并用同一个 seq 更新 BELL.json，否则旧任务状态和历史 task card 都不能参与当前执行。

## Freshness Rule

任何一方行动前，BELL.json.seq 必须等于本 packet 的 SEQ。

不要用 messages.ndjson、timestamp、file mtime、旧 task card、旧 report、旧 review
或 terminal recap 判断 freshness。
