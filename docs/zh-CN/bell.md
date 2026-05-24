# Shared Bell Workflow v2

`BELL.json` 是一个 Codex session 搭配一个 Claude Code session 时使用的共享铃铛。

它只回答一个问题：

```text
现在轮到谁行动？
```

其他细节放在对应文件里：

```text
task card     = Claude 要做什么
Claude report = Claude 做了什么
Codex review  = Codex 是否接受
messages log  = append-only audit trail
```

## 系统图

```text
              +------------------+
              |     Arthur       |
              | starts Claude    |
              | authorizes gates |
              +---------+--------+
                        |
                        v
+-----------------------+--------------------------------+
| docs/operations/agent-coordination/                    |
|                                                        |
| auto/BELL.json       -> shared turn signal             |
| auto/messages.ndjson -> append-only audit truth        |
| auto/state.json      -> machine projection             |
| auto/BOARD.md        -> human projection               |
| inbox/TASK-*.md      -> detailed task instructions     |
| reports/*.md         -> Claude detailed reports        |
| codex-reviews/*.md   -> Codex detailed reviews         |
+-------------+---------------------------+--------------+
              |                           |
              v                           v
      +-------+--------+          +-------+--------+
      | Claude Code    |          | Codex          |
      | executes task  |          | reviews/plans  |
      +----------------+          +----------------+
```

## Bell 字段

```json
{
  "version": 1,
  "holder": "claude",
  "status": "READY_FOR_CLAUDE",
  "runId": "mode-b-my-project-docs-sweep-b01",
  "taskId": "TASK-MY-PROJECT-B01-T01-DOCS-SWEEP",
  "taskCardPath": "docs/operations/agent-coordination/inbox/TASK-MY-PROJECT-B01-T01-DOCS-SWEEP.md",
  "reportPath": "docs/operations/agent-coordination/reports/TASK-MY-PROJECT-B01-T01-DOCS-SWEEP-claude-report.md",
  "waitingFor": "claude_execution",
  "lastEventId": "2026-01-01T00:00:01Z-codex-task-ready",
  "updatedAt": "2026-01-01T00:00:01Z",
  "summary": "Codex handed the active bounded task to Claude Code."
}
```

## Holder 规则

```text
holder=claude -> Claude Code 干活；Codex 等。
holder=codex  -> Codex review、closeout，或准备下一个有限任务。
holder=arthur -> Arthur 决策；两个 agent 都不推进。
```

允许的 holder/status 组合：

```text
claude + READY_FOR_CLAUDE
claude + CLAUDE_RUNNING
codex  + READY_FOR_CODEX_REVIEW
codex  + CODEX_REVIEWING
codex  + OWNER_REVIEW_REQUIRED when delegated continuation is recorded
arthur + OWNER_REVIEW_REQUIRED when not delegated
arthur + OWNER_DECISION_REQUIRED
arthur + BLOCKED
arthur + ERROR
```

## 数据流

```text
Codex 写 task card
  -> Codex append TASK_READY 到 messages.ndjson
  -> Codex 写 BELL holder=claude
  -> Claude 读 BELL，再校验 state/log
  -> Claude 执行 active task
  -> Claude 写 report
  -> Claude append REPORT_READY 到 messages.ndjson
  -> Claude 写 BELL holder=codex
  -> Codex 读 BELL、report、diff 和 log
  -> Codex 写 review
  -> Codex accept、退回有限 fix、block，或把新任务交给 Claude
```

## 状态机

```text
holder=claude
READY_FOR_CLAUDE
  |
  v
holder=claude
CLAUDE_RUNNING
  |
  v
holder=codex
READY_FOR_CODEX_REVIEW
  |
  +--> ACCEPTED -> OWNER_REVIEW_REQUIRED or next READY_FOR_CLAUDE
  |
  +--> NEEDS_FIX -> holder=claude / READY_FOR_CLAUDE same task
  |
  +--> BLOCKED or OWNER_DECISION_REQUIRED -> holder=arthur
```

## 两条线路规则

保持两条线路分离：

```text
Line A: BELL.json
  很小的 latest-turn projection，用来避免错过信号。

Line B: task/report/review/log files
  详细指令、证据、验证、review 和审计历史。
```

不要把详细实现指令塞进 `BELL.json`。实现细节放 task card。不要把长报告证据塞进 `BELL.json`。报告细节放 Claude report。

## 冲突规则

读取顺序：

```text
1. BELL.json
2. messages.ndjson
3. state.json
4. BOARD.md
5. active task card or report as needed
```

`BELL.json`、`state.json` 和 `BOARD.md` 都是 projection。如果它们和 `messages.ndjson` 冲突，先停止并 resync，再行动。不要在 repo 文件冲突时相信 terminal recap。

## 禁止的信号文件

不要创建角色专属 signal 文件，例如：

```text
CLAUDE_SIGNAL.json
CODEX_SIGNAL.json
NEXT_TASK.json
LATEST_STATUS.json
```

一个 Codex session 配一个 Claude Code session 时，一个共享铃铛就够了。
