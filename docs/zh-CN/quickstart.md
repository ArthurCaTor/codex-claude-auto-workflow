# 快速开始

## 1. 复制模板

复制到目标项目的 `docs/operations/agent-coordination/auto/`：

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

替换所有占位符。不要复制其他项目的 run id、task id、report、review、state、
heartbeat 或历史。

## 2. 验证 Reset

```text
BELL.protocol == CURRENT_ONLY_KISS_V1
BELL.seq == CURRENT.md SEQ
holder=codex
status=IDLE
taskId=null
payloadType=RESET_PENDING
```

## 3. 启动 Codex

粘贴项目化后的 Codex start prompt。Codex 应该只发布一张新的安全 Claude task
packet，先写 `CURRENT.md`，再写 `BELL.json`。

## 4. 启动 Claude Code

当 Codex 输出 `holder=claude/status=READY_FOR_CLAUDE` 后，把短 Claude watcher
prompt 粘贴进 Claude Code。Claude 必须保持 60 秒 watcher，不写 idle heartbeat 文件。

## 5. Review Loop

当 Claude 返回 `holder=codex/status=READY_FOR_CODEX_REVIEW`，Codex 先 review
report 和 diff，然后再 accept、返回同一任务内的有限 fix、或发布下一张单卡。
