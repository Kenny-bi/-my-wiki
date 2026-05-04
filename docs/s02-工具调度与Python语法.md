---
title: s02 - 工具调度与 Python 语法
date: 2026-04-16
tags: [Lang/Python, Domain/Agent, Type/原理, Status/已理解]
session: s02
---

# s02 - 工具调度与 Python 语法

## s02 的核心思想

s01 的循环体完全没变。只是多加了工具 + 一个分发表。

```
s01: 1 个工具 (bash)  → 硬编码调用 run_bash
s02: 4 个工具          → 用字典 TOOL_HANDLERS 分发
```

---

## Python 语法详解

### 1. 类型注解（`p: str`、`-> Path`）

```python
def safe_path(p: str) -> Path:
```

纯给人看的提示，Python 运行时完全忽略：

| 部分 | 含义 |
|------|------|
| `p: str` | 参数 p 是字符串类型 |
| `-> Path` | 函数返回 Path 对象 |

```python
# 这两个完全等价
def safe_path(p: str) -> Path:   # 有注解
def safe_path(p):                 # 没注解，一样跑
```

---

### 2. Path 的 `/` 不是除法，是路径拼接

```python
WORKDIR = Path.cwd()          # Path 对象，不是字符串
path = (WORKDIR / p).resolve()
```

`pathlib` 库重载了 `/` 运算符，用来拼接路径：

```python
Path("D:/proj") / "notes/a.md"
# → Path("D:/proj/notes/a.md")

# 等价于
os.path.join("D:/proj", "notes/a.md")
```

`.resolve()` 把路径绝对化，解析掉 `..` 和 `.`：

```python
Path("D:/proj/../test").resolve()   # → Path("D:/test")
```

---

### 3. lambda 表达式（匿名函数）

```python
lambda **kw: run_bash(kw["command"])
```

`lambda` 就是一行写完的匿名函数。等价于：

```python
def 匿名函数(**kw):
    return run_bash(kw["command"])
```

**语法**：`lambda 参数: 返回值`

示例对比：

```python
# lambda 写法
f = lambda x: x * 2
f(5)    # → 10

# def 等价写法
def f(x):
    return x * 2
f(5)    # → 10
```

**为什么这里用 lambda？** 因为每个工具的处理逻辑就一行，没必要单独定义函数。

---

### 4. `**kw`（关键字参数打包）

```python
lambda **kw: run_bash(kw["command"])
```

`**kw` 把所有传入的参数打包成一个字典：

```python
def f(**kw):
    print(kw)

f(a=1, b=2)    # → {"a": 1, "b": 2}
f(name="test")  # → {"name": "test"}
```

反向操作是 `**dict` 拆开：

```python
d = {"command": "ls"}
run_bash(**d)   # 等价于 run_bash(command="ls")
```

---

### 5. 默认参数值（`limit: int = None`）

```python
def run_read(path: str, limit: int = None) -> str:
```

`limit = None` 意味着调用时可以不传这个参数：

```python
run_read("a.txt")           # limit 取默认值 None，读全部
run_read("a.txt", limit=10) # limit 取 10，只读前 10 行
```

---

### 6. `kw.get("limit")` vs `kw["limit"]`

```python
lambda **kw: run_read(kw["path"], kw.get("limit"))
```

| 写法 | key 不存在时 | 用在 |
|------|-------------|------|
| `kw["limit"]` | 报 KeyError | 必填参数 |
| `kw.get("limit")` | 返回 None | 可选参数 |

`path` 是必填的所以用 `kw["path"]`，`limit` 可选所以用 `kw.get("limit")`。

---

## TOOL_HANDLERS 分发表详解

```python
TOOL_HANDLERS = {
    "bash":       lambda **kw: run_bash(kw["command"]),
    "read_file":  lambda **kw: run_read(kw["path"], kw.get("limit")),
    "write_file": lambda **kw: run_write(kw["path"], kw["content"]),
    "edit_file":  lambda **kw: run_edit(kw["path"], kw["old_text"], kw["new_text"]),
}
```

本质：字典，键是工具名，值是处理函数。

调用时（第 126-127 行）：

```python
handler = TOOL_HANDLERS.get(block.name)          # 按名字找到对应函数
output = handler(**block.input)                  # 拆开参数字典传入
# 例：block.name = "bash", block.input = {"command": "ls"}
# → handler = lambda **kw: run_bash(kw["command"])
# → handler(command="ls") → run_bash("ls")
```

