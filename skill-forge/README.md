# Skill Forge

> Forge skills. Audit them. Ship them.

A lean, opinionated skill for building **Claude Code** and **Codex** skills the right way — grounded in real expertise, ruthless about context cost, and calibrated per task fragility.

[English](#english) | [中文](#中文)

---

## English

### Why another skill-creator?

Anthropic and OpenAI both ship their own `skill-creator`. They are excellent, but they are also large — one bundles a full eval-viewer pipeline, the other focuses on Codex-specific scaffolding. **Skill Forge** is a distilled, opinionated alternative built around one idea:

> A skill is a contract, not a tutorial.

The contract is enforced by **10 hard rules** embedded directly in `SKILL.md`, derived from the [agentskills.io best-practices article](https://agentskills.io/skill-creation/best-practices). Every draft must pass the audit before it ships.

### Features

- **Dual-harness** — works in both Claude Code (`~/.claude/skills/`) and Codex, with `agents/openai.yaml` for Codex UI metadata.
- **10 hard rules** — no general-knowledge filler, <500 line SKILL.md, explicit `references/` load triggers, defaults-not-menus, runnable examples only, procedures over declarations, and more.
- **6-stage workflow** — Capture → Plan → Draft → Run → Refine → Package. Skipping stage 4 (real execution) is the most common failure mode; Skill Forge pushes back on it.
- **Anti-pattern detection** — refuses to ship `README.md`/`CHANGELOG.md` inside the skill folder, 1500-line monster `SKILL.md`, keyword-stuffed descriptions, or pseudo-code examples.
- **Bundled scripts** — `init_skill.py`, `quick_validate.py`, `generate_openai_yaml.py` (adapted from OpenAI's `codex-skill-creator`).
- **Condensed best-practices reference** — load-on-demand at `references/best-practices.md`, used to justify hard rules when users push back.

### Installation

Skill Forge lives as a subdirectory inside the [`fgksgf/my-skills`](https://github.com/fgksgf/my-skills) skill collection. Pick one of:

#### Option A: clone the whole collection

```bash
git clone https://github.com/fgksgf/my-skills.git
cp -r my-skills/skill-forge ~/.claude/skills/skill-forge       # Claude Code
cp -r my-skills/skill-forge <your-codex-skills-dir>/skill-forge # Codex
```

#### Option B: sparse checkout (just this skill)

```bash
git clone --filter=blob:none --no-checkout https://github.com/fgksgf/my-skills.git
cd my-skills
git sparse-checkout init --cone
git sparse-checkout set skill-forge
git checkout
```

### Usage

Trigger phrases (any of these should activate the skill):

- "Help me turn this workflow into a skill"
- "Create a skill for extracting invoices from PDFs"
- "Review my SKILL.md for bloat"
- "Improve my skill's description so it triggers correctly"
- "I want to package this domain knowledge as a skill"

Once triggered, Skill Forge walks you through the 6 stages and refuses to let you skip the "bring real samples" requirement.

### Structure

```
skill-forge/
├── SKILL.md                 # Core: 6-stage loop + 10 hard rules (<200 lines)
├── agents/
│   └── openai.yaml          # Codex UI metadata
├── references/
│   ├── best-practices.md    # Condensed agentskills.io article (load on demand)
│   └── openai_yaml.md       # Field definitions for agents/openai.yaml
├── scripts/
│   ├── init_skill.py        # Scaffold a new skill folder
│   ├── quick_validate.py    # Validate frontmatter + naming
│   └── generate_openai_yaml.py
└── README.md                # This file
```

### The 10 hard rules

Every draft SKILL.md is audited against these before shipping:

1. No general-knowledge filler — if a sentence explains what PDF/HTTP/SQL *is*, delete it.
2. SKILL.md body < 500 lines / 5000 tokens. Total skill size is unbounded; overflow goes to `references/`.
3. Every `references/` pointer has an explicit load trigger ("if X then read Y").
4. Provide a default, not a menu.
5. Always include a `## Gotchas` section with concrete environment-specific facts.
6. Destructive or batch operations use plan-validate-execute.
7. Every code example must be runnable. No pseudocode.
8. Procedures over declarations — teach the method, not the answer.
9. Strict instructions are explicitly marked ("run exactly this, do not add flags").
10. Calibrate freedom per section — a single SKILL.md can mix high- and low-freedom parts.

### Credits

- Workflow and patterns inspired by Anthropic's [`claude-skill-creator`](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator) and OpenAI's [`codex-skill-creator`](https://github.com/openai/skills/tree/main/skills/.system/skill-creator).
- Hard rules distilled from the [agentskills.io best-practices article](https://agentskills.io/skill-creation/best-practices).
- `init_skill.py`, `quick_validate.py`, and `generate_openai_yaml.py` are adapted from OpenAI's `codex-skill-creator`. Upstream has no explicit license at the time of writing; scripts are included in good faith with attribution headers.

### License

The original content of Skill Forge (SKILL.md, README, reference distillation) is released under MIT. The three bundled scripts under `scripts/` are adapted from an unlicensed upstream — see the attribution headers inside each file.

> **Note on the README inside a skill folder**: Skill Forge's own hard rules call out `README.md` inside a skill folder as an anti-pattern. That rule applies to the **runtime** content that the model consumes. A repository-level README for human discovery on GitHub is standard practice and is excluded from this rule. If you install Skill Forge into `~/.claude/skills/`, the model simply ignores `README.md` at runtime — it only loads `SKILL.md`.

---

## 中文

### 为什么又造一个 skill-creator?

Anthropic 和 OpenAI 都官方发布了自己的 `skill-creator`。它们很优秀, 但也很重 — 一个捆绑了完整的 eval-viewer 流水线, 另一个聚焦 Codex 专属脚手架。**Skill Forge** 是一个精简的、有主见的替代品, 围绕一个核心理念构建:

> skill 是契约, 不是教程。

这份契约由嵌入在 `SKILL.md` 中的 **10 条硬约束** 强制执行, 提炼自 [agentskills.io best-practices 文章](https://agentskills.io/skill-creation/best-practices)。每份草稿在交付前必须通过审计。

### 特性

- **双 harness 支持** — 同时适配 Claude Code (`~/.claude/skills/`) 和 Codex, 自带 `agents/openai.yaml` 作为 Codex UI 元数据。
- **10 条硬约束** — 不写通用知识填充物、SKILL.md < 500 行、`references/` 加载触发条件必须显式、给默认值而非菜单、代码示例必须可运行、教方法而非答案, 等等。
- **6 阶段工作流** — 捕获 → 规划 → 起草 → 运行 → 精炼 → 打包。跳过阶段 4(真实执行) 是最常见的失败模式, Skill Forge 会主动 push back。
- **反模式检测** — 拒绝在 skill 目录内生成 `README.md`/`CHANGELOG.md`、1500 行怪物 `SKILL.md`、关键词堆砌的 description、伪代码示例。
- **打包脚本** — `init_skill.py`、`quick_validate.py`、`generate_openai_yaml.py`(改编自 OpenAI 的 `codex-skill-creator`)。
- **最佳实践精简引用** — 位于 `references/best-practices.md`, 按需加载, 当用户对硬约束提出质疑时用来援引依据。

### 安装

Skill Forge 作为子目录存放在 [`fgksgf/my-skills`](https://github.com/fgksgf/my-skills) skill 集合仓库中。任选其一:

#### 方式 A: 克隆整个集合

```bash
git clone https://github.com/fgksgf/my-skills.git
cp -r my-skills/skill-forge ~/.claude/skills/skill-forge        # Claude Code
cp -r my-skills/skill-forge <你的-codex-skills-目录>/skill-forge  # Codex
```

#### 方式 B: 稀疏检出(只拿这个 skill)

```bash
git clone --filter=blob:none --no-checkout https://github.com/fgksgf/my-skills.git
cd my-skills
git sparse-checkout init --cone
git sparse-checkout set skill-forge
git checkout
```

### 使用

触发短语(任何一条都应激活此 skill):

- "帮我把这个工作流变成一个 skill"
- "给我创建一个从 PDF 提取发票的 skill"
- "帮我审查 SKILL.md 有没有冗余"
- "优化我的 skill description 让它能正确触发"
- "我想把这份领域知识打包成一个 skill"

一旦触发, Skill Forge 会带你走完 6 个阶段, 并且拒绝让你跳过「提供真实样本」这条要求。

### 目录结构

```
skill-forge/
├── SKILL.md                 # 核心: 6 阶段循环 + 10 条硬约束 (<200 行)
├── agents/
│   └── openai.yaml          # Codex UI 元数据
├── references/
│   ├── best-practices.md    # agentskills.io 文章精简提要 (按需加载)
│   └── openai_yaml.md       # agents/openai.yaml 字段定义
├── scripts/
│   ├── init_skill.py        # 生成新 skill 脚手架
│   ├── quick_validate.py    # 校验 frontmatter + 命名
│   └── generate_openai_yaml.py
└── README.md                # 本文件
```

### 10 条硬约束

每一份 SKILL.md 草稿在交付前都要逐条过一遍:

1. 不写通用知识填充 — 如果某句话在解释 PDF/HTTP/SQL *是什么*, 删掉。
2. SKILL.md 主体 < 500 行 / 5000 tokens。总体积不限, 溢出内容放 `references/`。
3. 每个 `references/` 引用必须有显式加载触发条件 ("if X then read Y")。
4. 给默认值, 不给菜单。
5. 必须包含 `## Gotchas` 段落, 内容是具体的环境特异性事实。
6. 破坏性或批量操作使用 plan-validate-execute 模式。
7. 每个代码示例必须可运行, 禁止伪代码。
8. 教方法而非结论 — 传授手段, 不给一次性答案。
9. 严格指令必须显式标注 ("精确执行此命令, 不要加 flag")。
10. 每一段的控制粒度独立校准 — 同一个 SKILL.md 里可以混用高自由度和低自由度段落。

### 致谢

- 工作流与模式灵感来自 Anthropic 的 [`claude-skill-creator`](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator) 和 OpenAI 的 [`codex-skill-creator`](https://github.com/openai/skills/tree/main/skills/.system/skill-creator)。
- 硬约束提炼自 [agentskills.io best-practices 文章](https://agentskills.io/skill-creation/best-practices)。
- `init_skill.py`、`quick_validate.py`、`generate_openai_yaml.py` 改编自 OpenAI 的 `codex-skill-creator`。原仓库在撰写本文时没有明确的 license, 这些脚本带 attribution header 以示出处。

### 许可

Skill Forge 的原创内容 (SKILL.md、README、参考资料提炼) 以 MIT 许可发布。`scripts/` 目录下的三个脚本改编自一个未明确 license 的上游项目 — 详见每个脚本文件内部的 attribution header。

> **关于 skill 目录内 README 的说明**: Skill Forge 自己的硬约束明确把 skill 目录内的 `README.md` 列为反模式。这条规则针对的是**运行时**由模型加载的内容。仓库级别的 README(给人读的, 用于 GitHub 发现)属于标准做法, 不在此规则覆盖范围内。当你把 Skill Forge 安装到 `~/.claude/skills/` 时, 模型在运行时只会加载 `SKILL.md`, `README.md` 会被忽略。
