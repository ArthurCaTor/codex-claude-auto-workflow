# 工作流

CURRENT_ONLY_KISS_V1 通过两个当前文件，协调一个 Codex session 和一个手动启动的
Claude Code watcher。

```text
BELL.json  = 极小机器轮次信号
CURRENT.md = 唯一当前详细通信包
state.json = projection/debug
BOARD.md   = projection/debug
```

历史日志、旧卡、旧 report、旧 review、terminal recap、timestamp、event id、
file mtime 都只是 legacy/debug，不能决定当前真相。

## 读取顺序

```text
1. 读 BELL.json。
2. 读 CURRENT.md。
3. 要求 BELL.json.seq == CURRENT.md SEQ。
4. 只有 CURRENT.md 指向 task card 或 report 时才读它。
```

如果文件不可读、格式错误、疑似半写、缺必需字段、或 seq 不一致，报告
`PROJECTION_DRIFT`，不要行动。

## 信号

```text
holder=claude / READY_FOR_CLAUDE
  Claude 执行 CURRENT.md 指向的唯一 task。

holder=codex / READY_FOR_CODEX_REVIEW
  Codex review report、diff、scope、validation。

holder=codex / IDLE
  Codex 可以发布一张安全有边界 task card。

holder=arthur 或 hard-gate status
  停下等待 owner 决策。
```

## 写入顺序

Codex 发布任务或 fix packet：

```text
1. next seq = current BELL.seq + 1
2. 先覆盖 CURRENT.md
3. 再覆盖 BELL.json，使用同一个 seq
4. state.json / BOARD.md 只作为 projection 更新
```

Claude 报告完成：

```text
1. 写 required report
2. 重读 BELL.json 和 CURRENT.md
3. 确认 holder=claude/status=READY_FOR_CLAUDE 且仍是同一 seq/taskId
4. 先覆盖 CURRENT.md 为 REPORT_READY
5. 再覆盖 BELL.json 为 holder=codex / READY_FOR_CODEX_REVIEW
6. 继续 60 秒 watcher
```

## 有限自动化

Codex 每次只能发布一张 task card。如果 owner 提供既定任务链，Codex 只把这条链拆成逐张 Claude card，不重新生成整套蓝图，也不让 Claude 自选任务。

`READY_FOR_CODEX_REVIEW` 必须先 review，不能绕过 review 直接发下一张。
`NEEDS_FIX` 只能在同一 active task、allowed files 和 retry budget 内回给 Claude。
