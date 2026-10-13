# Language Content

e-agent's core is **personal and general-purpose**: the loop (SOUL.md §2), the agents' workflow logic, `rules/common/`, `contexts/`, `memory/`, and the stack-agnostic skills. That core is what you install into any project.

Language- and framework-specific content is **content you grow as needed** — a grouping concept, not an installation unit. Plugin installs are always full-catalog (Claude Code loads every agent/skill in the repo); nothing is truly "plugged in or out". If a future need justifies selective installation, that requires installer infrastructure on the scale ECC built (46 install modules + installer scripts) — explicitly out of scope.

## Current language content: TypeScript + Vue 3

Serves this repository's stack — TypeScript + Vue 3 + Vite + Pinia + Vitest.

| Asset | Location |
| --- | --- |
| TypeScript rules | `rules/typescript/` (coding-style, testing, patterns, hooks, security) |
| Vue rules | `rules/vue/` (coding-style, testing, patterns, hooks, security) |
| Skills | `typescript-strict`, `vue-best-practices`, `frontend-design` |
| Typecheck convention | `vue-tsc --noEmit` (never plain `tsc` for `.vue` files) |

## Adding a language (e.g. `python`)

1. Create `rules/python/` with files that each start with:
   ```
   > This file extends [common/xxx.md](../common/xxx.md) with Python specific content.
   ```
   Minimum set: `coding-style.md`, `testing.md`, `patterns.md`, `hooks.md`, `security.md`.
2. If the stack needs a deep how-to skill, add `skills/<name>/SKILL.md` following the function-based naming rule (never brand-prefixed; language suffix is fine, e.g. `python-testing`).
3. Register it in `SOUL.md` §6 and this file.
4. Stack-specific commands (typecheck/test runners) belong in that stack's rules, not in core agents — core agents say "per the active stack".

## Rules for language-content authors

- Language content **extends**, never replaces: core principles (SOUL.md §3) always apply.
- Core agents stay language-neutral. Language specifics live in rules files and skills the agents point to.
- Keep it honest: if the repo has no code for a stack yet (current state), the rules document the *planned* conventions (see CLAUDE.md "Tech Stack (Planned)").
