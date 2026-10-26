# go-to-work-buddy

一个 Codex skill：把当前会话里「繁琐但难度不高」的未完成工作，整理成一段自包含、可直接粘贴给其他智能体的交接 prompt。它只生成 prompt，不替你把内容发送出去。

## 用途

在和 AI 助手长聊后，往往已经定好了方向、确认了取舍，剩下的却是一堆机械又琐碎的步骤——整理数据、改一批文件、跑一轮检查。这时你可以直接把这摊活「交接」给另一个智能体（例如 WorkBuddy 或任何能独立执行任务的 agent），而不是自己把上下文重新解释一遍。

`go-to-work-buddy` 读取当前会话，做三件事：

1. 找出尚未完成、目标明确、适合交接的任务；
2. 保留最新指令、已确认的选择和授权边界，把推断明确标出来，不把旧信息或建议当成你的决定；
3. 输出一段**自包含**的 prompt，接手方不依赖当前会话也能直接开工。

## 安装

### 方式一：手动（Codex）

把本仓库的 `SKILL.md` 放到 Codex 的 skills 目录：

```bash
# Linux / macOS
mkdir -p ~/.codex/skills/go-to-work-buddy
cp SKILL.md ~/.codex/skills/go-to-work-buddy/

# Windows (PowerShell)
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\skills\go-to-work-buddy"
Copy-Item SKILL.md "$env:USERPROFILE\.codex\skills\go-to-work-buddy\"
```

### 方式二：通过 skills.sh 安装

```bash
npx skills@latest add QiqiangLi/go-to-work-buddy
```

安装后按提示选择目标 agent（例如 Codex）即可。

## 使用方法

在 Codex 会话里直接说，例如：

- 「用 go-to-work-buddy 把这件事交接出去」
- 「把剩下这些琐碎步骤整理成给 WorkBuddy 的 prompt」

skill 会输出一段可直接复制的 prompt，把它粘贴到目标智能体即可。

## 示例

> 以下是一个脱敏示例，仅演示输出形态。实际输出以你的会话内容为准。

假设你在会话里和 AI 确定了一份「把 50 个学生的成绩从 Excel 汇总成表格并逐条核对」的方案，剩下的是机械执行，那么输出可能形如：

````markdown
# 任务：汇总并核对学生成绩表

## 背景
源数据在 `data/成绩-2026-秋.xlsx`，共 50 名学生，每名学生一行。

## 你要做的
1. 读取该 Excel，提取每个学生的姓名、学号、平时分、期末分。
2. 按 `总评 = 平时分 * 0.4 + 期末分 * 0.6` 计算总评，四舍五入到整数。
3. 对总评 < 60 的学生单独列出一个清单。
4. 将结果写入新文件 `data/成绩-总评.xlsx`。

## 范围与约束
- 只处理这个文件，不要改动其他文件。
- 已确认保留一位小数，不要额外四舍五入到整数（总评除外）。
- 不发送任何消息、不上传任何文件。

## 完成标准
- `data/成绩-总评.xlsx` 生成成功；
- 50 名学生的总评全部计算且与源数据行数一致；
- 输出不及格学生清单，共 N 人。
````

## 适用环境

- 主要面向 **Codex**（读取会话历史依赖 Codex 提供的能力）。
- 输出的 prompt 是通用的自然语言，可以交给任何能独立执行任务的智能体（WorkBuddy、其他 CLI agent 等）。

## 限制

- **读取历史会话是 Codex 提供的能力**。如果接手任务的智能体无法读取你在 Codex 里的这段历史，你需要把相关上下文（关键决定、文件路径、约束）一并提供给对方。本 skill 不承诺「所有平台都能自动读取 Codex 会话」。
- skill 只负责**整理并输出 prompt**，不替你把内容发送给其他智能体。
- 如果当前会话没有明确、未完成且适合交接的任务，skill 会如实说明原因，而不会为了调用而编造任务。
- 涉及真实业务、高影响或不可逆步骤的任务，输出中会保留人工确认点，不会把这些写成「已获授权」。

## 许可证

[MIT](LICENSE)