如果不用 lambda + **kw，等价写法：

```python
def handle_bash(**kw):
    return run_bash(kw["command"])

def handle_read(**kw):
    return run_read(kw["path"], kw.get("limit"))

TOOL_HANDLERS = {
    "bash": handle_bash,
    "read_file": handle_read,
    ...
}
```

---

## safe_path 安全检查

```python
def safe_path(p: str) -> Path:
    path = (WORKDIR / p).resolve()
    if not path.is_relative_to(WORKDIR):
        raise ValueError(f"Path escapes workspace: {p}")
    return path
```

防止模型读取项目外的文件：

```python
# 正常情况
safe_path("notes/a.md")
# → Path("D:/proj/notes/a.md")  ✓ 在工作目录内

# 恶意情况
safe_path("../../etc/passwd")
# → Path("C:/etc/passwd")
# → is_relative_to(WORKDIR) = False → 报错拦截！
```

---

## s01 → s02 的变化对比

| | s01 | s02 |
|---|---|---|
| 工具数量 | 1 (bash) | 4 (bash/read/write/edit) |
| 调用方式 | 硬编码 `if block.type == "tool_use"` | `TOOL_HANDLERS[block.name](**input)` |
| 循环体 | 不变 | 不变 |

**s02 的教训**：加工具不需要改循环，只需要 1) 写处理函数 2) 加到分发表 3) 加到 TOOLS 定义。

---

## 2026-05-01：SDK 是什么

### 问题

Anthropic SDK 是啥？跟 API 什么关系？

### 答案

**SDK** = Software Development Kit（软件开发工具包），就是别人写好的代码库，让你直接调用，不用从零写。

**调用链**：

```
你的代码: client.messages.create(...)   ← 一行调用
    │
    ▼
Anthropic SDK: 把参数转成 HTTP 请求       ← SDK 干的事
    │
    ▼
网络: POST https://api.anthropic.com/...  ← 发到服务器
    │
    ▼
Claude 服务器: 推理，返回结果              ← API 干活
```

**没有 SDK 的话**，你得自己写 HTTP 请求：

```python
import requests
response = requests.post(
    "https://api.anthropic.com/v1/messages",
    headers={"x-api-key": "...", "anthropic-version": "2023-06-01"},
    json={"model": "claude-sonnet-4-6", "messages": [...], "max_tokens": 8000}
)
```

SDK 把 HTTP 请求、认证、重试、错误处理这些琐碎细节封装好，给你干净的 `client.messages.create()` 用。

**类比**：SDK 是现成的工具箱，API 是工具箱里的工具，你不需要自己打铁造工具。

---

## 2026-05-01：subprocess 是什么

### 问题

`subprocess` 是啥？

### 答案

Python 标准库模块，作用：**在 Python 代码里执行 shell 命令**。

```python
r = subprocess.run(
    command,             # 要执行的命令，比如 "ls"
    shell=True,          # 通过 shell 执行（支持管道 |、重定向 >）
    cwd=os.getcwd(),     # 在哪个目录执行
    capture_output=True, # 抓取命令的输出
    text=True,           # 输出是字符串（不然是字节 bytes）
    timeout=120          # 超过 120 秒就杀掉
)
```

本质：Python → 开一个子进程 → 执行命令 → 把结果拿回来。

类比：你在终端里敲 `ls` 看结果，`subprocess.run()` 就是 Python 替你敲了这个命令，然后把终端显示的文字抓回 Python 变量里。

---

## 2026-05-01：`any(d in command for d in dangerous)` 拆解

### 问题

这行判断啥意思？

```python
if any(d in command for d in dangerous):
```

### 答案

逐层拆解：

```python
dangerous = ["rm -rf /", "sudo", "shutdown", "reboot", "> /dev/"]

# 1. for d in dangerous → 遍历危险词列表
# 2. d in command      → 检查这个危险词是否出现在用户命令里
# 3. any(...)          → 只要有一个 True，整体就是 True
```

手动等价写法：

```python
def 手动版(command):
    for d in dangerous:       # 逐个检查
        if d in command:      # 出现在命令里？
            return True       # 中一个就立刻 True（短路）
    return False              # 全没中，False
```

`any()` 就是把这个循环压缩成一行，**短路逻辑**：一旦找到匹配就立即返回 True，后面不再检查。

---
#Lang/Python #Domain/Agent #Type/原理 #Session/s02
