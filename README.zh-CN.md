# Codex-Claude Auto Workflow

一个用于 Codex 管理、Claude Code 执行的自动化文件工作流。

语言：[English](README.md) | [简体中文](README.zh-CN.md)

Codex-Claude Auto Workflow 把手动来回复制粘贴的协作方式，变成一个有边界、可审计、低人工干预的自动流：

```text
Codex       = 规划者、项目经理、任务卡作者、审查者
Claude Code = 只执行当前一个有边界任务的执行者
Human owner = 风险、范围、发布动作的授权关口
Files       = 共享协作状态
```

这个工作流适合这样的场景：你希望 Codex 负责项目管理、任务拆分和验收，同时让 Claude Code 在可见终端里执行实现、文档、验证或审计任务。它可以让有限任务批次自动流转，但仍然在 owner gate 停下等待人工授权。

## 目的

这个工作流的目标，是让多 Agent 开发更安全、更自动化，也更省心。

没有它时，owner 往往要在 Codex 和 Claude Code 之间手动搬运消息：

```text
Owner 让 Codex 做计划
Owner 把 Codex 指令复制给 Claude Code
Claude Code 执行
Owner 把 Claude 报告复制回 Codex
Codex 审查
Owner 把下一轮修复或任务再复制给 Claude Code
```

Codex-Claude Auto Workflow 用一个小型文件心跳状态机，替代大部分手动转发：

```text
Codex 写一个有边界的任务卡
Codex 初始化一个有限 run
Claude Code 持续监听协作文件
Claude Code 只执行当前 active task
Claude Code 写报告并追加事件
Codex heartbeat 审查报告和本地 diff
Codex 接受、阻塞，或返回一个有限的 report-only 修复
run 停在 owner review
```

重点是“有限自动流”，不是无限自治。Codex 和 Claude 可以在一个有限批次内自动推进，甚至可以自动完成有限次数的 report-only 修复循环；但部署、 secrets、schema 变更、依赖安装、commit、push 和范围扩张仍然必须由 owner 显式授权。

## 它能做什么

- 跑文档审计。
- 跑源码扫描和 inventory 任务。
- 把很窄的实现切片交给 Claude Code。
- 让 Codex 负责任务排序、边界控制和验收。
- 保留 handoff、report、review、owner gate 的持久审计轨迹。
- 通过唯一 run/task 名称，支持多个项目同时运行。
- 把人工复制粘贴降低到：一个 Codex 启动 prompt，加一个 Claude Code 短启动 prompt。

## 它不做什么

- 它不是 daemon 或托管服务。
- 它不会启动或控制 Claude Code 进程。
- 它不会替代项目自己的架构规则。
- 它默认不授权部署、secrets、依赖安装、commit、push、migration 或外部 API 调用。
- 它不允许 Claude Code 自行推进一个无限 backlog。

## 工作方式

每个项目会得到一个小型协作目录：

```text
docs/operations/agent-coordination/
  auto/
    README.md
    messages.ndjson
    state.json
    BOARD.md
  inbox/
    TASK-*.md
  reports/
    TASK-*-claude-report.md
  codex-reviews/
    TASK-*-codex-review.md
```

核心 truth 规则：

```text
messages.ndjson = append-only coordination truth
state.json      = machine-readable projection
BOARD.md        = human-readable projection
```

如果文件之间不一致，以 `messages.ndjson` 为准。

## 一个 Prompt 启动

在新项目里启动整套流程时，打开该项目里的 Codex，然后粘贴：

[docs/zh-CN/one-prompt-start.md](docs/zh-CN/one-prompt-start.md)

Codex 会：

1. 读取目标项目规则和 git 状态。
2. 选择项目专属名称。
3. 创建协作目录和文件。
4. 创建第一个低风险任务卡。
5. 准备 Codex heartbeat prompt。
6. 输出一段简短 Claude Code 启动 prompt，方便 owner 复制到 Claude Code。
7. 在执行前停下，向 owner 请求授权。

## 关键保证

- 每个 run 都是有限的。
- 每个 task 都有 allowed files、forbidden scope、validation、report path 和 stop conditions。
- Claude Code 只执行一个 active task card。
- Codex 独立审查 Claude 报告和本地 diff。
- owner-gated 动作必须停下等待人工授权。
- review 未通过时，可以在同一任务内进入有限的 report-only fix loop。
- 禁止使用通用 automation 名称，因此多个项目可以安全并行运行。

## 快速开始

阅读：

- [docs/zh-CN/one-prompt-start.md](docs/zh-CN/one-prompt-start.md)
- [docs/zh-CN/quickstart.md](docs/zh-CN/quickstart.md)
- [docs/zh-CN/workflow.md](docs/zh-CN/workflow.md)
- [docs/zh-CN/naming.md](docs/zh-CN/naming.md)

然后复制并改写 [templates/zh-CN/](templates/zh-CN/) 里的模板。

## 仓库结构

```text
docs/
  zh-CN/
  adoption-checklist.md
  heartbeat.md
  naming.md
  one-prompt-start.md
  workflow.md
  quickstart.md
  report-only-fix-loop.md
  roles.md
  state-machine.md
examples/
  minimal-run/
templates/
  zh-CN/
  AUTO-README.template.md
  BOARD.template.md
  CODEX-HEARTBEAT-PROMPT.template.md
  CODEX-REVIEW.template.md
  CODEX-START-PROMPT.template.md
  SHORT-CLAUDE-STARTUP-PROMPT.template.md
  STATE.initial.json
  MESSAGES.initial.ndjson
  TASK-CARD.template.md
```

## License

MIT. See [LICENSE](LICENSE).
