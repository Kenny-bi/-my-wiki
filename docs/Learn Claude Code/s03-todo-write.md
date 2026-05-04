---
title: s03 - TodoWrite 与 Nag 提醒
date: 2026-05-01
tags: [Lang/Python, Domain/Agent, Type/原理, Status/已理解]
session: s03
---

# s03 - TodoWrite 与 Nag 提醒

## 核心思想

s03 给 Agent 加了一个 **todo 工具**：模型可以自己维护任务列表，标记进度。

加上一个 **Nag 提醒机制**：如果连续 3 轮没用 todo，就往对话里塞一句 "别忘了更新 todo"。

```
+----------+      +-------+      +---------+
|   User   | ---> |  LLM  | ---> | Tools   |
|  prompt  |      |       |      | + todo  |
+----------+      +---+---+      +----+----+
                      ^               |
                      |   tool_result |
                      +---------------+
                            |
                +-----------+-----------+
                | TodoManager state     |
                | [ ] task A            |
                | [>] task B <- doing   |
                | [x] task C            |
                +-----------------------+
                            |
                if rounds_since_todo >= 3:
                  inject <reminder>
```

对比 s02：s03 的循环体**变了**——多了 todo 计数器和 nag 提醒注入。

---

## 第 30-37 行：import 和环境

```python
import os
import subprocess
from pathlib import Path

from anthropic import Anthropic
from dotenv import load_dotenv

load_dotenv(override=True)
```

和 s01/s02 一样。`override=True` 意思是 .env 文件的配置覆盖已有的环境变量。

---

## 第 39-40 行：base_url 特殊处理

```python
if os.getenv("ANTHROPIC_BASE_URL"):
    os.environ.pop("ANTHROPIC_AUTH_TOKEN", None)
```

如果用了自定义 base_url（比如代理），就删掉 `ANTHROPIC_AUTH_TOKEN`。因为自定义服务端可能用不同的鉴权方式，留着原来的 token 反而会冲突。`pop` 的第二个参数 `None` 意思是 key 不存在也不报错。

---

## 第 42-48 行：全局初始化

```python
WORKDIR = Path.cwd()
client = Anthropic(base_url=os.getenv("ANTHROPIC_BASE_URL"))
MODEL = os.environ["MODEL_ID"]

SYSTEM = f"""You are a coding agent at {WORKDIR}.
Use the todo tool to plan multi-step tasks. Mark in_progress before starting, completed when done.
Prefer tools over prose."""
```

和 s01/s02 同样的结构，SYSTEM 提示词多了两句：
- "用 todo 工具规划多步任务"
- "开始前标 in_progress，完成后标 completed"
- "优先用工具而非废话"

---

## 第 52-86 行：TodoManager 类（本期重点）

```python
class TodoManager:
    def __init__(self):
        self.items = []
```

**`class` 是什么**：定义一个类型，把数据和操作绑定在一起。

| 语法 | 含义 |
|------|------|
| `class TodoManager:` | 定义一个类 |
| `def __init__(self):` | 构造函数，创建对象时自动调用 |
| `self` | 代表"这个对象本身"，调用方法时 Python 自动传入 |

**类比**：类是蓝图，对象是按蓝图造出来的房子。
- `self` = "这间房子"
- `self.items` = "这间房子的 items 属性"

---

### `update()` 方法（第 56-75 行）：验证 + 更新

```python
def update(self, items: list) -> str:
    if len(items) > 20:
        raise ValueError("Max 20 todos allowed")
```

接收模型传来的 todo 列表，验证后存起来，返回格式化文本。

**第 60 行**：`in_progress_count = 0`，计数器，为了后面判断是否有多个 in_progress。

**第 61-71 行**：逐个验证每个 item：

```python
for i, item in enumerate(items):
```

`enumerate()` 同时拿到索引和值：

```python
for i, item in enumerate(["a", "b", "c"]):
    # i=0,item="a"  →  i=1,item="b"  →  i=2,item="c"
```

**第 62-64 行**：用 `.get()` 安全取值，给默认值：

