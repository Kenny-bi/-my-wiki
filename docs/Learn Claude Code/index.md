# Learn Claude Code

> [Bash is all you need](https://github.com/shareAI-lab/learn-claude-code) — 一个 nano Claude Code 风格的 Agent Harness，从零搭建。

这个项目通过 12 个递进的 Session，逐行拆解一个类 Claude Code 的 Agent 框架。每个 Session 在上一个的基础上新增一个核心机制，从最简循环到完整的技能加载系统。

## 目录

| Session | 主题 | 核心机制 |
|---------|------|---------|
| [s01 - 逐行拆解](s01-逐行拆解.md) | 最小 Agent 循环 | API 调用 + while 循环 + tool_use |
| [s02 - 工具调度与Python语法](s02-工具调度与Python语法.md) | 工具调度 | TOOL_HANDLERS 分发表、lambda、.get() |
| [s03 - TodoWrite](s03-todo-write.md) | 任务管理 + Nag 提醒 | TodoManager 类、class、enumerate() |
| [s04 - Subagent](s04-subagent.md) | 父子 Agent 架构 | 上下文隔离、子循环、30轮安全上限 |
| [s05 - Skill Loading](s05-skill-loading.md) | 按需加载技能 | 两层注入、YAML frontmatter、延迟加载 |

## 学习路线

```
s01: Agent 能跑起来（API 调用 + 工具执行循环）
s02: Agent 能用工具（bash/read/write/edit）
s03: Agent 能规划任务（todo + 提醒）
s04: Agent 能派子任务（上下文隔离）
s05: Agent 能按需学习（技能加载）
s06-s12: 更多机制...
```
