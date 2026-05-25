# <task-id>

## Goal

<one bounded goal>

## Allowed Files

- <path>

## Forbidden Scope

- no schema or migrations
- no dependency installation
- no package or lockfile changes
- no deployment or external API calls
- no secrets
- no commit, push, merge, or PR
- no work outside this task card

## Instructions

1. Read `docs/operations/agent-coordination/auto/BELL.json`.
2. Read `docs/operations/agent-coordination/auto/CURRENT.md`.
3. Confirm `BELL.seq == CURRENT.SEQ`.
4. Execute only if `holder=claude/status=READY_FOR_CLAUDE` and CURRENT names
   this task.
5. Write the required report.
6. Before writing report packets, reread BELL/CURRENT and confirm the same
   `seq/taskId`.

## Validation

```text
<commands>
```

## Report Path

```text
docs/operations/agent-coordination/reports/<task-id>-claude-report.md
```

## Stop Conditions

Stop and return to Codex/owner if any forbidden scope is required or if
`PROJECTION_DRIFT` occurs.
