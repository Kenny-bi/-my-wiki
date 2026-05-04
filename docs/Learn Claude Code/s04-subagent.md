---
title: s04 - Subagent 子代理
date: 2026-05-01
tags: [Lang/Python, Domain/Agent, Type/原理, Status/已理解]
session: s04
---

# s04 - Subagent 子代理

## 核心思想

父 Agent 可以派一个子 Agent 出去干杂活。子 Agent 带着**空的对话历史**出发，干完活只返回**一句话总结**。

```
Parent agent                     Subagent
+------------------+             +------------------+
| messages=[...]   |             | messages=[]      |  ← 空的！
|                  |  dispatch   |                  |
| tool: task       | ----------> | while tool_use:  |
|   prompt="..."   |             |   call tools     |
|                  |  summary    |                  |
|   result = "..." | <---------- | return last text |
+------------------+             +------------------+
          |
Parent context stays clean.      子 agent 的上下文被丢弃。
```

**为什么需要这个**：父 Agent 的 messages 越来越长，如果让它自己去做搜索/探索类任务，上下文会被大量搜索结果撑爆。派一个子 Agent 去做，父 Agent 只收到一句总结，上下文保持干净。

---

## 第 26-43 行：import 和初始化

```python
import os
import subprocess
from pathlib import Path

from anthropic import Anthropic
from dotenv import load_dotenv

load_dotenv(override=True)

if os.getenv("ANTHROPIC_BASE_URL"):
    os.environ.pop("ANTHROPIC_AUTH_TOKEN", None)

WORKDIR = Path.cwd()
client = Anthropic(base_url=os.getenv("ANTHROPIC_BASE_URL"))
MODEL = os.environ["MODEL_ID"]
```

和 s02/s03 完全一样。

---

### 关键变化：两个 System Prompt

```python
SYSTEM = f"You are a coding agent at {WORKDIR}. Use the task tool to delegate exploration or subtasks."

SUBAGENT_SYSTEM = f"You are a coding subagent at {WORKDIR}. Complete the given task, then summarize your findings."
```

| 变量 | 给谁用 | 内容 |
|------|--------|------|
| `SYSTEM` | 父 Agent | "用 task 工具派任务" |
| `SUBAGENT_SYSTEM` | 子 Agent | "完成任务，然后总结" |

父 Agent 知道可以派任务，子 Agent 知道自己的职责是干活+总结。

---

## 第 47-95 行：四个基础工具

和 s02/s03 完全一样：`safe_path`, `run_bash`, `run_read`, `run_write`, `run_edit`。

区别：s04 的 `run_bash` 多了一个 except：

```python
except (FileNotFoundError, OSError) as e:
    return f"Error: {e}"
```

`(FileNotFoundError, OSError)` 是元组，匹配其中任意一个异常。比分开写两个 except 简洁。

---

## 第 97-102 行：TOOL_HANDLERS

```python
TOOL_HANDLERS = {
    "bash":       lambda **kw: run_bash(kw["command"]),
    "read_file":  lambda **kw: run_read(kw["path"], kw.get("limit")),
    "write_file": lambda **kw: run_write(kw["path"], kw["content"]),
    "edit_file":  lambda **kw: run_edit(kw["path"], kw["old_text"], kw["new_text"]),
}
```

和 s02 一样，四个基础工具。注意没有 `todo` 和 `task`——todo 没带过来，task 是父 Agent 专属，后面单独处理。

---

## 第 104-114 行：CHILD_TOOLS（子 Agent 的工具箱）

```python
CHILD_TOOLS = [
    {"name": "bash", ...},
    {"name": "read_file", ...},
    {"name": "write_file", ...},
    {"name": "edit_file", ...},
]
```

子 Agent 只有四个基础工具。**没有 task 工具**——注释写得很清楚：`no recursive spawning`，防止子 Agent 再派孙 Agent，无限套娃。

---

## 第 118-136 行：run_subagent（本期核心）

```python
def run_subagent(prompt: str) -> str:
    sub_messages = [{"role": "user", "content": prompt}]  # 空的！只有任务描述
```

**输入**：父 Agent 给的任务描述字符串
**返回**：子 Agent 的最后一段文字总结

**关键**：`sub_messages` 只包含一条用户消息。父 Agent 的整个对话历史（可能几百条消息）**不传给子 Agent**。这就是注释里说的 "fresh context"。

---

### 第 120 行：安全上限

```python
for _ in range(30):  # safety limit
```

