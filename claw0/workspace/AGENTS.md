# 多 Agent 说明

## 默认 Agent

除非路由绑定将流量定向到特定 Agent，否则默认 Agent 处理所有消息。

## 多 Agent 配置

在生产级 OpenClaw 中，多个 Agent 可以同时运行：
- 每个 Agent 有自己的 workspace、会话和记忆
- 路由绑定决定哪个 Agent 处理哪条消息
- Agent 之间相互隔离，无法读取对方的会话

## Agent 间通信

Agent 之间不直接通信。协调通过以下方式进行：
1. 共享的 workspace 文件（如果配置了共享）
2. 路由层（绑定规则可在运行时更新）
3. 人工操作员（通过 Gateway API 或命令行）
