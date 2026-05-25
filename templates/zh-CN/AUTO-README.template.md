# Mode B Current-Only Coordination Protocol

STATUS: ACTIVE for `<projectName>` after placeholder replacement.

## Purpose

本目录协调一个 Codex session 和一个手动启动的 Claude Code session。Codex 不得启动、停止、kill 或 restart Claude Code。

Protocol: `CURRENT_ONLY_KISS_V1`.

## Active Truth

```text
BELL.json  = small machine turn signal and seq pointer
CURRENT.md = only active detailed communication packet
state.json = machine-readable projection
BOARD.md   = human-readable projection
```

`messages.ndjson`、旧 task card、旧 report、旧 review、terminal recap、
timestamp、event id、file mtime 都只是 legacy/debug/audit context，不能决定当前 turn order、freshness 或 latest task。

Freshness 只看：

```text
BELL.json.seq == CURRENT.md SEQ
```

任一文件不可读、疑似半写、格式错误、缺必需字段、或 seq 不一致时，双方都不能行动。

## Signals

```text
CLAUDE_WORK       = holder=claude / READY_FOR_CLAUDE / seq matches
CODEX_REVIEW      = holder=codex / READY_FOR_CODEX_REVIEW / seq matches
CODEX_SCHEDULE    = holder=codex / IDLE / seq matches
OWNER_OR_BLOCKED  = holder=arthur or hard-gate status
```

## Writer Order

Codex 发布 task/review/fix packet：

```text
1. next seq = current BELL.seq + 1
2. 覆盖 CURRENT.md
3. 覆盖 BELL.json，使用同一个 seq 和 holder/status/taskId
4. state.json / BOARD.md 只作为 projection 更新
```

Claude 报告：

```text
1. 写 required report
2. 重读 BELL.json 和 CURRENT.md
3. 确认同一 seq/taskId 且 holder=claude/status=READY_FOR_CLAUDE
4. 覆盖 CURRENT.md 为 next-seq REPORT_READY
5. 覆盖 BELL.json 为同一个 seq，holder=codex，status=READY_FOR_CODEX_REVIEW
6. 继续 60 秒 watcher
```
