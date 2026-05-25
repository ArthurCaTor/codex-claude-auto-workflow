# Codex Heartbeat

Codex app heartbeat 是唤醒机制，不是通信真相。active truth 只来自对齐的
`BELL.json + CURRENT.md`。

## 隔离

一个 Codex heartbeat thread 只监控一个项目根目录。

不要创建 dual-project monitor。如果当前 Codex thread 已经有另一个 active
heartbeat，就让新项目 monitor 保持 paused，并为新项目开独立 Codex thread。

## 唤醒逻辑

每次 wake：

```text
1. 读 BELL.json。
2. 读 CURRENT.md。
3. 要求 BELL.seq == CURRENT.SEQ。
4. 推导 holder/status。
5. 只按当前 signal 行动。
```

`READY_FOR_CODEX_REVIEW` 表示必须先 review。Codex 必须检查 report、本地
diff、allowed files 和 validation，然后才能 accept、要求有限 fix、或发布下一张卡。

`IDLE` 表示 Codex 可以发布一张安全有边界 card。

`READY_FOR_CLAUDE` 表示 Claude 持有当前轮次，Codex 等待。

## 默认禁止

除非 owner 显式授权，不要启动/停止/kill/restart Claude Code，不要安装依赖、
修改 schema/migration、部署、使用 secrets、调用外部 API、commit、push、merge、
开 PR、发布 release、移动或删除 audit evidence。