```python
text   = str(item.get("text", "")).strip()      # 没 text → "" → 转为空字符串
status = str(item.get("status", "pending")).lower()  # 没 status → "pending"
item_id = str(item.get("id", str(i + 1)))       # 没 id → 用序号 "1","2","3"...
```

**为什么用 `.get()`**：s02 学过，key 不存在返回默认值而不报错。

**第 65-68 行**：校验 text 和 status：

```python
if not text:                                    # 空字符串 = False
    raise ValueError(f"Item {item_id}: text required")
if status not in ("pending", "in_progress", "completed"):
    raise ValueError(f"Item {item_id}: invalid status '{status}'")
```

`status not in (...)` 比 `status != ... and status != ...` 简洁。

**第 69-73 行**：最多一个 in_progress：

```python
if status == "in_progress":
    in_progress_count += 1
# ... 循环结束后：
if in_progress_count > 1:
    raise ValueError("Only one task can be in_progress at a time")
```

**第 74-75 行**：验证通过，保存并返回渲染文本：

```python
self.items = validated
return self.render()
```

---

### `render()` 方法（第 77-86 行）：格式化为文本

```python
def render(self) -> str:
    if not self.items:
        return "No todos."
    lines = []
    for item in self.items:
        marker = {"pending": "[ ]", "in_progress": "[>]", "completed": "[x]"}[item["status"]]
        lines.append(f"{marker} #{item['id']}: {item['text']}")
    done = sum(1 for t in self.items if t["status"] == "completed")
    lines.append(f"\n({done}/{len(self.items)} completed)")
    return "\n".join(lines)
```

**第 82 行**：字典映射状态到图标：

```python
# status → marker
# "pending"     → "[ ]"
# "in_progress" → "[>]"
# "completed"   → "[x]"
```

**第 84 行**：`sum(1 for ... if ...)` 计数模式：

```python
sum(1 for t in self.items if t["status"] == "completed")
```

生成器表达式产生 `1, 1, 1,...` 然后 sum 加起来，就是 completed 的个数。

等价于：

```python
count = 0
for t in self.items:
    if t["status"] == "completed":
        count += 1
```

**渲染效果**：

```
[ ] #1: 读代码
[>] #2: 写笔记     ← 当前正在做的
[x] #3: 配置环境   ← 已完成

(1/3 completed)
```

---

## 第 89 行：全局 TodoManager 实例

```python
TODO = TodoManager()
```

只创建一个，整个 agent loop 共用。状态在循环轮次之间保持。

---

## 第 93-139 行：四个文件/命令工具

和 s02 完全一样：`safe_path`, `run_bash`, `run_read`, `run_write`, `run_edit`。s02 已详细解释，不重复。

唯一区别是 s03 的 `run_bash` 多了 try/except 处理超时：

```python
try:
    r = subprocess.run(...)
except subprocess.TimeoutExpired:
    return "Error: Timeout (120s)"
```

---

## 第 141-160 行：TOOL_HANDLERS + TOOLS

和 s02 结构一样，只是**多了 todo**：

```python
TOOL_HANDLERS = {
    "bash":       lambda **kw: run_bash(kw["command"]),
    "read_file":  lambda **kw: run_read(kw["path"], kw.get("limit")),
    "write_file": lambda **kw: run_write(kw["path"], kw["content"]),
    "edit_file":  lambda **kw: run_edit(kw["path"], kw["old_text"], kw["new_text"]),
    "todo":       lambda **kw: TODO.update(kw["items"]),   # ← 新增
}
```

`TODO.update()` 接收 items 列表，验证后存起来，返回格式化文本给模型看。

TOOLS 定义里，todo 是唯一一个复杂嵌套结构的工具：

```python
{"name": "todo", "description": "Update task list...",
 "input_schema": {
     "type": "object",
     "properties": {
         "items": {                          # 参数名：items
             "type": "array",                # 数组类型
             "items": {                      # 数组里每个元素的 schema
                 "type": "object",
                 "properties": {
                     "id":     {"type": "string"},
                     "text":   {"type": "string"},
                     "status": {"type": "string", "enum": ["pending", "in_progress", "completed"]}
                 },
                 "required": ["id", "text", "status"]
             }
         }
     },
     "required": ["items"]
 }}
```

