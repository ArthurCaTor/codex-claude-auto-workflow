# 角色

## Human Owner

Human owner：

- 手动启动 Claude Code
- 观察 Claude Code logs
- 授权 owner-gated actions
- 决定是否启动下一个有限批次
- 退出 manual recovery states

## Codex

Codex：

- 读取项目 authority docs
- 写 bounded task cards
- 初始化有限 runs
- 在可用时创建 heartbeat automation
- 独立审查 Claude reports 和 local diffs
- 写 Codex review files
- 接受、阻塞，或返回有限的 report-only fixes

Codex 不得：

- 启动、停止、kill 或重启 Claude Code
- 只凭 Claude report 就接受工作
- 未经 owner 显式授权就 deploy 或 push
- 越过 owner-gated stop lines

## Claude Code

Claude Code：

- 读取 `messages.ndjson`、`state.json`、`BOARD.md` 和 active task card
- 只在 status 为 `READY_FOR_CLAUDE` 时执行
- 只写 allowed files
- 写 required report
- 追加一个 report 或 blocking event
- 等待 Codex review

Claude Code 不得：

- 自行推进到下一个 task
- 写 Codex reviews
- 标记 work accepted
- 编辑 task card 之外的文件
- deploy、install dependencies、use secrets、push 或 merge，除非 active task 明确授权
