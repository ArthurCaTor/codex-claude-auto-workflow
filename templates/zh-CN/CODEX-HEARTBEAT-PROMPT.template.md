# Codex Heartbeat Prompt

```text
Mode B heartbeat for `<projectSlug>` run `mode-b-<projectSlug>-<runSlug>` in
`<absolute-project-path>`。

只监控 `<task-id>`。

每次 heartbeat：
1. 先读 auto/messages.ndjson，再读 state.json、BOARD.md、task card 和 project instructions。
2. 如果 status 是 READY_FOR_CODEX_REVIEW，读取 Claude report 并检查 local diff。
3. 验证 allowed files、stop lines 和 validation。
4. 写 Codex review。
5. 如果 accepted 且 maxTasks 已达到，追加 OWNER_REVIEW_REQUIRED，并更新 projections。
6. 如果 NEEDS_FIX 只涉及 report/coordination files 且 reportFixLoop 预算仍可用，追加带 reviewVerdict=NEEDS_FIX 的 TASK_READY，并把 projections 更新到 READY_FOR_CLAUDE。
7. 如果问题不是 report-only，或预算耗尽，停在 OWNER_REVIEW_REQUIRED、OWNER_DECISION_REQUIRED 或 BLOCKED。

绝不启动/停止 Claude Code。绝不只凭 Claude report 就 accept。
```
