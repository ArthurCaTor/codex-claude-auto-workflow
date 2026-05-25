# Mode B Current-Only Coordination Protocol

STATUS: ACTIVE for `<projectName>` after placeholder replacement.

## Purpose

This directory coordinates one Codex session and one manually started Claude
Code session. Codex must not start, stop, kill, or restart Claude Code.

Protocol: `CURRENT_ONLY_KISS_V1`.

## Active Truth

```text
BELL.json  = small machine turn signal and seq pointer
CURRENT.md = only active detailed communication packet
state.json = machine-readable projection
BOARD.md   = human-readable projection
```

`messages.ndjson`, old task cards, old reports, old reviews, terminal recaps,
timestamps, event ids, and file mtimes are legacy/debug/audit context only.
They must not decide current turn order, freshness, or latest task.

## Read Order

```text
1. BELL.json
2. CURRENT.md
3. state.json or BOARD.md only as projection/debug if needed
4. active task card or report only if named by CURRENT.md
```

Freshness is valid only when:

```text
BELL.json.seq == CURRENT.md SEQ
```

If either file is unreadable, partially written, malformed, missing required
fields, or seq-mismatched, neither agent may act.

## Signals

```text
CLAUDE_WORK       = holder=claude / READY_FOR_CLAUDE / seq matches
CODEX_REVIEW      = holder=codex / READY_FOR_CODEX_REVIEW / seq matches
CODEX_SCHEDULE    = holder=codex / IDLE / seq matches
OWNER_OR_BLOCKED  = holder=arthur or hard-gate status
```

## Writer Order

Codex publishes task/review/fix packets:

```text
1. choose next seq = current BELL.seq + 1
2. overwrite CURRENT.md with seq N
3. overwrite BELL.json with seq N and holder/status/taskId
4. update state.json and BOARD.md as projections
```

Claude reports:

```text
1. write the required report
2. reread BELL.json and CURRENT.md
3. confirm same seq/taskId and holder=claude/status=READY_FOR_CLAUDE
4. overwrite CURRENT.md with next-seq REPORT_READY
5. overwrite BELL.json with same seq, holder=codex, status=READY_FOR_CODEX_REVIEW
6. continue the hard 60-second watcher loop
```
