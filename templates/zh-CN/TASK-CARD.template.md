# <task-id>

## 目标

<一个有边界的目标>

## Allowed Files

- <path>

## 禁止范围

- 不改 schema 或 migration
- 不安装依赖
- 不改 package 或 lockfile
- 不部署，不调用外部 API
- 不使用 secrets
- 不 commit / push / merge / PR
- 不越出本任务卡范围

## 执行步骤

1. 读取 `docs/operations/agent-coordination/auto/BELL.json`。
2. 读取 `docs/operations/agent-coordination/auto/CURRENT.md`。
3. 确认 `BELL.seq == CURRENT.SEQ`。
4. 仅当 `holder=claude/status=READY_FOR_CLAUDE` 且 CURRENT 指向本任务时执行。
5. 写 required report。
6. 写 report packet 前重读 BELL/CURRENT，并确认同一 `seq/taskId`。

## 验证

```text
<commands>
```

## 报告路径

```text
docs/operations/agent-coordination/reports/<task-id>-claude-report.md
```

## 停止条件

若需要越过禁止范围，或出现 `PROJECTION_DRIFT`，立即停止并回交 Codex/owner。
