# 一个 Prompt 启动

从目标项目目录打开 Codex，然后粘贴下面这段。它会让 Codex 学习项目、初始化 workflow，并输出一段简短的 Claude Code prompt。

```text
在这个项目中初始化 Codex-Claude Auto Workflow。

不要立即实现业务功能。先学习这个项目，并准备一个有限的 Codex 管理 / Claude Code 执行 workflow。

使用这些规则：

- Codex 是规划者、项目经理、任务卡作者和 reviewer。
- Claude Code 是有边界的执行者，只执行一个 active task card。
- human owner 手动启动 Claude Code，并授权 owner-gated actions。
- 文件是协作层。
- BELL.json 是共享铃铛：holder=claude 表示 Claude 干活，
  holder=codex 表示 Codex review 或准备下一个有限任务，
  holder=arthur 表示需要 owner 决策。
- workflow 应该在一个有限批次内以自动化、低人工干预的 file-heartbeat loop 运行。
- messages.ndjson 是 append-only coordination truth。
- state.json 和 BOARD.md 是 projection；必要时可从 messages.ndjson 重建。
- 每个 run 都是有限的，必须停在 OWNER_REVIEW_REQUIRED。
- 名称必须包含 projectSlug 和 runSlug，确保多个项目可以安全并行运行。
- Claude Code 绝不能自行推进到下一个 task。
- Codex 必须独立审查 Claude report 和 local diff。
- review 未通过时，只能在设置了 maxReportFixRounds 的 bounded report-only fix loop 中自动回给 Claude。
  不要把这个自动循环用于 source code、schema、dependency、deployment 或 architecture fixes。

先读取：

- AGENTS.md 或 CLAUDE.md，如果存在
- README.md
- architecture / design / operations docs，如果存在
- package scripts 或 test-command documentation
- 当前 git status

然后创建或更新：

- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

使用全局唯一名称：

- projectSlug：这个项目的 lowercase kebab-case 名称
- runSlug：这个有限批次的 lowercase kebab-case 名称
- runId：mode-b-<projectSlug>-<runSlug>
- automationName：mode-b-<projectSlug>-<runSlug>-monitor
- taskId：TASK-<PROJECTSLUG>-B01-T01-<SHORT-DESCRIPTION>

第一个 task 必须低风险：

- 优先 read-only source sweep、docs-only inventory、guard inventory 或 test-command inventory
- maxTasks = 1
- maxReportFixRounds = 2
- allowed files 必须明确
- forbidden scope 必须明确
- validation commands 必须明确
- report path 必须明确

准备：

1. 协作文件。
2. 一个有边界的 first task card。
3. 这个 run 专属的 Codex heartbeat prompt。
4. 一段简短可复制的 Claude Code startup prompt，供 owner 粘贴。

禁止：

- 启动、停止、kill 或重启 Claude Code
- deploy
- install dependencies
- call external APIs
- use secrets
- change schema or migrations
- commit, push, merge, or open a PR
- 继续进入业务实现

完成后报告：

- created or changed files
- projectSlug / runSlug / runId / automationName / taskId
- first task card path
- heartbeat automation 是已创建还是仅 proposed
- short Claude Code startup prompt
- 开始运行前需要 owner 授权什么
```
