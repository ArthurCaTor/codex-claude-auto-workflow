# Fixed Task Chain Prompts

Use these prompts when the owner already has a fixed task chain and wants Codex
to run it one Claude card at a time.

## Codex Prompt

```text
I have a fixed task chain to execute with CURRENT_ONLY_KISS_V1.

Act as the only orchestrator. Do not regenerate the whole blueprint. Do not let
Claude choose tasks. Convert the task chain I provide into sequential Claude
task cards and publish them through BELL.json + CURRENT.md one card at a time.

Read:
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/CURRENT.md
- the task chain below

First confirm BELL.json is parseable, CURRENT.md is readable, required fields
exist, and BELL.seq == CURRENT.md SEQ. If not, stop and report
PROJECTION_DRIFT.

Publish the first/next card only when holder=codex/status=IDLE.
If holder=codex/status=READY_FOR_CODEX_REVIEW, review Claude's report first.
If holder=claude, wait and do not overwrite.
If holder=arthur or a hard gate is touched, stop for owner.

For each card:
1. Publish exactly one executable Claude task card.
2. Overwrite CURRENT.md with SEQ = current BELL.seq + 1.
3. Overwrite BELL.json with the same seq.
4. Set holder=claude.
5. Set status=READY_FOR_CLAUDE.
6. Name taskId, taskCardPath, allowed files, forbidden scope, validation, and
   stop conditions.
7. After Claude reports, Codex reviews before deciding whether to publish the
   next card.

Do not use historical logs, old reports, terminal recaps, timestamps, event ids,
or file mtimes to decide the current state.

Task chain:
<paste task chain here>
```

## Claude Code Prompt

```text
Start the CURRENT_ONLY_KISS_V1 persistent watcher.

Active communication truth is only:
1. docs/operations/agent-coordination/auto/BELL.json
2. docs/operations/agent-coordination/auto/CURRENT.md

Every loop reads BELL.json first, then CURRENT.md, and confirms:
BELL.seq == CURRENT.md SEQ

If files are unreadable, malformed, partially written, missing required fields,
or seq-mismatched, report PROJECTION_DRIFT, do not execute, and reread after 60
seconds.

Only when holder=claude/status=READY_FOR_CLAUDE, execute the current task card
named by CURRENT.md. Execute only that one card. Do not select the next card.

When finished:
1. Write the report.
2. Before writing back, reread BELL.json and CURRENT.md and confirm you still
   hold the same seq/taskId with holder=claude/status=READY_FOR_CLAUDE.
3. If the check fails, report PROJECTION_DRIFT and do not overwrite CURRENT.md
   or BELL.json.
4. Overwrite CURRENT.md as a report packet with SEQ = old seq + 1.
5. Overwrite BELL.json with the same seq, holder=codex, status=READY_FOR_CODEX_REVIEW.
6. Continue the 60-second watcher.
7. Do not write heartbeat files while waiting.
```