`_` 是占位变量名——约定俗成表示"这个值我不关心"。等价于 `for i in range(30)` 但明确告诉读代码的人：循环变量用不到。

最多 30 轮，防止子 Agent 无限循环，比 `while True` 安全。

---

### 第 121-127 行：子 Agent 的 API 调用

```python
response = client.messages.create(
    model=MODEL, system=SUBAGENT_SYSTEM, messages=sub_messages,
    tools=CHILD_TOOLS, max_tokens=8000,
)
sub_messages.append({"role": "assistant", "content": response.content})
if response.stop_reason != "tool_use":
    break
```

和父 Agent 的循环一样，区别：
- 用的是 `SUBAGENT_SYSTEM`（"你是子 Agent，完成任务后总结"）
- 用的是 `CHILD_TOOLS`（没有 task 工具）
- 30 轮安全上限

---

### 第 128-134 行：子 Agent 执行工具

```python
results = []
for block in response.content:
    if block.type == "tool_use":
        handler = TOOL_HANDLERS.get(block.name)
        output = handler(**block.input) if handler else f"Unknown tool: {block.name}"
        results.append({"type": "tool_result", "tool_use_id": block.id, "content": str(output)[:50000]})
sub_messages.append({"role": "user", "content": results})
```

和 s01/s02 完全一样的工具执行逻辑。代码复用了同一个 `TOOL_HANDLERS`。

---

### 第 136 行：提取总结

```python
return "".join(b.text for b in response.content if hasattr(b, "text")) or "(no summary)"
```

拆解：

```python
# 1. for b in response.content              → 遍历模型返回的每个 block
# 2. if hasattr(b, "text")                  → 只取有 text 属性的 block（跳过 tool_use block）
# 3. b.text                                 → 取文字内容
# 4. "".join(...)                           → 把所有文字拼成一个字符串
# 5. ... or "(no summary)"                 → 如果结果是空字符串，用默认值
```

**巧妙的 `or` 短路**：空字符串 `""` 是 falsy，所以 `"" or "(no summary)"` 返回 `"(no summary)"`。

`s01` 学过：`a or b` 如果 a 是 falsy 就返回 b。

这条返回的文字就是子 Agent 给父 Agent 的"工作总结"。

---

## 第 140-143 行：PARENT_TOOLS（父 Agent 的工具箱）

```python
PARENT_TOOLS = CHILD_TOOLS + [
    {"name": "task", ...},
]
```

父 Agent 的工具 = 四个基础工具 + task 工具。

**列表相加**：`[a, b] + [c]` → `[a, b, c]`。Python 里 `+` 拼接两个列表，返回新列表。

task 工具的定义：

```python
{"name": "task", "description": "Spawn a subagent with fresh context...",
 "input_schema": {
     "type": "object",
     "properties": {
         "prompt": {"type": "string"},                              # 必填：任务描述
         "description": {"type": "string", "description": "Short description"}  # 可选：简短说明
     },
     "required": ["prompt"]     # 只有 prompt 必填，description 可选
 }}
```

父 Agent 调用 task 工具时，需要提供任务描述 prompt。description 是可选的，只是人类看得懂的标签。

---

## 第 146-168 行：agent_loop（父 Agent 循环）

```python
def agent_loop(messages: list):
    while True:
        response = client.messages.create(
            model=MODEL, system=SYSTEM, messages=messages,
            tools=PARENT_TOOLS, max_tokens=8000,    # 用的是 PARENT_TOOLS
        )
        messages.append({"role": "assistant", "content": response.content})
        if response.stop_reason != "tool_use":
            return
        results = []
        for block in response.content:
            if block.type == "tool_use":
                if block.name == "task":              # ← 新逻辑：如果是 task 工具
                    desc = block.input.get("description", "subtask")
                    prompt = block.input.get("prompt", "")
                    print(f"> task ({desc}): {prompt[:80]}")
                    output = run_subagent(prompt)     # 启动子 Agent！
                else:
                    handler = TOOL_HANDLERS.get(block.name)
                    output = handler(**block.input) if handler else f"Unknown tool: {block.name}"
                print(f"  {str(output)[:200]}")
                results.append({"type": "tool_result", "tool_use_id": block.id, "content": str(output)})
        messages.append({"role": "user", "content": results})
```

### 关键分支（第 158-162 行）

