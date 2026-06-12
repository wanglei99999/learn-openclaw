# 启动上下文

此文件在 Agent 启动时加载，提供额外的背景信息。

## 项目背景

此 Agent 是 claw0 教学框架的一部分，演示如何从零构建 AI Agent 网关。workspace 目录中的配置文件决定了 Agent 的行为：

- SOUL.md：人格与沟通风格
- IDENTITY.md：角色定义与边界
- TOOLS.md：可用工具及使用指南
- MEMORY.md：长期事实与偏好
- HEARTBEAT.md：主动行为指令
- BOOTSTRAP.md：本文件——额外的启动上下文
- AGENTS.md：多 Agent 协调说明
- CRON.json：定时任务定义

## Workspace 目录结构

```
workspace/
  *.md          -- 启动文件（加载进系统提示词）
  CRON.json     -- 定时任务定义
  memory/       -- 每日记忆日志
  skills/       -- 技能定义
  .sessions/    -- 会话记录（自动管理）
  .agents/      -- 各 Agent 状态（自动管理）
```
