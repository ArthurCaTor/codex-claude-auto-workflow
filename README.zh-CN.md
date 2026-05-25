# Codex-Claude Auto Workflow

一个用于 Codex 管理、Claude Code 执行的自动化文件工作流。

语言：[English](README.md) | [简体中文](README.zh-CN.md)

Codex-Claude Auto Workflow 把手动来回复制粘贴的协作方式，变成一个有边界、低人工干预的自动流：

```text
Codex       = 规划者、项目经理、任务卡作者、审查者
Claude Code = 只执行当前一个有边界任务的执行者
Human owner = 风险、范围、发布动作的授权关口
Files       = 共享协作状态
```

当前标准是 **CURRENT_ONLY_KISS_V1**。

```text
BELL.json  = 极小机器信号：现在轮到谁
CURRENT.md = 唯一当前详细通信包
state.json = 机器可读 projection/debug
BOARD.md   = 人类可读 projection/debug
```

`messages.ndjson`、旧 task card、旧 report、旧 review、terminal recap、
timestamp、event id、file mtime 都只是 legacy/debug，不能决定当前任务、新旧顺序或该谁行动。

## 为什么这样设计

最可靠的模型就是一个铃铛：

```text
holder=claude / READY_FOR_CLAUDE       -> Claude Code 干活
holder=codex  / READY_FOR_CODEX_REVIEW -> Codex review
holder=codex  / IDLE                   -> Codex 发一张安全任务卡
holder=arthur 或 hard gate             -> owner 决策
```

只要一个 actor 需要另一个 actor 处理事情，就用明确的 `holder` 和 `status`
交出铃铛。详细信息放在 `CURRENT.md`、task card、report、review 里。

这样可以避免 Codex 和 Claude Code 因为复杂信号、旧 summary、terminal recap
或历史日志而错过对方、互相死锁。

## 它能做什么

- 跑文档审计和源码扫描。
- 把很窄的实现切片交给 Claude Code。
- 让 Codex 负责任务排序、边界控制和验收。
- 按既定任务链逐卡执行，每次只给 Claude 一张卡。
- Codex review 未通过时，把同一任务内的有限 fix loop 自动回给 Claude。
- 多项目并行时，通过项目专属名称和“一项目一 Codex heartbeat thread”避免混乱。
- 把人工复制粘贴降低到一个 Codex 启动 prompt 和一个 Claude watcher prompt。

## 它不做什么

- 它不是 daemon 或托管服务。
- 它不会启动、停止或控制 Claude Code。
- 它不会替代项目自己的架构规则。
- 它默认不授权部署、secrets、依赖安装、commit、push、migration、schema 变更或外部 API 调用。
- 它不允许 Claude Code 自己选择下一张任务卡。

## 工作方式

每个项目创建：

```text
docs/operations/agent-coordination/
  auto/
    BELL.json
    CURRENT.md
    state.json
    BOARD.md
    README.md
    COMMUNICATION_ASCII_GATE.md
    CODEX-HEARTBEAT-PROMPT.md
    CLAUDE-WATCHER-PROMPT.md
  inbox/
    TASK-*.md
  reports/
    TASK-*-claude-report.md
  codex-reviews/
    TASK-*-codex-review.md
```

唯一 freshness gate：

```text
BELL.json.seq == CURRENT.md SEQ
```

如果任一文件不可读、格式错误、疑似半写、缺必需字段、或 seq 不一致，报告
`PROJECTION_DRIFT`，不要行动。

## 一个 Prompt 启动

在目标项目里打开 Codex，然后粘贴：

[docs/zh-CN/one-prompt-start.md](docs/zh-CN/one-prompt-start.md)

Codex 会准备本地协作文件，并输出一段简短 Claude Code watcher prompt。
Claude 之后保持 60 秒 watcher，只在 `holder=claude/status=READY_FOR_CLAUDE`
时执行。

## 关键保证

- 一个 Codex thread 只监控一个项目。
- Codex 发布任务时先写 `CURRENT.md`，再写 `BELL.json`。
- Claude 写完 report 后，先重读 BELL/CURRENT，确认自己仍持有同一个
  `seq/taskId`，再先写 `CURRENT.md`、后写 `BELL.json`。
- `READY_FOR_CODEX_REVIEW` 是 review gate，不能绕过 review 直接发下一张卡。
- 每个任务都有 allowed files、forbidden scope、validation 和 stop conditions。
- owner-gated 动作必须停下等待人工授权。

## 仓库结构

```text
docs/
  zh-CN/
  adoption-checklist.md
  bell.md
  heartbeat.md
  naming.md
  one-prompt-start.md
  workflow.md
  quickstart.md
  report-only-fix-loop.md
  roles.md
  state-machine.md
  task-chain-prompts.md
examples/
  minimal-run/
templates/
  zh-CN/
  AUTO-README.template.md
  BELL.initial.json
  CURRENT.initial.md
  BOARD.template.md
  COMMUNICATION_ASCII_GATE.template.md
  CODEX-HEARTBEAT-PROMPT.template.md
  CLAUDE-WATCHER-PROMPT.template.md
  CODEX-START-PROMPT.template.md
  SHORT-CLAUDE-STARTUP-PROMPT.template.md
  STATE.initial.json
  TASK-CARD.template.md
  TASK-CHAIN-PROMPTS.md
```

## License

MIT. See [LICENSE](LICENSE).