```python
if block.name == "task":
    desc = block.input.get("description", "subtask")  # 取描述，没有则默认 "subtask"
    prompt = block.input.get("prompt", "")
    print(f"> task ({desc}): {prompt[:80]}")           # 打印前 80 个字符
    output = run_subagent(prompt)                     # 同步等待子 Agent 完成
```

**`.get("description", "subtask")`**：因为 description 是可选的（TOOLS 定义里 required 只有 prompt），用 `.get()` 提供默认值。

**`prompt[:80]`**：切片，只取前 80 个字符打印，防止终端刷屏。

**同步执行**：`run_subagent(prompt)` 是同步调用，父 Agent 会卡住等待子 Agent 完成。子 Agent 最多跑 30 轮，所以父 Agent 最多等 30 轮 API 调用的时间。

### else 分支（第 163-165 行）

如果不是 task 工具，就和以前一样走 `TOOL_HANDLERS` 分发表。

---

## s03 → s04 变化对比

| | s03 | s04 |
|---|---|---|
| 工具数 | 5 (含 todo) | 子:4, 父:5 (含 task) |
| Agent 数量 | 1 | 2 (父子) |
| System Prompt | 1 个 | 2 个 |
| 循环 | 1 个 agent_loop | 父循环 + 子循环 |
| 上下文 | 单一份 | 父子隔离，子返回后丢弃 |
| 新 Python | class, enumerate, sum | `_` 占位符, `"".join()`, `a or b` 短路, 列表相加 |
| 安全机制 | todo 验证 | 30 轮上限 + 禁止递归套娃 |

**s04 的教训**：上下文隔离不需要进程隔离，只需要不同的 `messages` 列表。子 Agent 的 messages 用完即丢，父 Agent 的上下文保持干净。

---

## 2026-05-01：dotenv 库是干嘛的

### 问题

每个 session 文件开头都有 `from dotenv import load_dotenv`，这库干啥的？

### 答案

把 `.env` 文件里的配置读到系统环境变量里。

```python
from dotenv import load_dotenv
load_dotenv(override=True)
```

**`.env` 文件长这样**：

```
ANTHROPIC_BASE_URL=https://api.anthropic.com
MODEL_ID=claude-sonnet-4-6
ANTHROPIC_API_KEY=sk-ant-xxx
```

**运行流程**：

```
.env 文件 (在硬盘上)
    │  load_dotenv()
    ▼
系统环境变量 (在内存里)
    │  os.getenv() / os.environ[]
    ▼
你的代码里直接用
```

**不用 dotenv 的话**，你得每次手动在终端 export。

`override=True`：如果环境变量已经存在，用 .env 的值覆盖它。

**核心作用**：配置和代码分离。API key 这种敏感信息写在 .env 里，.env 不提交到 git（写在 .gitignore 里），代码里只写 `os.getenv("KEY")`。

---

## 2026-05-01：一行简写为什么难读

### 问题

```python
return "".join(b.text for b in response.content if hasattr(b, "text")) or "(no summary)"
```

这一行太浓缩了，为什么代码里很多这种简写？

### 展开写法

```python
# 第一步：收集所有文字
texts = []
for b in response.content:              # 遍历每个 block
    if hasattr(b, "text"):              # 这个 block 有文字吗？
        texts.append(b.text)            # 有就收集起来

# 第二步：拼成一句话
summary = "".join(texts)                # 把所有文字拼成一个字符串

# 第三步：兜底
if summary:                             # 非空字符串就是 True
    return summary
else:
    return "(no summary)"               # 空字符串的兜底
```

### 语法对应

| 简写 | 等价展开 | 哪学的 |
|------|---------|--------|
| `b.text for b in ... if ...` | for 循环 + append | 生成器表达式 |
| `"".join(...)` | 把字符串列表拼成一个 | s04 新学的 |
| `... or "default"` | if 判空后返回默认值 | s01 的 `a or b` |

### 为什么代码里这么写

这个项目是**教学用的**，逐 session 递增复杂度：

```
s01: 没有简写，全是展开写法
s02: lambda + **kw 简写
s03: sum(1 for ...) 生成器表达式
s04: "".join(...) + or 短路
```

每个 session 引入一两个新简写，让你慢慢适应。现在觉得难读是因为一次看了四个 session。

实际工程中用这些是因为 5 行变 1 行。但更好的写法是拆成两步：

```python
text_blocks = [b.text for b in response.content if hasattr(b, "text")]
return "".join(text_blocks) or "(no summary)"
```

一行写完属于炫技，可读性优先。

---
#Lang/Python #Domain/Agent #Type/原理 #Session/s04
