# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

**claw0** 是一个用 Python 从零构建 AI Agent 网关的渐进式教程。共 10 个章节（`s01`–`s10`），每节是一个独立可运行的 `.py` 文件，在前一节基础上只新增一个概念。代码 + 文档并列放置，支持三种语言（`en/`、`zh/`、`ja/`），三个语言版本逻辑完全一致，只有注释和文档语言不同。

## 环境准备

```sh
pip install -r requirements.txt
cp .env.example .env
# 在 .env 中填写 ANTHROPIC_API_KEY 和 MODEL_ID
```

`.env` 必填：`ANTHROPIC_API_KEY`、`MODEL_ID`（如 `claude-sonnet-4-20250514`）。  
可选：`ANTHROPIC_BASE_URL`（代理商）、`TELEGRAM_BOT_TOKEN`、`FEISHU_APP_ID`/`FEISHU_APP_SECRET`（s04 起）、`HEARTBEAT_*`（s07 起）。

## 运行章节

```sh
# 从仓库根目录运行，脚本会自动向上找 .env
python sessions/zh/s01_agent_loop.py
python sessions/en/s06_intelligence.py
```

每个脚本完全自包含，无跨章节 import，可单独运行任意章节。

## 架构：章节累积关系

每节严格在前一节基础上扩展，不重构已有代码：

| 章节 | 核心新增 |
|------|---------|
| s01 | `while True` + `stop_reason` — 最小 Agent 循环 |
| s02 | 工具 dispatch table：schema dict + handler map |
| s03 | JSONL 会话持久化 + 3 阶段上下文溢出重试 |
| s04 | 通道适配器（Telegram、Feishu）→ 统一 `InboundMessage` |
| s05 | 网关：5 级绑定表 `(peer > guild > account > channel > default)` → `(agent_id, session_key)` |
| s06 | 智能层：从 workspace 文件组装系统提示词 + `MemoryStore` + `SkillsManager` |
| s07 | 心跳定时线程 + `CRON.json` 调度器，与用户消息共用单一 `threading.Lock` |
| s08 | 预写投递队列：先写磁盘再发送，指数退避 |
| s09 | 3 层重试洋葱：API key 轮换（Layer 1）→ 上下文溢出压缩（Layer 2）→ 工具循环（Layer 3） |
| s10 | 用命名 FIFO 车道（`main`/`cron`/`heartbeat`）替换单一 Lock，基于 `concurrent.futures.Future` 返回结果 |

## workspace 目录

`workspace/` 存放 s06 起在 Agent 启动时加载的运行时配置文件：

| 文件 | 用途 |
|------|------|
| `SOUL.md` | 人格定义 |
| `IDENTITY.md` | 角色与边界 |
| `TOOLS.md` | 工具使用指引 |
| `USER.md` / `MEMORY.md` | 用户上下文 / 长期记忆 |
| `HEARTBEAT.md` | 心跳主动行为指令 |
| `BOOTSTRAP.md` | 额外启动上下文 |
| `AGENTS.md` | 多 Agent 协调说明 |
| `CRON.json` | 定时任务定义（s07 起） |
| `skills/example-skill/SKILL.md` | 示例技能（s06 `SkillsManager` 加载） |

修改这些文件即可改变 Agent 行为，无需动代码。运行时自动生成的目录（`.sessions/`、`.agents/`、`memory/`）已在 `.gitignore` 中忽略。

## 关键设计模式

**所有章节统一的 .env 加载方式：**
```python
load_dotenv(Path(__file__).resolve().parent.parent.parent / ".env", override=True)
```

**会话存储**（s03 起）：JSONL 文件位于 `workspace/.agents/<agent_id>/sessions/`。追加写入，顺序重放。溢出时通过独立 API 调用对旧消息做摘要压缩。

**5 级路由**（s05 起）：绑定解析优先级 `peer_id > guild_id > account_id > channel > default`，最具体匹配胜出。

**提示词组装**（s06 起）：`BootstrapLoader` 按 `BOOTSTRAP_FILES` 顺序读取各文件，单文件上限 `MAX_FILE_CHARS`，总量上限 `MAX_TOTAL_CHARS`。`SkillsManager` 通过 TF-IDF 余弦相似度附加相关技能内容。

**s06 REPL 调试命令：** `/soul`、`/skills`、`/memory`、`/search <q>`、`/prompt`、`/bootstrap` — 实时审查 Agent 内部状态。
