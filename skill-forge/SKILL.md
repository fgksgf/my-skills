---
name: skill-forge
description: Forge new Claude/Codex skills, refine existing ones, and audit them against best practices. Use this whenever the user wants to build a skill, turn a workflow into a skill, package domain knowledge as a skill, improve a skill's description so it triggers correctly, or review an existing SKILL.md for bloat, missing gotchas, or unsafe defaults. Trigger even when the user says "help me capture this", "make this reusable", "turn this conversation into a skill", or mentions SKILL.md, frontmatter, or progressive disclosure.
---

# Skill Forge

A lean skill for building skills the right way - grounded in real expertise, ruthless about context cost, and calibrated per task fragility.

## Communication

- Match the user's language. If they write in Chinese, respond in Chinese; if English, respond in English.
- Write all skill file contents in **English** regardless (skills are consumed by future model instances; English is the lingua franca for instructions).
- Skip preamble. Prefer terse, structured answers. Do not announce what you are about to do - just do it.

## The loop (do not skip stages)

```
1. CAPTURE     -> get real intent + real material from the user
2. PLAN        -> decide what's a script, what's a reference, what's inline
3. DRAFT       -> write SKILL.md with hard rules applied
4. RUN         -> actually use the skill on a real task
5. REFINE      -> read the trace, cut waste, add missing gotchas
6. PACKAGE     -> validate, install path, done
```

The most common failure mode is jumping from stage 1 to stage 6 in a single shot. Resist it. A skill that has never been executed against a real task is a draft, not a skill.

## Stage 1: Capture intent (anti-vague)

Never let the user hand-wave the intent. Extract these answers before drafting:

1. **One concrete task** the skill must handle end-to-end. Not "PDF stuff" - "extract line items from supplier invoice PDFs into a CSV with columns A/B/C".
2. **Trigger phrases** the user would actually type. Realistic, with typos and casual register if that matches them.
3. **Real material**: at least one of - sample input file, real error log, runbook excerpt, code review comment, git patch, or a transcript of doing the task with you.
4. **Output format**: shape, file type, where it goes.
5. **Failure modes** the user has seen before. These become gotchas.

If the user cannot supply any of items 3 or 5, push back: "Without real samples I can only generate generic platitudes. Can you paste in an example?" This is non-negotiable - it is the central lesson of the best-practices article.

## Stage 2: Plan reusable contents

For each step in the user's workflow, classify it:

| Signal | Goes to |
|---|---|
| Same code rewritten every run | `scripts/` (write once, call from SKILL.md) |
| Domain knowledge / schema / API spec | `references/<topic>.md` (load on demand) |
| Template or boilerplate consumed in output | `assets/` |
| Core procedure, gotchas, decision rules | inline in SKILL.md |

If nothing falls into the first three buckets, the skill is just SKILL.md - that is fine, do not invent ceremony.

## Stage 3: Draft SKILL.md

Use the bundled `scripts/init_skill.py` to generate the scaffold:

```bash
python scripts/init_skill.py <skill-name> --path <output-dir> [--resources scripts,references,assets]
```

This generates `SKILL.md` with frontmatter, `agents/openai.yaml`, and any requested resource directories. Pass `--interface display_name=... short_description=... default_prompt=...` to populate the OpenAI metadata file deterministically.

Then fill the body. Apply the hard rules below at write time, not as an afterthought.

### Frontmatter

- `name`: lowercase, hyphen-case, < 64 chars, verb-led when possible.
- `description`: this is the only thing the model sees before deciding to load the skill. Make it **slightly pushy** - Claude/Codex tend to under-trigger. Include both *what* and *when*. Mention the surface phrases the user might type. Do not stuff keywords; describe trigger contexts.

Bad: `"Helps with PDFs."`
Good: `"Extract structured line items from supplier invoice PDFs into normalized CSV. Use whenever the user mentions invoices, supplier billing, accounts payable, or has a PDF that looks like a bill - even if they do not say 'extract'."`

## HARD RULES (audit every draft against this list)

These are non-negotiable. After writing a draft, re-read it line by line and check:

1. **No general-knowledge filler.** If a sentence explains what PDF / HTTP / SQL / a database migration *is*, delete it. The model already knows.
2. **SKILL.md body < 500 lines and < 5000 tokens.** Hard ceiling for the main file. Total skill size (including references/) is unbounded - large reference material is fine as long as SKILL.md stays lean and each reference has an explicit load trigger. For multi-variant skills (e.g. cloud-deploy with AWS/GCP/Azure), keep only the workflow + selection logic in SKILL.md and push variant details to `references/<variant>.md`.
3. **Every `references/` pointer has a trigger condition.** Not "see references/ for details" but "if the API returns non-200, read references/api-errors.md".
4. **Provide a default, not a menu.** When tools/libraries are interchangeable, pick one and mention alternatives in one line.
5. **Always include a `## Gotchas` section** unless the user explicitly says there are none. Each gotcha must be a concrete environment-specific fact, not generic advice.
6. **Destructive or batch operations use plan-validate-execute.** The agent generates a plan file, a script validates it against ground truth, only then execute.
7. **Every code example must be runnable.** No pseudocode, no `...`, no `# your logic here`.
8. **Procedures over declarations.** Teach the method ("read the schema, join on _id, format as table"), not the answer for one specific instance.
9. **Strict instructions are explicitly marked.** When a sequence is fragile, write "Run exactly this command. Do not add flags." When an instruction is flexible, explain *why* instead of forcing it.
10. **Calibrate freedom per section.** A single SKILL.md can mix high-freedom (code review checklist) and low-freedom (migration command) sections. Do not make the whole file one register.

