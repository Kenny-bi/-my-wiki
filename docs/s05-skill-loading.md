---
title: s05 - Skill Loading 按需加载技能
date: 2026-05-03
tags: [Lang/Python, Domain/Agent, Type/原理, Status/已理解]
session: s05
---

# s05 - Skill Loading 按需加载技能

## 核心思想

s05 解决的问题是：**不要把所有专业知识都塞进 system prompt，而是在模型需要时按需加载。**

两层注入架构：

```
Layer 1（便宜）：system prompt 只放技能名 + 一句话描述（~100 tokens/skill）
Layer 2（按需）：模型调用 load_skill("pdf") 时，完整技能指令通过 tool_result 返回

┌─────────────────────────────────┐
│ System Prompt                   │
│                                 │
│ Skills available:               │
│   - pdf: Process PDF files...   │  ← Layer 1: 只有名字和描述
│   - code-review: Review code... │     每轮对话都会发送，所以要短
│                                 │
└─────────────────────────────────┘

模型决定需要 PDF 技能 → 调用 load_skill("pdf")

┌─────────────────────────────────┐
│ tool_result                     │
│ <skill name="pdf">              │
│   Full PDF processing instrs    │  ← Layer 2: 完整的几十行技能指令
│   Step 1: ...                   │     只在需要时加载一次
│   Step 2: ...                   │
│ </skill>                        │
└─────────────────────────────────┘
```

**为什么要分两层**：system prompt 里的每个 token 在**每一轮对话**都会发送。一个技能可能几百行，塞 10 个技能就是几千 token 的固定开销，大部分任务只用 1-2 个，完全浪费。

---

## 第 1-36 行：文件头注释

```python
"""
s05_skill_loading.py - Skills

Two-layer skill injection that avoids bloating the system prompt:

    Layer 1 (cheap): skill names in system prompt (~100 tokens/skill)
    Layer 2 (on demand): full skill body in tool_result

    skills/
      pdf/
        SKILL.md          <-- frontmatter (name, description) + body
      code-review/
        SKILL.md

Key insight: "Don't put everything in the system prompt. Load on demand."
"""
```

注释里画了两层架构的图，最后一句总结：**不要把所有东西放 system prompt，按需加载。**

---

## 第 38-55 行：import 和初始化

```python
import os
import re
import subprocess
import yaml
from pathlib import Path

from anthropic import Anthropic
from dotenv import load_dotenv

load_dotenv(override=True)

if os.getenv("ANTHROPIC_BASE_URL"):
    os.environ.pop("ANTHROPIC_AUTH_TOKEN", None)

WORKDIR = Path.cwd()
client = Anthropic(base_url=os.getenv("ANTHROPIC_BASE_URL"))
MODEL = os.environ["MODEL_ID"]
SKILLS_DIR = WORKDIR / "skills"
```

和 s03/s04 一样的初始化。**新增两样**：

| 新增 | 说明 |
|------|------|
| `import re` | 正则表达式，用来解析 SKILL.md 的 YAML 头 |
| `import yaml` | PyYAML 库，把 YAML 文本变成 Python 字典 |
| `SKILLS_DIR` | 技能文件目录，`Path.cwd() / "skills"` |

**`re` 是什么**：Python 内置的正则表达式模块。`re.match(正则, 文本)` 从文本开头匹配。

**`yaml` 是什么**：第三方库，`pip install pyyaml`。`yaml.safe_load("name: pdf")` 返回 `{"name": "pdf"}`。带 `safe` 的版本只解析基本类型，不执行任意代码，比 `yaml.load()` 安全。

---

## 第 58-107 行：SkillLoader 类（本期重点）

```python
class SkillLoader:
    def __init__(self, skills_dir: Path):
        self.skills_dir = skills_dir
        self.skills = {}           # 字典: {技能名: {meta, body, path}}
        self._load_all()
```

**类的职责**：扫描 `skills/` 目录下的所有 `SKILL.md`，解析后存在内存里。

