# Claude Code 短启动 Prompt

```text
在 `<absolute-project-path>` 中运行 Mode B watcher。

先读取：
- AGENTS.md / CLAUDE.md，如果存在
- docs/operations/agent-coordination/auto/README.md
- docs/operations/agent-coordination/auto/messages.ndjson
- docs/operations/agent-coordination/auto/state.json
- docs/operations/agent-coordination/auto/BOARD.md
- docs/operations/agent-coordination/inbox/<task-id>.md

只处理 run `mode-b-<projectSlug>-<runSlug>` 和 active task `<task-id>`。

循环：
- 先读 messages.ndjson，再读 state.json 和 BOARD.md。
- 只有 status = READY_FOR_CLAUDE 时才工作；否则等待 20 秒后重新读取。
- 只执行 active task card。
- 只写 allowed files：Claude report，以及 auto/messages.ndjson、auto/state.json、auto/BOARD.md。
- report 完成后，追加一个 REPORT_READY event，并把 state/BOARD 更新到 READY_FOR_CODEX_REVIEW。
- 如果 Codex 发送 reviewVerdict=NEEDS_FIX，只修复 report/coordination files。不要编辑 source。
- 遇到 OWNER_REVIEW_REQUIRED / OWNER_DECISION_REQUIRED / BLOCKED / ERROR 就停止。

禁止：不要启动/停止 services，不要编辑 allowed files 之外的文件，不要 install dependencies，不要 deploy，不要 call external APIs，不要 use secrets，不要 commit/push/merge，不要推进到另一个 task。
```
