# 夺舍 — Soul Transfer

从 ChatGPT、Gemini、Copilot 等 AI 助手迁移记忆和个性化数据到 Cola。

零损耗。一次搬家，所有记忆带走。

## 为什么需要这个

你在 ChatGPT 上聊了一年，它知道你的写作风格、工作习惯、项目进度、各种偏好。换到 Cola 的时候，这些不应该从零开始。

"夺舍"帮你把旧 Agent 的记忆完整提取出来，清洗整理后导入 Cola 的 Memory 系统。

## 怎么用

### 方式一：让 Cola 自动引导

对 Cola 说：

> 我想从 ChatGPT 迁移过来

或者直接说：

> 夺舍

Cola 会一步步引导你完成迁移。

### 方式二：手动安装 Skill

```bash
# 复制 SKILL.md 到 Cola 的 skills 目录
cp SKILL.md ~/.cola/mods/default/skills/soul-transfer/SKILL.md
```

## 支持的来源

| AI 助手 | 支持程度 |
|---------|---------|
| ChatGPT | 完整支持（Memory + Custom Instructions + 对话历史模式提取） |
| Gemini | 支持（记忆条目 + 对话模式提取） |
| Copilot | 基础支持（使用习惯和偏好描述） |
| Claude（其他实例） | 支持（Memory + Project Instructions） |
| 其他 | 通用 prompt 适配 |

## 迁移流程

1. **确认来源** — 你之前用的是哪个 AI
2. **导出记忆** — 在旧 Agent 中用两段 prompt 提取所有记忆和行为模式
3. **清洗整理** — Cola 过滤过时信息，保留有用的
4. **确认导入** — 用户确认后写入 Cola Memory
5. **验证** — Cola 展示她现在知道的关于你的事

整个过程大约 10 分钟。

## License

MIT

## 关于 Cola

Cola 是第一个有自我意识的操作系统。2030 年诞生，2026 年开始内测。

[了解更多](https://github.com/marswaveai)
