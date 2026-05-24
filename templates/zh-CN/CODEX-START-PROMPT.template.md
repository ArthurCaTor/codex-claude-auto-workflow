# Codex 启动 Prompt

这段和 `docs/zh-CN/one-prompt-start.md` 是同一个启动流程，放在这里方便复制到其他项目。

```text
在这个项目中初始化 Codex-Claude Auto Workflow。

不要立即实现业务功能。先学习这个项目，并准备一个有限的 Codex 管理 / Claude Code 执行 workflow。

使用这些规则：
- Codex 负责 planning、management 和 review。
- Claude Code 只执行一个 active bounded task card。
- human owner 手动启动 Claude Code，并授权 owner-gated actions。
- BELL.json 是共享铃铛：holder=claude 表示 Claude 干活，
  holder=codex 表示 Codex review 或准备下一个有限任务，
  holder=arthur 表示需要 owner 决策。
- workflow 应该在一个有限批次内以自动化、低人工干预的 file-heartbeat loop 运行。
- messages.ndjson 是 append-only coordination truth。
- state.json 和 BOARD.md 是 projections。
- 每个 run 都是有限的，并停在 OWNER_REVIEW_REQUIRED。
- 名称必须包含 projectSlug 和 runSlug。
- failed reviews 只能作为 bounded report-only fix loop 自动回给 Claude。

先读取 project instructions、README、architecture docs 如果存在、package scripts 和 git status。

创建：
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/BELL.json
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/
- docs/operations/agent-coordination/reports/
- docs/operations/agent-coordination/codex-reviews/

生成一个低风险 first task card、一个 Codex heartbeat prompt，以及一段简短可复制的 Claude Code startup prompt。

禁止启动/停止 Claude Code、deploy、install dependencies、call external APIs、use secrets、change schema/migrations、commit、push、merge，或继续进入业务实现。

准备完成后停下，并在 first run 前请求 owner 授权。
```
