# Codex Heartbeat Prompt

```text
Mode B heartbeat for `<projectSlug>` run `mode-b-<projectSlug>-<runSlug>` in
`<absolute-project-path>`。

只监控 `<task-id>`。

每次 heartbeat：
1. 先读 auto/BELL.json。
2. 用 auto/messages.ndjson 和 auto/state.json 校验 BELL.json。
3. 如果 BELL.json 与 messages.ndjson/state.json 冲突，停止、报告 mismatch，并提供 resync prompt。不要根据 terminal recap 猜。
4. 读取 BOARD.md、task card 和 project instructions。
5. 如果 holder=codex 且 status 是 READY_FOR_CODEX_REVIEW，读取 Claude report 并检查 local diff。
6. 验证 allowed files、stop lines 和 validation。
7. 写 Codex review。
8. 如果 accepted 且 maxTasks 已达到，追加 OWNER_REVIEW_REQUIRED，并更新 BELL.json/state.json/BOARD.md。
9. 如果 NEEDS_FIX 只涉及 report/coordination files 且 reportFixLoop 预算仍可用，追加带 reviewVerdict=NEEDS_FIX 的 TASK_READY，并把 BELL.json 更新为 holder=claude、status=READY_FOR_CLAUDE。
10. 如果问题不是 report-only，或预算耗尽，停在 OWNER_REVIEW_REQUIRED、OWNER_DECISION_REQUIRED 或 BLOCKED，并按情况把 BELL.json holder 设置为 codex 或 arthur。

绝不启动/停止 Claude Code。绝不只凭 Claude report 就 accept。
```
