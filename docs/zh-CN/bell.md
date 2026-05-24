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

## 为什么做这次更新

早期 file-heartbeat 版本的信息其实够多，但“轮到谁”的信号不够显式。
Codex 或 Claude 可能看到了 report、heartbeat、recap 或 log line，却仍然对
谁该行动产生分歧。

Shared Bell Workflow v2 把系统拆成两条线路：

```text
Bell lane   = 简单、明确的交接信号
Detail lane = task 细节、report、review、validation、audit
```

Bell 线必须保持简单。如果一个 actor 需要另一个 actor 处理，就必须改写
`BELL.json`，给出明确的 `holder` 和 `status`。不要从自然语言 `summary`、
Claude report、Codex review 或 terminal recap 推断谁该干活。

## 系统图

```text
                         +------------------+
                         |      Arthur      |
                         | owner decisions  |
                         +---------+--------+
                                   |
                         holder=arthur
                                   |
                                   v
+-------------------+     +-------------------------------+     +-------------------+
| Claude Code       |     | shared repo coordination       |     | Codex             |
| executes one task |<--->|                               |<--->| plans and reviews |
+-------------------+     | Line A: BELL.json             |     +-------------------+
                          | - holder                      |
                          | - status                      |
                          | - task/report pointers        |
                          | - no detailed reasoning       |
                          |                               |
                          | Line B: detailed files        |
                          | - inbox/TASK-*.md             |
                          | - reports/*.md                |
                          | - codex-reviews/*.md          |
                          | - messages.ndjson audit log   |
                          | - state.json / BOARD.md       |
                          +-------------------------------+

规则：agent 可以通过 Line B 交流细节，但只能通过 Line A 判断轮到谁。
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
Codex 需要 Claude 干活
  Detail lane: 写 task card；append TASK_READY audit event
  Bell lane:   设置 holder=claude, status=READY_FOR_CLAUDE
  Result:      Claude 干活；Codex 等

Claude 需要 Codex review
  Detail lane: 写 report；append REPORT_READY audit event
  Bell lane:   设置 holder=codex, status=READY_FOR_CODEX_REVIEW
  Result:      Codex review；Claude watch

Codex 需要 Claude 修同一张任务
  Detail lane: 写 Codex review；append 带 NEEDS_FIX 的 TASK_READY
  Bell lane:   设置 holder=claude, status=READY_FOR_CLAUDE
  Result:      Claude 只修 active task 的 allowed files

任一方需要 Arthur
  Detail lane: 在 report/review/log 写明原因
  Bell lane:   设置 holder=arthur, status=OWNER_DECISION_REQUIRED
  Result:      双方停止推进

不要从 summary 文本推断下一个 actor。铃铛必须显式说明。
```

## 状态机

```text
+----------------------------------+
| holder=claude                    |
| status=READY_FOR_CLAUDE          |
| action: Claude executes task     |
+----------------+-----------------+
                 |
                 v
+----------------------------------+
| holder=claude                    |
| status=CLAUDE_RUNNING            |
| action: Claude continues task    |
+----------------+-----------------+
                 |
                 v
+----------------------------------+
| holder=codex                     |
| status=READY_FOR_CODEX_REVIEW    |
| action: Codex reviews report     |
+----+---------------+-------------+
     |               |
     | accept        | needs same-task fix
     v               v
+----------------+  +----------------------------------+
| batch done     |  | holder=claude                    |
| holder=codex   |  | status=READY_FOR_CLAUDE          |
| or arthur      |  | action: Claude fixes active task |
| OWNER_REVIEW   |  +----------------------------------+
+----------------+
     |
     | owner-only decision / blocker / error
     v
+----------------------------------+
| holder=arthur                    |
| OWNER_DECISION_REQUIRED/BLOCKED  |
| action: Arthur decides           |
+----------------------------------+
```

状态名可以承载细节，但轮到谁必须保持显式：下一个 actor 看 `holder`，
不是看 report、review 或 summary 里的自然语言。

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
