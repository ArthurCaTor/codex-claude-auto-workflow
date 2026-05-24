# 命名规则

多个 Mode B automation 可能同时运行。名称必须全局唯一。

## Slugs

```text
projectSlug = lowercase kebab-case project name
runSlug     = lowercase kebab-case bounded run name
batchId     = B01, B02, B03...
taskSeq     = T01, T02, T03...
```

## Automation Names

```text
mode-b-<projectSlug>-<runSlug>-monitor
```

好的例子：

```text
mode-b-agent-galaxy-t3-dev-b01-monitor
mode-b-finance-dashboard-test-inventory-b01-monitor
```

坏的例子：

```text
mode-b-monitor
mode-b-batch-01-monitor
claude-monitor
latest-monitor
```

## Run IDs

```text
mode-b-<projectSlug>-<runSlug>
```

## Task IDs

```text
TASK-<PROJECTSLUG>-<BATCH>-<SEQ>-<SHORT-DESCRIPTION>
```

例子：

```text
TASK-FINANCE-DASHBOARD-B01-T01-TEST-COMMAND-INVENTORY
```

## Report And Review Paths

```text
docs/operations/agent-coordination/reports/<TASK-ID>-claude-report.md
docs/operations/agent-coordination/codex-reviews/<TASK-ID>-codex-review.md
```