**`_load_all` 前面的下划线**：Python 约定，以 `_` 开头的方法表示"内部方法，外部不要直接调用"。这只是约定，Python 不会强制。

---

### `_load_all` 方法（第 65-72 行）：扫描所有技能文件

```python
def _load_all(self):
    if not self.skills_dir.exists():
        return
    for f in sorted(self.skills_dir.rglob("SKILL.md")):
        text = f.read_text()
        meta, body = self._parse_frontmatter(text)
        name = meta.get("name", f.parent.name)
        self.skills[name] = {"meta": meta, "body": body, "path": str(f)}
```

逐行拆解：

**第 66-67 行**：目录不存在就直接返回，不报错。防御性编程。

**第 68 行**：`rglob("SKILL.md")` 递归搜索所有子目录中叫 `SKILL.md` 的文件。

```
skills/
├── pdf/SKILL.md              ← 找到
├── code-review/SKILL.md      ← 找到
├── agent-builder/SKILL.md    ← 找到
├── mcp-builder/SKILL.md      ← 找到
└── agent-builder/references/ ← 没有 SKILL.md，跳过
```

**`sorted()`**：保证每次加载顺序一致。不同操作系统 `rglob` 返回的顺序可能不同，sorted 消除这个不确定性。

**第 71 行**：`f.parent.name` 取父目录名作为备选技能名。

```python
# 如果 SKILL.md 里没有写 name: xxx，就用文件夹名
# skills/pdf/SKILL.md → f.parent = skills/pdf → f.parent.name = "pdf"
```

**第 72 行**：每个技能存三个字段：

```python
{
    "meta": {"name": "pdf", "description": "Process PDF files..."},  # YAML 头解析出的字典
    "body": "# PDF Processing Skill\nYou now have...",              # 正文（去掉 YAML 头后的部分）
    "path": "D:/AI_Projects/learn-claude-code/skills/pdf/SKILL.md"  # 文件路径，调试用
}
```

---

### `_parse_frontmatter` 方法（第 74-83 行）：解析 YAML 头

```python
def _parse_frontmatter(self, text: str) -> tuple:
    match = re.match(r"^---\n(.*?)\n---\n(.*)", text, re.DOTALL)
    if not match:
        return {}, text
    try:
        meta = yaml.safe_load(match.group(1)) or {}
    except yaml.YAMLError:
        meta = {}
    return meta, match.group(2).strip()
```

**SKILL.md 的结构**：

```
---                        ← 开始分隔线
name: pdf                  ← YAML frontmatter
description: Process PDF files...
---                        ← 结束分隔线

# PDF Processing Skill     ← 正文 body
You now have expertise...
```

**正则拆解**：`r"^---\n(.*?)\n---\n(.*)"` 

| 部分 | 匹配什么 |
|------|---------|
| `^---\n` | 开头的 `---` 加换行 |
| `(.*?)` | 第 1 组：YAML 内容，非贪婪（尽可能少匹配） |
| `\n---\n` | 中间的 `---` 分隔线 |
| `(.*)` | 第 2 组：正文 body |

**`re.DOTALL`**：让 `.` 也能匹配换行符。没有这个标志，`.` 只匹配非换行字符，`(.*?)` 就匹配不了跨行的 YAML 内容。

**第 78 行**：`match.group(1)` 是第 1 组（YAML），`match.group(2)` 是第 2 组（正文）。

**第 79-81 行**：双重防御：
- `yaml.safe_load(...)` 解析 YAML → 字典
- `or {}`：如果 `safe_load` 返回 None（空 YAML），用空字典
- `try/except`：YAML 格式错误也不崩溃，返回空字典

**返回值**：`(meta字典, 正文字符串)`。s03 学过 Python 函数可以返回 tuple。

---

### `get_descriptions` 方法（第 85-97 行）：生成 Layer 1

```python
def get_descriptions(self) -> str:
    if not self.skills:
        return "(no skills available)"
    lines = []
    for name, skill in self.skills.items():
        desc = skill["meta"].get("description", "No description")
        tags = skill["meta"].get("tags", "")
        line = f"  - {name}: {desc}"
        if tags:
            line += f" [{tags}]"
        lines.append(line)
    return "\n".join(lines)
```

