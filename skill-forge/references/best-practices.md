# Best Practices - Condensed Reference

Source: https://agentskills.io/skill-creation/best-practices

This file is a load-on-demand companion to SKILL.md. Read it when you need to justify a hard rule to the user, recover the original reasoning behind a pattern, or settle a design dispute about scope.

## Five pillars

### 1. Start from real expertise
Generic LLM-generated skills produce generic outputs ("handle errors appropriately"). Effective skills extract expertise from real material.

Sources, ranked by signal:
1. Hands-on task transcript with corrections
2. Code review comments (captures recurring concerns)
3. Git patches and fixes (shows what actually broke)
4. Incident reports / runbooks
5. Internal API specs and schemas
6. Generic documentation (lowest signal - skip if other sources exist)

### 2. Refine with real execution
First drafts are always wrong in subtle ways. Run the skill, read the trace (not just the output), revise. One pass of execute-then-revise noticeably improves quality.

Trace problems map to root causes:
- Wandering -> instructions too vague
- Wrong-context behavior -> instructions did not gate by context
- Dithering -> menu without default

### 3. Spend context wisely
Skill content competes with conversation history and system prompt for the agent's attention.

- Add what the agent lacks, omit what it knows
- Design coherent units (one skill = one composable unit of work)
- Aim for moderate detail (over-comprehensive hurts more than helps)
- SKILL.md < 500 lines / 5000 tokens; overflow goes to references/ with explicit triggers

### 4. Calibrate control per task fragility
- High-freedom (multiple valid approaches): explain *why*, not *how*
- Low-freedom (fragile sequence): give exact commands, mark "do not modify"
- Most skills mix both - calibrate per section

Two corollaries:
- **Defaults, not menus**: pick one tool, mention alternatives in one line
- **Procedures, not declarations**: teach the method, not the answer

### 5. Patterns for effective instructions
- **Gotchas section** (must live in SKILL.md, not references/, so the agent reads it before encountering the trap)
- **Output templates** (concrete structures beat prose descriptions)
- **Checklists** (for multi-step workflows with dependencies)
- **Validation loops** (do -> validate -> fix -> repeat)
- **Plan-validate-execute** (for batch/destructive ops; the validator script must give actionable error messages)
- **Bundled scripts** (when traces show the model reinventing the same logic repeatedly)

## The "would the agent get this wrong without me?" test

Apply to every paragraph of a skill draft. If the answer is no, delete it. If unsure, test it. If the agent already nails the task without the skill, the skill is not adding value.

## The pushback questions

When the user resists the rules, these are the answers:

- *"Why so terse?"* - Every token in the skill body competes with conversation history. Bloat costs precision.
- *"Why do I need real samples?"* - Without them, the skill encodes generic knowledge the model already has, and adds no value.
- *"Why insist on Gotchas?"* - Gotchas are the highest-leverage content because they correct mistakes the model would otherwise make confidently.
- *"Why not list all the options?"* - A menu without a default makes the model dither; a default with a one-line escape hatch does not.
- *"Why must references/ files have triggers?"* - The agent will not auto-load them. Without an explicit "if X then read Y", they are dead weight.

## What NOT to put in a skill

- README.md, CHANGELOG.md, INSTALL.md - skills contain only runtime instructions
- Process notes about how the skill was created
- General programming knowledge
- Pseudocode or `# your logic here` placeholders
- Sentences that explain what a well-known tool/format is
- "When to use this skill" sections in the body (that goes in `description`, the body is only loaded *after* triggering)
