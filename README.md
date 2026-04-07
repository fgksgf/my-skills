# my-skills

> Personal collection of skills for Claude Code and Codex.

[English](#english) | [中文](#中文)

---

## English

A curated collection of [skills](https://agentskills.io) I build and use across **Claude Code** and **Codex**. Each skill lives in its own subdirectory with a self-contained `SKILL.md`, optional `references/`, `scripts/`, and `assets/`.

### Skills

| Skill | Description |
|---|---|
| [`skill-forge`](./skill-forge) | Opinionated skill-creator with embedded best-practices audit. Forges, refines, and audits Claude/Codex skills against 10 hard rules distilled from agentskills.io. |

More skills will be added over time. Each follows the same structure: a small, well-scoped `SKILL.md` plus on-demand references and scripts.

### Installing a skill

Pick one of the following.

#### Option A: clone the whole collection

```bash
git clone https://github.com/fgksgf/my-skills.git
cp -r my-skills/<skill-name> ~/.claude/skills/<skill-name>       # Claude Code
cp -r my-skills/<skill-name> <your-codex-skills-dir>/<skill-name> # Codex
```

#### Option B: sparse checkout (just one skill)

```bash
git clone --filter=blob:none --no-checkout https://github.com/fgksgf/my-skills.git
cd my-skills
git sparse-checkout init --cone
git sparse-checkout set <skill-name>
git checkout
```

### Layout

```
my-skills/
├── LICENSE              # MIT, covers original content in this repo
├── README.md            # This file
└── <skill-name>/        # One directory per skill
    ├── SKILL.md         # Required: instructions the model loads at runtime
    ├── agents/          # Optional: Codex UI metadata (openai.yaml)
    ├── references/      # Optional: load-on-demand reference material
    ├── scripts/         # Optional: bundled helper scripts
    └── assets/          # Optional: templates, fonts, boilerplate
```

### Contributing

This is a personal collection, but issues and pull requests are welcome — especially if you find a bug in a skill, or want to suggest a hard rule worth adding to `skill-forge`'s audit list.

### License

MIT — see [LICENSE](LICENSE).

Some skills bundle scripts adapted from upstream projects without explicit licenses; those scripts carry attribution headers in-file and are included in good faith. See each skill's own README for details.

---

## 中文

我个人在 **Claude Code** 和 **Codex** 中构建并使用的 [skills](https://agentskills.io) 集合。每个 skill 独立存放在自己的子目录中, 包含自包含的 `SKILL.md`、可选的 `references/`、`scripts/` 和 `assets/`。

### Skill 列表

| Skill | 描述 |
|---|---|
| [`skill-forge`](./skill-forge) | 有主见的 skill-creator, 内嵌最佳实践审计。围绕从 agentskills.io 提炼的 10 条硬约束, 锻造、精炼并审计 Claude/Codex skill。 |

后续会持续添加新的 skill。每个 skill 都遵循同一结构: 一个精简、范围明确的 `SKILL.md`, 加上按需加载的 references 和脚本。

### 安装某个 skill

任选其一。

#### 方式 A: 克隆整个集合

```bash
git clone https://github.com/fgksgf/my-skills.git
cp -r my-skills/<skill-name> ~/.claude/skills/<skill-name>        # Claude Code
cp -r my-skills/<skill-name> <你的-codex-skills-目录>/<skill-name>  # Codex
```

#### 方式 B: 稀疏检出(只拿一个 skill)

```bash
git clone --filter=blob:none --no-checkout https://github.com/fgksgf/my-skills.git
cd my-skills
git sparse-checkout init --cone
git sparse-checkout set <skill-name>
git checkout
```

### 目录结构

```
my-skills/
├── LICENSE              # MIT, 覆盖本仓库原创内容
├── README.md            # 本文件
└── <skill-name>/        # 每个 skill 一个目录
    ├── SKILL.md         # 必需: 模型在运行时加载的指令
    ├── agents/          # 可选: Codex UI 元数据 (openai.yaml)
    ├── references/      # 可选: 按需加载的参考材料
    ├── scripts/         # 可选: 打包的辅助脚本
    └── assets/          # 可选: 模板、字体、样板代码
```

### 贡献

这是一个个人集合, 但欢迎 issue 和 PR — 尤其是当你在某个 skill 中发现 bug, 或想建议一条值得加入 `skill-forge` 审计列表的硬约束时。

### 许可

MIT — 见 [LICENSE](LICENSE)。

部分 skill 打包了从无明确 license 的上游项目改编的脚本; 这些脚本在文件内部带 attribution header, 以善意方式收录。详情参见每个 skill 自己的 README。
