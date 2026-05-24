# 接入清单

## Project Authority

- [ ] 读取 project instructions。
- [ ] 读取 README 和 architecture docs。
- [ ] 读取 package scripts 和 validation commands。
- [ ] 检查 `git status --short`。
- [ ] 记录 dirty worktree risks。

## Naming

- [ ] 定义 `projectSlug`。
- [ ] 定义 `runSlug`。
- [ ] 定义 `runId`。
- [ ] 定义 automation name。
- [ ] 定义 task id。
- [ ] 确认没有使用 generic names。

## Coordination Files

- [ ] 创建 `auto/README.md`。
- [ ] 创建 `auto/BELL.json`。
- [ ] 创建 `auto/messages.ndjson`。
- [ ] 创建 `auto/state.json`。
- [ ] 创建 `auto/BOARD.md`。
- [ ] 创建 `inbox/`。
- [ ] 创建 `reports/`。
- [ ] 创建 `codex-reviews/`。

## First Run

- [ ] 选择一个低风险 first task。
- [ ] 设置 `maxTasks = 1`。
- [ ] 设置 `maxReportFixRounds = 2`。
- [ ] 写明 allowed files。
- [ ] 写明 forbidden scope。
- [ ] 写明 validation。
- [ ] 写明 report path。
- [ ] 为 first active task 设置 `BELL.json.holder = "claude"`。

## Owner Gate

- [ ] owner 授权有限 run。
- [ ] owner 手动启动 Claude Code。
- [ ] Codex heartbeat 不控制 Claude process。
- [ ] run 停在 `OWNER_REVIEW_REQUIRED`。