**输出示例**：

```
  - pdf: Process PDF files - extract text, create PDFs, merge documents. Use when user asks to read PDF, create PDF, or work with PDF files.
  - code-review: Perform thorough code reviews with security, performance, and maintainability analysis. Use when user asks to review code, check for bugs, or audit a codebase.
  - agent-builder: ...
  - mcp-builder: ...
```

每条一行，name + description + 可选的 tags。这些会被插入 system prompt。

---

### `get_content` 方法（第 99-104 行）：返回 Layer 2

```python
def get_content(self, name: str) -> str:
    skill = self.skills.get(name)
    if not skill:
        return f"Error: Unknown skill '{name}'. Available: {', '.join(self.skills.keys())}"
    return f"<skill name=\"{name}\">\n{skill['body']}\n</skill>"
```

**第 102 行**：`self.skills.get(name)` 找不到返回 None，不会报 KeyError。

**第 103 行**：错误消息里列出所有可用技能名，帮助模型（或开发者）知道能加载哪些。

**第 104 行**：用 `<skill>` XML 标签包裹正文。Claude API 推荐用 XML 标签结构化内容，模型更容易理解边界。

```xml
<skill name="pdf">
  # PDF Processing Skill
  You now have expertise in PDF manipulation...
  ...（完整的几十行指令）
</skill>
```

---

## 第 107 行：全局实例

```python
SKILL_LOADER = SkillLoader(SKILLS_DIR)
```

程序启动时创建一个 SkillLoader，扫描所有 SKILL.md 并加载到内存。之后 `load_skill` 工具直接从内存取，不用重复读文件。

---

## 第 109-114 行：System Prompt（注入 Layer 1）

```python
SYSTEM = f"""You are a coding agent at {WORKDIR}.
Use load_skill to access specialized knowledge before tackling unfamiliar topics.

Skills available:
{SKILL_LOADER.get_descriptions()}"""
```

f-string 里直接嵌入 `get_descriptions()` 的输出。模型在第一轮对话就能看到有哪些技能可用。

效果：

```
You are a coding agent at D:\AI_Projects\learn-claude-code.
Use load_skill to access specialized knowledge before tackling unfamiliar topics.

Skills available:
  - pdf: Process PDF files...
  - code-review: Perform thorough code reviews...
  - agent-builder: ...
```

---

## 第 117-163 行：五个工具函数

和 s04 完全一样：`safe_path`, `run_bash`, `run_read`, `run_write`, `run_edit`。不重复。

---

## 第 166-172 行：TOOL_HANDLERS

```python
TOOL_HANDLERS = {
    "bash":       lambda **kw: run_bash(kw["command"]),
    "read_file":  lambda **kw: run_read(kw["path"], kw.get("limit")),
    "write_file": lambda **kw: run_write(kw["path"], kw["content"]),
    "edit_file":  lambda **kw: run_edit(kw["path"], kw["old_text"], kw["new_text"]),
    "load_skill": lambda **kw: SKILL_LOADER.get_content(kw["name"]),   # ← 新增！
}
```

**关键区别**：`load_skill` 不执行任何命令，不读文件，不做 IO——只是从内存字典里取一段预加载好的文本。所以它是五个工具里最快的。

---

## 第 174-185 行：TOOLS 定义

```python
TOOLS = [
    {"name": "bash", ...},
    {"name": "read_file", ...},
    {"name": "write_file", ...},
    {"name": "edit_file", ...},
    {"name": "load_skill",
     "description": "Load specialized knowledge by name.",
     "input_schema": {
         "type": "object",
         "properties": {
             "name": {"type": "string", "description": "Skill name to load"}
         },
         "required": ["name"]
     }},
]
```

**`name` 参数有 description**：`"Skill name to load"`。这是给模型看的提示，帮助模型理解应该传什么值。之前四个工具的参数没有 description，因为 `command`、`path` 这些已经足够自明。

---

## 第 188-208 行：agent_loop

