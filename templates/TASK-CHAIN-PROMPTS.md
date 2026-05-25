# CURRENT_ONLY_KISS_V1 Task Chain Prompts

Use this file when a project already has a fixed task chain and the owner wants
Codex to execute it through CURRENT_ONLY_KISS_V1 one Claude card at a time.

## Codex Prompt

```text
I have a fixed task chain to execute with CURRENT_ONLY_KISS_V1.

Act as the only orchestrator. Do not regenerate the whole blueprint. Do not let
Claude choose tasks. Convert my task chain into sequential Claude task cards
and publish only one executable card at a time through BELL.json + CURRENT.md.

First read:
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/CURRENT.md
- the task chain below

Confirm BELL.json is parseable, CURRENT.md is readable, required fields exist,
and BELL.seq == CURRENT.md SEQ. If not, stop and report PROJECTION_DRIFT.

Only publish the first/next card when holder=codex/status=IDLE.
If holder=codex/status=READY_FOR_CODEX_REVIEW, review Claude's report first.
If holder=claude, wait and do not overwrite.
If holder=arthur or a hard gate is touched, stop for owner.

Task chain:
<paste task chain here>
```

## Claude Code Prompt

```text
Start the CURRENT_ONLY_KISS_V1 persistent watcher.

Only BELL.json + CURRENT.md decide communication truth. Require BELL.seq ==
CURRENT.md SEQ. Execute only when holder=claude/status=READY_FOR_CLAUDE.

After the report, reread BELL.json and CURRENT.md and confirm you still hold
the same seq/taskId. If not, report PROJECTION_DRIFT and do not overwrite.
Then write CURRENT.md as REPORT_READY, write BELL.json as holder=codex /
READY_FOR_CODEX_REVIEW, and continue the 60-second watcher.
```