If a draft fails any of these, fix the draft before showing it to the user.

## Reusable patterns (use when they fit, do not force)

### Gotchas section
Lives in SKILL.md (not references/) so the model reads it before encountering the trap. Each item: concrete, environment-specific, actionable.

```markdown
## Gotchas

- The `users` table uses soft deletes. Always `WHERE deleted_at IS NULL`.
- `/health` returns 200 if the web server is up even when the DB is down. Use `/ready` for full health.
```

### Output template
For fixed-format outputs, paste the literal template. Models pattern-match templates better than prose descriptions. Long templates go in `assets/` with a load trigger.

### Checklist for multi-step workflows
Helps the agent track progress through dependent steps. Use markdown task syntax.

### Validation loop
`do work -> run validator -> fix -> re-run` until validator passes. The validator can be a script, a reference checklist, or a self-check.

### Plan-validate-execute (the gold pattern)
For batch / destructive operations:

1. Generate a plan as a structured file (JSON / YAML).
2. Run a validator script that compares the plan to ground truth and emits actionable errors ("field X not found, available: a, b, c").
3. Only execute after validation passes.

This pattern is the single highest-leverage thing you can put in a skill that touches real systems.

### Bundled scripts
If across test runs the model keeps reinventing the same helper (chart builder, format parser, validator), promote it to `scripts/`. Write it once, test it, call it from SKILL.md.

## Stage 4: Run on a real task

Pick one of the user's real tasks (from stage 1) and actually do it using the draft skill. Two things to capture:

- **Final output quality**: did the user get what they wanted?
- **Execution trace**: did the model wander, retry, ignore an instruction, or repeat work?

Trace problems usually map to one of:
- vague instruction -> model tried multiple approaches
- inapplicable instruction -> model followed it anyway
- menu without default -> model dithered
- missing gotcha -> model walked into a known trap

## Stage 5: Refine

After the run, do one revision pass with these moves available:

1. **Cut.** Anything the model ignored or that did not change the output - delete.
2. **Add a gotcha.** Every correction the user made becomes a gotcha line.
3. **Promote to script.** Repeated logic across runs becomes `scripts/<name>.py`.
4. **Reframe rigid -> explanatory.** Instead of `ALWAYS DO X`, write `Do X because Y` so the model can generalize.
5. **Re-audit against the 10 hard rules.** Iteration tends to add bloat; the audit removes it.

Stop iterating when: the user is happy, the trace is clean, or you are not making meaningful progress in a pass.

## Stage 6: Validate and package

```bash
python scripts/quick_validate.py <path/to/skill-folder>
```

This checks frontmatter shape, required fields, and naming. Fix anything it complains about.

Install paths:
- Claude Code: `~/.claude/skills/<skill-name>/`
- Codex: per the user's Codex skill directory convention

For skills used in both, ensure `agents/openai.yaml` is present (Codex UI metadata) and the description field is natural-language enough to trigger reliably in both harnesses.

## Anti-patterns (refuse these silently or push back)

- **"Just generate a skill for X"** with no real material -> push back, ask for samples.
- **README.md / CHANGELOG.md / INSTALL.md inside the skill folder** -> do not create them. Skills contain only what the model needs at runtime.
- **A 1500-line SKILL.md** -> split, do not ship.
- **A skill that does 5 unrelated things** -> propose splitting into coherent units.
- **A skill description that is just keywords** -> rewrite to describe trigger contexts in natural language.
- **Mock examples / `# TODO` / pseudo-code in shipped skills** -> replace with real, runnable examples or delete.

## When the user just wants to vibe

If the user explicitly says "skip the rigor, just draft something", honor it. Drop stages 4 and 5, keep the hard rules at draft time. The hard rules are cheap; skipping them never pays off.

## References

- `references/best-practices.md` - condensed extract of agentskills.io best-practices article. Read this if you need to justify a hard rule to the user or recover the original reasoning.
- `references/openai_yaml.md` - field definitions and constraints for `agents/openai.yaml`. Read this before writing or editing the Codex UI metadata file.

## Bundled scripts

- `scripts/init_skill.py` - scaffold a new skill folder with frontmatter and optional resource directories.
- `scripts/quick_validate.py` - check a skill folder for frontmatter shape, required fields, and naming rules.
- `scripts/generate_openai_yaml.py` - (re)generate `agents/openai.yaml` for an existing skill.