```python
def agent_loop(messages: list):
    while True:
        response = client.messages.create(
            model=MODEL, system=SYSTEM, messages=messages,
            tools=TOOLS, max_tokens=8000,
        )
        messages.append({"role": "assistant", "content": response.content})
        if response.stop_reason != "tool_use":
            return
        results = []
        for block in response.content:
            if block.type == "tool_use":
                handler = TOOL_HANDLERS.get(block.name)
                try:
                    output = handler(**block.input) if handler else f"Unknown tool: {block.name}"
                except Exception as e:
                    output = f"Error: {e}"
                print(f"> {block.name}:")
                print(str(output)[:200])
                results.append({"type": "tool_result", "tool_use_id": block.id, "content": str(output)})
        messages.append({"role": "user", "content": results})
```

和 s02 的循环结构一样，没有新逻辑。`load_skill` 工具的处理走的是同一个 `TOOL_HANDLERS` 分发表。

---

## 第 211-227 行：main 入口

```python
if __name__ == "__main__":
    history = []
    while True:
        try:
            query = input("\033[36ms05 >> \033[0m")
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

和 s03/s04 完全一样，只是提示符从 `s03 >>` 变成了 `s05 >>`。

---

## 完整数据流示例

用户问 "帮我 review 一下 main.py"：

```
1. 用户输入 → history.append(user message)

2. API 调用，system prompt 包含 Layer 1:
   "Skills available:
     - pdf: Process PDF files...
     - code-review: Perform thorough code reviews..."

3. 模型思考: "我需要 code-review 技能"
   → 调用 load_skill("code-review")

4. handler 执行: SKILL_LOADER.get_content("code-review")
   → 返回完整的 code-review 检查清单（Layer 2，100+ 行）

5. tool_result 发回 API
   → 模型现在有了完整的 review 方法论

6. 模型再调用 read_file("main.py") 读取代码

7. 模型根据技能指令 + 代码内容，输出 review 结果
```

**关键点**：如果用户问的是 "帮我写个脚本"，模型可能根本不调用 load_skill——省下了加载 code-review 技能的 token。**按需加载的核心价值：用不到就不花钱。**

---

## s04 → s05 变化对比

| | s04 | s05 |
|---|---|---|
| 新增 import | 无 | `re`, `yaml` |
| 新增类 | 无 | `SkillLoader`（扫描/解析/查询） |
| 新增工具 | task（派子 agent） | load_skill（加载技能文本） |
| System Prompt | 静态文本 | 动态拼接技能描述 |
| Agent 数量 | 2（父子） | 1 |
| 外部数据 | 无 | `skills/` 目录下的 SKILL.md 文件 |
| 新 Python | `_` 占位符, 列表相加 | `re.match()`, `yaml.safe_load()`, `rglob()`, `re.DOTALL` |
| 核心机制 | 上下文隔离 | 延迟加载（Lazy Loading） |

---

## s05 的设计智慧

1. **延迟加载**：只在实际需要时才加载完整知识，节省 token 成本
2. **关注点分离**：技能定义（SKILL.md）和 Agent 逻辑（Python 代码）完全解耦
3. **零代码扩展**：添加新技能只需在 `skills/` 下新建文件夹 + SKILL.md，不改一行 Python
4. **YAML frontmatter**：用标准格式存元数据，解析简单，人类可读

---

## 和 Claude Code 真实产品的对应

| s05 概念 | Claude Code 对应 |
|----------|-----------------|
| `skills/` 目录 | Claude Code 的 Skills 插件系统 |
| `SKILL.md` 的 frontmatter | Skills 列表里看到的名称和描述 |
| `get_descriptions()` → system prompt | 系统提示中列出的可用 Skills |
| `load_skill()` 工具调用 | 模型决定使用某个 Skill 时的加载动作 |
| `get_content()` 返回的 body | 完整的技能指令（如 PDF 处理步骤） |

Claude Code 的 `/skill` 命令触发就是这个模式的完整实现。

---
#Lang/Python #Domain/Agent #Type/原理 #Session/s05
