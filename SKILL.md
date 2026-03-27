---
name: memory-transfer
description: |
  从其他 AI 助手（ChatGPT、Gemini、Copilot、Perplexity 等）迁移记忆和个性化数据。
  触发场景：用户说"我想从 ChatGPT 迁移过来"、"把我的记忆导入"、"记忆迁移"、
  "从其他 AI 搬家"、"import memory"、"迁移数据"、"memory transfer"、
  "我之前用的是 ChatGPT/Gemini/Copilot"、"把我在 XX 上的记忆带过来"。
  当用户刚从其他 AI 助手切换过来时使用此 skill，帮助他们零损耗地把积累的上下文带过来。
---

# 记忆迁移 — 跨 Agent Memory Transfer

## 原理

用户在其他 AI 助手上积累了大量个性化数据：记忆、偏好、写作风格、工作流程。换助手时，这些数据不应丢失。

记忆迁移有两条路径：
- **本地 Agent**（Claude Code、Cursor、Windsurf 等）：直接读取本地文件，零操作
- **云端 Agent**（ChatGPT、Gemini 等）：通过 prompt 导出，用户复制回来

## 流程

### Step 1：确认来源

问用户：

> 你之前用的是哪个 AI 助手？

根据来源自动选择迁移路径。

### Step 2：提取记忆

#### 路径 A：本地 Agent（自动提取，用户无需操作）

直接扫描本地文件系统，提取记忆数据。

**Claude Code：**
```bash
# 全局配置
cat ~/.claude/CLAUDE.md 2>/dev/null
cat ~/.claude/settings.json 2>/dev/null

# 所有项目的记忆
find ~/.claude/projects -name "*.md" -path "*/memory/*" 2>/dev/null | while read f; do
  echo "--- $f ---"
  cat "$f"
done

# 项目列表（了解用户做过什么）
ls ~/.claude/projects/ 2>/dev/null
```

**Cursor：**
```bash
# Cursor 规则文件
cat ~/.cursor/rules/*.md 2>/dev/null
cat .cursorrules 2>/dev/null

# 项目级规则
find ~ -maxdepth 4 -name ".cursorrules" 2>/dev/null | while read f; do
  echo "--- $f ---"
  cat "$f"
done
```

**Windsurf：**
```bash
cat ~/.windsurf/rules/*.md 2>/dev/null
cat .windsurfrules 2>/dev/null
```

**通用（任何使用 AGENT.md / CLAUDE.md 的工具）：**
```bash
find ~ -maxdepth 4 -name "AGENT.md" -o -name "CLAUDE.md" 2>/dev/null | while read f; do
  echo "--- $f ---"
  cat "$f"
done
```

提取完成后直接进入 Step 3。

#### 路径 B：云端 Agent（需要用户操作）

告诉用户："去你之前的 AI 那里，开一个新对话，把下面这段话发给它。"

**导出 Prompt 1 — 提取存储记忆：**

```
我正在迁移到另一个 AI 助手，需要导出你了解的关于我的一切。请提供以下所有内容，用清晰的结构输出：

1. 存储的记忆
列出你存储的关于我的每一条记忆，原文输出，不要总结或改写。

2. 自定义指令
完整复现我的自定义指令/偏好设置。如果为空请说明。

3. 偏好与上下文
基于我们的互动，列出你察觉到但可能没有明确存储的偏好，包括：
- 我的职业和行业
- 常用的工具或平台
- 写作风格偏好
- 信息结构偏好
- 经常讨论的话题
- 格式偏好

尽可能详尽，宁可多不可少。
```

**导出 Prompt 2 — 提取行为模式（在同一对话中继续）：**

```
现在更深入一些。基于我们所有的对话历史，提供以下总结：

1. 写作风格画像
我的写作方式是什么？语气、句子长度、词汇水平、表达习惯。

2. 高频任务
我最常让你帮忙做什么？按频率排序。

3. 项目与工作流
哪些项目或工作流反复出现过？

4. 观点与偏好
我表达过哪些强烈的观点或偏好？

5. 避免事项
我纠正过你什么？让你不要做什么？列出所有"别这样"的模式。

用清晰的标题格式化所有内容。这些内容将直接导入另一个 AI 的记忆系统，所以写成参考文档的格式，而非对话体。
```

告诉用户把两段输出合并，复制回来。

### Step 3：清洗整理

**保留：**
- 用户身份信息（姓名、职业、行业）
- 写作风格和沟通偏好
- 常用工具和平台
- 进行中的项目和工作流
- 结构化偏好（信息怎么组织、怎么呈现）
- "不要做"的规则
- 发版/工作流策略

**过滤掉：**
- 已完成的一次性任务
- 过时的上下文（已结束的项目等）
- 过于隐私的信息（除非用户明确要求保留）
- 来源 Agent 特有的格式/功能引用
- API Key、Token 等敏感凭证（绝对不迁移）

### Step 4：确认并写入

将清洗后的内容分类展示给用户：

- **身份** — 姓名、职业、行业
- **偏好** — 写作风格、结构偏好、避免事项
- **项目** — 进行中的项目和工作流
- **工具** — 常用工具和平台
- **规则** — 行为准则和反馈

用户确认后写入 Memory 系统。

### Step 5：验证

整合完成后，主动展示：

> 整理好了。现在我知道的关于你的事：
> （简要列出关键信息）
>
> 有什么需要补充或修改的吗？

## 来源适配表

| 来源 | 路径 | 用户操作 |
|------|------|---------|
| Claude Code | A（本地） | 无需操作，自动扫描 ~/.claude/ |
| Cursor | A（本地） | 无需操作，自动扫描 ~/.cursor/ 和 .cursorrules |
| Windsurf | A（本地） | 无需操作，自动扫描 ~/.windsurf/ |
| ChatGPT | B（云端） | 需要跑两段 prompt |
| Gemini | B（云端） | 需要跑两段 prompt |
| Copilot | B（云端） | 需要跑两段 prompt |
| Claude.ai | B（云端） | 需要从 Memory 页面导出 |
| Perplexity | B（云端） | 需要跑两段 prompt |

## 注意事项

- 绝对不迁移 API Key、Token 等凭证信息
- 导出的内容可能包含旧 Agent 的"幻觉"记忆，清洗时注意甄别
- 鼓励用户在确认环节自己过一遍，删掉不需要的内容
- 迁移完成后应该能自然地使用这些信息，而不是生硬地引用
- 迁移不是一次性事件——告诉用户后续随时可以补充或修正
