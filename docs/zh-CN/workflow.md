# 工作流

Codex-Claude Auto Workflow 是一个自动化文件心跳工作流，用项目文件协调 Codex 和 Claude Code。

Codex 负责规划、任务边界、heartbeat 审查和验收。Claude Code 监听协作文件，执行唯一 active task card，并写回报告。Human owner 手动启动 Claude Code，并授权 owner-gated 动作。

## 协作文件

```text
docs/operations/agent-coordination/auto/
  README.md
  messages.ndjson
  state.json
  BOARD.md
```

## Truth 和 Projection

```text
messages.ndjson = append-only coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

如果文件之间不一致，以 `messages.ndjson` 为准。

## Message Log

`messages.ndjson` 中每一行都是一个 JSON object。

最小字段：

```json
{
  "id": "2026-01-01T00:00:00Z-codex-my-project-b01-task-ready",
  "from": "codex",
  "to": "claude",
  "projectSlug": "my-project",
  "runId": "mode-b-my-project-docs-sweep-b01",
  "taskId": "TASK-MY-PROJECT-B01-T01-DOCS-SWEEP",
  "type": "TASK_READY",
  "summary": "Task card ready.",
  "createdAt": "2026-01-01T00:00:00Z"
}
```

常见事件类型：

```text
INIT
TASK_READY
CLAUDE_STARTED
REPORT_READY
CODEX_REVIEW_STARTED
REVIEW_ACCEPTED
REVIEW_NEEDS_FIX
REVIEW_BLOCKED
OWNER_DECISION_REQUIRED
OWNER_REVIEW_REQUIRED
BOARD_UPDATED
HEARTBEAT
ERROR
```

## 写入规则

- 不要修改 `messages.ndjson` 的旧行。
- 如果需要修正，追加 correction event，不要改历史。
- 尽量用 temp file 加 atomic rename 更新 `state.json` 和 `BOARD.md`。
- 每个 actor 每次 heartbeat 最多追加一个 action event。

## 有限 Run 规则

每个 run 都声明：

```json
{
  "batch": {
    "enabled": true,
    "maxTasks": 1,
    "completedTasks": 0,
    "stopAfterMaxTasks": true,
    "terminalStateAfterBatch": "OWNER_REVIEW_REQUIRED"
  }
}
```

不授权无限 backlog。

## 自动化规则

这个工作流用于在一个有限 run 内形成低人工干预的自动流：

- Claude Code 可以持续监听当前 active task 的协作文件。
- Codex heartbeat 可以独立审查 Claude 报告和本地 diff。
- review 未通过时，只能在有限预算内回到 Claude 做 report-only 修复。
- 达到批次上限后，run 必须停在 `OWNER_REVIEW_REQUIRED`。

自动化不等于无人负责。schema 变更、依赖安装、部署、commit、push、secrets、外部 API 调用等高风险动作，都必须由 owner 显式授权。