`"enum"` 限定了 status 只能是这三个值之一。API 层面就限制了，模型不会传别的。

---

## 第 164-192 行：agent_loop（本期核心变化）

```python
def agent_loop(messages: list):
    rounds_since_todo = 0              # 新增：计数器
```

### 对比 s01/s02 的变化

```
s01/s02 的循环：
  while True → call API → 收回复 → 执行工具 → 追加结果 → 循环

s03 的循环：
  while True → call API → 收回复 → 执行工具，记录是否用了 todo
           → rounds_since_todo++ 或归零
           → 如果 >= 3，往 results 塞 reminder
           → 追加结果 → 循环
```

### 第 176-188 行：执行工具，追踪 todo

```python
used_todo = False                      # 新变量
for block in response.content:
    if block.type == "tool_use":
        handler = TOOL_HANDLERS.get(block.name)
        try:
            output = handler(**block.input) if handler else f"Unknown tool: {block.name}"
        except Exception as e:
            output = f"Error: {e}"
        print(f"> {block.name}:")
        print(str(output)[:200])       # 终端打印，最多 200 字符
        results.append(...)
        if block.name == "todo":        # 用了 todo？
            used_todo = True            # 标记一下
```

**第 179 行**：`handler = TOOL_HANDLERS.get(block.name)`，如果模型调了一个不存在的工具，handler 是 None，然后被 `if handler else f"Unknown tool"` 兜底。

**第 180-183 行**：try/except 包装执行，即使 handler 抛异常也不会炸掉整个循环。

### 第 189-191 行：Nag 提醒机制

```python
rounds_since_todo = 0 if used_todo else rounds_since_todo + 1
if rounds_since_todo >= 3:
    results.append({"type": "text", "text": "<reminder>Update your todos.</reminder>"})
```

**逻辑**：

```
用 todo 了 → rounds_since_todo = 0      （归零）
没用 todo  → rounds_since_todo + 1     （累加）
                      ↓
           如果 >= 3，塞一句提醒到 results
```

**为什么需要这个**：模型经常"忘记"用 todo。如果连续 3 轮都在干别的没更新任务列表，系统就主动提醒一句。这个提醒是以 `"user"` 角色的 `"text"` 类型注入的——模型收到后就会想起来更新 todo。

**注意**：reminder 追加到 `results` 列表中，和其他 `tool_result` 一起作为 `user` 消息发给模型。

---

## 第 195-211 行：main 交互

```python
if __name__ == "__main__":
    history = []
    while True:
        try:
            query = input("\033[36ms03 >> \033[0m")
        except (EOFError, KeyboardInterrupt):
            break
        if query.strip().lower() in ("q", "exit", ""):
            break
        history.append({"role": "user", "content": query})
        agent_loop(history)
        response_content = history[-1]["content"]
        if isinstance(response_content, list):
            for block in response_content:
                if hasattr(block, "text"):
                    print(block.text)
        print()
```

**`\033[36m`**：ANSI 转义码，青色文字。`\033[0m` 重置颜色。效果是终端显示青色的 `s03 >>` 提示符。

**`isinstance(response_content, list)`**：安全检查。因为 Anthropic API 返回的 content 有时是字符串有时是列表。如果是列表就逐个打印 block.text。

**`hasattr(block, "text")`**：检查 block 对象是否有 `text` 属性。因为有的 block 只有 tool_use 没有 text。

---

## s02 → s03 变化对比

| | s02 | s03 |
|---|---|---|
| 工具数 | 4 | 5 (+ todo) |
| 新 Python 概念 | lambda, **kw, .get() | class, self, `__init__`, enumerate(), sum(1 for...) |
| 循环体 | 不变 | 变了：多了 todo 追踪 + nag 提醒 |
| 新机制 | TOOL_HANDLERS 分发表 | TodoManager 状态管理 + rounds_since_todo 计数器 |
| 状态 | 无（每次循环无状态） | 有（TodoManager 跨轮次保持） |

**s03 的教训**：Agent 不只需要"执行"，还需要"规划"。加上一个 todo 工具，模型就能自己管理进度。但模型会忘，所以用 nag 提醒兜底。

---
#Lang/Python #Domain/Agent #Type/原理 #Session/s03
