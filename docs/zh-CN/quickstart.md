# 快速开始

本指南用于在新项目里初始化 Codex-Claude Auto Workflow。

最快方式：在目标项目中打开 Codex，粘贴 [one-prompt-start.md](one-prompt-start.md) 里的 prompt。下面解释这段 prompt 会让 Codex 创建什么。

## 1. 准备项目

在目标项目中找到项目指令文件：

```text
AGENTS.md
CLAUDE.md
README.md
docs/architecture/*
package.json or equivalent test command source
```

如果项目没有 agent instruction 文件，先创建一个，再启用 workflow。

## 2. 创建协作目录

```text
docs/operations/agent-coordination/
  auto/
  inbox/
  reports/
  codex-reviews/
```

## 3. 选择唯一名称

每个 run 都必须使用全局唯一名称。

```text
projectSlug = my-project
runSlug     = docs-sweep-b01
runId       = mode-b-my-project-docs-sweep-b01
automation  = mode-b-my-project-docs-sweep-b01-monitor
taskId      = TASK-MY-PROJECT-B01-T01-DOCS-SWEEP
```

见 [naming.md](naming.md)。

## 4. 添加协作文件

复制并改写：

```text
templates/AUTO-README.template.md -> docs/operations/agent-coordination/auto/README.md
templates/STATE.initial.json      -> docs/operations/agent-coordination/auto/state.json
templates/MESSAGES.initial.ndjson -> docs/operations/agent-coordination/auto/messages.ndjson
templates/BOARD.template.md       -> docs/operations/agent-coordination/auto/BOARD.md
templates/TASK-CARD.template.md   -> docs/operations/agent-coordination/inbox/<taskId>.md
```

也可以使用中文模板：

```text
templates/zh-CN/
```

## 5. 从低风险任务开始

适合第一批的任务：

- read-only source sweep
- docs-only inventory
- test-command inventory
- guard inventory
- current-status report

不适合第一批：

- schema changes
- migrations
- dependency install
- deployment
- external APIs
- secrets
- commits and pushes
- broad refactors

## 6. 启动 Codex Heartbeat

使用 [../../templates/zh-CN/CODEX-HEARTBEAT-PROMPT.template.md](../../templates/zh-CN/CODEX-HEARTBEAT-PROMPT.template.md)，为这一个 run 创建 Codex heartbeat automation。

## 7. 手动启动 Claude Code

Human owner 手动在目标项目目录打开 Claude Code，然后粘贴 [../../templates/zh-CN/SHORT-CLAUDE-STARTUP-PROMPT.template.md](../../templates/zh-CN/SHORT-CLAUDE-STARTUP-PROMPT.template.md) 改写后的短 prompt。

不要让 Codex 启动、停止、kill 或重启 Claude Code。

## 8. 停在 Owner Review

有限批次完成后，Codex 设置：

```text
OWNER_REVIEW_REQUIRED
```

owner 决定是否开启下一个有限批次。
