# 铃铛与当前通信包

CURRENT_ONLY_KISS_V1 故意把信号和细节分开。

```text
铃铛线   : BELL.json 说明现在轮到谁。
细节线   : CURRENT.md 说明当前通信包是什么。
```

## 最小 Bell

```json
{
  "version": 1,
  "seq": 1,
  "protocol": "CURRENT_ONLY_KISS_V1",
  "projectSlug": "my-project",
  "holder": "codex",
  "status": "IDLE",
  "runId": "mode-b-my-project-reset-pending",
  "taskId": null,
  "payloadType": "RESET_PENDING",
  "taskCardPath": null,
  "reportPath": null,
  "currentPath": "docs/operations/agent-coordination/auto/CURRENT.md",
  "waitingFor": "codex_schedule",
  "packetId": "seq-1-codex-reset-pending",
  "updatedAt": "debug-only",
  "summary": "No Claude task is executable until Codex publishes a fresh packet."
}
```

`updatedAt` 和 `packetId` 都只是 debug 字段，不能决定 freshness。

## Freshness Gate

```text
BELL.json.seq == CURRENT.md SEQ
```

这是唯一 freshness 规则。不要用 timestamp、event id、file mtime、terminal recap 或历史日志判断当前轮次。

## 状态机

```text
                 +----------------------+
                 |  Codex heartbeat     |
                 +----------+-----------+
                            |
                            v
                 read BELL.json first
                 read CURRENT.md second
                            |
                            v
              BELL.seq == CURRENT.SEQ ?
                    | yes        | no
                    v            v
             derive signal     PROJECTION_DRIFT
```

信号：

```text
holder=claude / READY_FOR_CLAUDE
  Claude 持有执行权。

holder=codex / READY_FOR_CODEX_REVIEW
  Codex 持有 review 权。

holder=codex / IDLE
  Codex 持有调度权。

holder=arthur 或 hard gate
  Owner 持有决策权。
```

## Drift

以下都视为 `PROJECTION_DRIFT`：

- `BELL.json` 不可读
- `CURRENT.md` 不可读
- JSON 格式错误
- 缺必需字段
- 文件疑似半写
- `BELL.seq != CURRENT.SEQ`
- Claude 准备写 report，但已经不再持有同一个 `seq/taskId`

发生 drift 时不要猜。等一个 poll interval 后重读，或请求 owner resync。
