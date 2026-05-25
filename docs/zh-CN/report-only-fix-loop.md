# 有限修复循环

CURRENT_ONLY_KISS_V1 支持在 Codex review 之后，把同一任务内的修复有限回给
Claude，但必须满足明确边界。

默认配置：只允许 report 与 coordination 文件修复。

## 允许自动回修的前提

- active task id 不变
- 修复范围仍在该任务 allowed files 内
- 不触发 hard gate
- retry budget 仍有余额
- Codex 已完成 `READY_FOR_CODEX_REVIEW` 的审查

## 常见可自动修复项

- Claude report 缺少 validation summary
- file inventory 或 risk section 不清楚
- `state.json` / `BOARD.md` projection 字段错误
- `CURRENT.md` report packet 字段不完整
- `BELL.json` 交接字段缺失

## 必须停止并升级 owner 的情况

- 修复需要改代码、schema、migration、依赖
- 修复需要部署、secrets、外部 API、commit/push/PR/release
- 修复需要越过当前任务 allowed files
- retry budget 已耗尽
- drift 无法安全对齐

## 运行形态

Codex review 可以把 `NEEDS_FIX` 以“同一任务 packet”回给 Claude：

```text
先写 CURRENT.md
再写 BELL.json
holder=claude
status=READY_FOR_CLAUDE
taskId 不变
```

修复通过后，Codex 要么发布下一张已授权单卡，要么按 owner 策略停在
`IDLE` / `OWNER_REVIEW_REQUIRED`。
