# Quick Start

## 1. Copy Templates

Copy these into the target project under
`docs/operations/agent-coordination/auto/`:

```text
templates/BELL.initial.json -> BELL.json
templates/CURRENT.initial.md -> CURRENT.md
templates/STATE.initial.json -> state.json
templates/BOARD.template.md -> BOARD.md
templates/AUTO-README.template.md -> README.md
templates/COMMUNICATION_ASCII_GATE.template.md -> COMMUNICATION_ASCII_GATE.md
templates/CODEX-HEARTBEAT-PROMPT.template.md -> CODEX-HEARTBEAT-PROMPT.md
templates/CLAUDE-WATCHER-PROMPT.template.md -> CLAUDE-WATCHER-PROMPT.md
templates/CODEX-START-PROMPT.template.md -> TEMPLATE-CODEX-MODEB-TIER3-START.md
templates/SHORT-CLAUDE-STARTUP-PROMPT.template.md -> TEMPLATE-CLAUDE-CODE-WATCHER-START.md
templates/TASK-CHAIN-PROMPTS.md -> TASK-CHAIN-PROMPTS.md
```

Replace every placeholder. Do not copy another project's run id, task id,
reports, reviews, state, heartbeat, or history.

## 2. Verify Reset

```text
BELL.protocol == CURRENT_ONLY_KISS_V1
BELL.seq == CURRENT.md SEQ
holder=codex
status=IDLE
taskId=null
payloadType=RESET_PENDING
```

## 3. Start Codex

Paste the project-adapted Codex start prompt. Codex should publish exactly one
fresh safe Claude task packet by writing `CURRENT.md` first and `BELL.json`
second.

## 4. Start Claude Code

After Codex outputs `holder=claude/status=READY_FOR_CLAUDE`, paste the short
Claude watcher prompt into Claude Code. Claude must continue the 60-second
watcher and must not write idle heartbeat files.

## 5. Review Loop

When Claude returns `holder=codex/status=READY_FOR_CODEX_REVIEW`, Codex reviews
the report and diff before it accepts, returns a bounded same-task fix, or
publishes the next one-card packet.
