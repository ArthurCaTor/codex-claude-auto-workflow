# Workflow

CURRENT_ONLY_KISS_V1 coordinates one Codex session and one manually started
Claude Code watcher through two current files.

```text
BELL.json  = small machine turn signal
CURRENT.md = only active detailed communication packet
state.json = projection/debug
BOARD.md   = projection/debug
```

Historical logs, old cards, reports, reviews, terminal recaps, timestamps,
event ids, and file mtimes are legacy/debug only. They do not decide current
truth.

## Read Order

```text
1. Read BELL.json.
2. Read CURRENT.md.
3. Require BELL.json.seq == CURRENT.md SEQ.
4. Read the task card or report only if CURRENT.md names it.
```

If files are unreadable, malformed, partially written, missing required fields,
or seq-mismatched, report `PROJECTION_DRIFT` and do not act.

## Signals

```text
holder=claude / READY_FOR_CLAUDE
  Claude executes the one task named by CURRENT.md.

holder=codex / READY_FOR_CODEX_REVIEW
  Codex reviews report, diff, scope, and validation.

holder=codex / IDLE
  Codex may schedule exactly one safe bounded card.

holder=arthur or hard-gate status
  Stop for owner decision.
```

## Writer Order

Codex publishes task or fix packets:

```text
1. choose next seq = current BELL.seq + 1
2. overwrite CURRENT.md first
3. overwrite BELL.json second with the same seq
4. update state.json / BOARD.md only as projections
```

Claude reports completion:

```text
1. write the required report
2. reread BELL.json and CURRENT.md
3. confirm holder=claude/status=READY_FOR_CLAUDE and same seq/taskId
4. overwrite CURRENT.md first as REPORT_READY
5. overwrite BELL.json second as holder=codex / READY_FOR_CODEX_REVIEW
6. continue the 60-second watcher loop
```

## Finite Automation

Codex may publish only one task card at a time. If the owner provides a fixed
task chain, Codex converts that chain into sequential Claude cards and does not
regenerate the whole blueprint or let Claude self-select work.

`READY_FOR_CODEX_REVIEW` must be reviewed before any next card is published.
`NEEDS_FIX` may be returned to Claude only inside the same active task, allowed
files, and retry budget.
