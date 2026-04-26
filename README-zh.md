# Prompt Sensei

> 在 AI 时代，写好 prompt 正在变成一项重要的个人能力。Prompt Sensei 不只是帮你改 prompt，也会教你如何提升这个能力。

[English](README.md)

Prompt Sensei 是一个面向 Claude Code 和 Codex 的本地优先 prompt 教练。它会根据你当前所处的阶段给反馈，把粗糙的 prompt 改成更可执行的版本，并帮助你一次练好一个习惯。

没有云端服务。没有遥测。没有排行榜。默认不保存原始 prompt。

---

## 为什么做这个

AI 编程 agent 和普通聊天模型不一样。它会改文件、跑命令、做实现决策。一个含糊的 prompt 不只是得到一个含糊的回答，还可能带来过大的改动、没说清的假设和反复返工。

Prompt Sensei 的核心想法很简单：

- 写 prompt 是一种工程能力。
- 工程能力需要靠反馈来进步。
- 好反馈应该友善、具体、私密、有用。

它不会说 `fix this test` 是“烂 prompt”。它会判断这是一个早期探索 prompt，然后告诉你下一步最值得补什么。

---

## 5 分钟快速开始

安装到 Claude Code：

```bash
git clone https://github.com/chengzhongwei/Prompt-sensei ~/.claude/skills/prompt-sensei
(cd ~/.claude/skills/prompt-sensei && npm install && npm run build)
```

安装到 Codex：

```bash
git clone https://github.com/chengzhongwei/Prompt-sensei ~/.codex/skills/prompt-sensei
(cd ~/.codex/skills/prompt-sensei && npm install && npm run build)
```

启动实时反馈：

```txt
/prompt-sensei observe
```

试着改写一个很常见的弱 prompt：

```txt
/prompt-sensei improve "fix this test"
```

示例输出：

```txt
Prompt Sensei Improve
=====================
Stage:    Exploration
Score:    70 / 100  (Good)

What is missing:
  - failing test name
  - expected behavior
  - actual error output

Improved prompt:
  Help me debug this failing test.

  Test: [test name]
  Expected: [what should happen]
  Actual: [error output or wrong behavior]
  Related file: [file path]

  Return:
  1. Likely root cause
  2. Minimal fix
  3. Test command to verify

Habit to practice next:
  Add expected and actual behavior before asking for a fix.
```

更多可复制的前后对比例子见 [examples/prompt-gallery.md](examples/prompt-gallery.md)。

---

## 命令

```txt
/prompt-sensei [observe|stop|improve|report|help|clear|update]
```

```txt
/prompt-sensei observe              # 开始实时反馈
/prompt-sensei stop                 # 停止本次会话反馈
/prompt-sensei improve "fix this"   # 改写 prompt，并给一个练习建议
/prompt-sensei report               # 查看本地趋势
/prompt-sensei update               # 拉取最新版本并重新构建
/prompt-sensei clear                # 删除本地 Prompt Sensei 数据
/prompt-sensei help
```

在 Codex 里可以用自然语言触发：

```txt
Use prompt-sensei to improve this prompt: "fix this test"
Use prompt-sensei to show my report.
```

---

## 它怎么教你

Prompt Sensei 会先判断阶段。早期探索时，一句话可能已经足够；真正要让 agent 改代码时，就需要更多上下文、边界和验证方式。

| 阶段 | 含义 | 示例 |
|---|---|---|
| Exploration | 还在弄清问题 | `why is this broken` |
| Diagnosis | 已经有现象或证据 | `expected /login, actual /dashboard` |
| Execution | 希望 agent 实现或修改 | `implement this with these constraints` |
| Verification | 希望检查正确性 | `find edge cases and test commands` |
| Reusable workflow | 希望得到可复用流程 | `create a code review checklist` |
| Action | 已有上下文的短指令 | `ok commit and push to main` |

实时反馈会保持很轻：

> **[Sensei: 68/100 · Diagnosis; Tip: add the error message and file path]**

报告关注的是成长，而不是排名：

```txt
Average score:    81 / 100  (Good)
Most common type: implementation
Next habit:       End prompts with the exact test command or edge cases.
```

完整理念见 [docs/philosophy.md](docs/philosophy.md)。评分细节见 [docs/scoring-rubric.md](docs/scoring-rubric.md)。

---

## 可选：Claude Code Hook

你可以把 `UserPromptSubmit` hook 加到 `~/.claude/settings.json`，在同意后后台记录 hash-only 元数据：

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "node ~/.claude/skills/prompt-sensei/dist/scripts/observe.js --hash-only",
            "async": true,
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

Hook 记录不会参与评分，因为 hook 没有足够的对话上下文来判断 prompt 阶段和反馈建议。

---

## 隐私

Prompt 往往包含业务逻辑、调试细节和未完成的想法，所以 Prompt Sensei 在你同意之前不会保存任何数据。

同意后，它只保存本地元数据：

- `~/.prompt-sensei/events.jsonl` — 观察日志
- `~/.prompt-sensei/config.json` — 同意记录
- `~/.prompt-sensei/update-check.json` — 更新检查缓存

默认不保存原始 prompt，也不会把 prompt、分数、报告或本地日志发送到任何服务。

更多细节见 [docs/privacy.md](docs/privacy.md)。

---

## 贡献

适合入手的方向：

- 更真实的 prompt 改进示例
- 评分规则改进
- 敏感信息脱敏规则
- 报告体验改进
- 支持更多 AI 编程工具

请保持这个项目的核心气质：安静、本地优先、鼓励式、注重隐私。

---

## License

Apache-2.0 — Copyright 2026 Chengzhong Wei
