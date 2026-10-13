# Language Packs

e-agent's core is language-agnostic: the loop (SOUL.md §2), the agents' workflow logic, `rules/common/`, `contexts/`, and `memory/`. Language- and framework-specific assets are grouped into **packs** you add per project.

This follows the layered convention inherited from ECC: `rules/common/` defines universal defaults; each pack extends them, and specific overrides general.

## Installed pack: `ts-vue`

Serves this repository's stack — TypeScript + Vue 3 + Vite + Pinia + Vitest.

| Asset | Location |
| --- | --- |
| TypeScript rules | `rules/typescript/` (coding-style, testing, patterns, hooks, security) |
| Vue rules | `rules/vue/` (coding-style, testing, patterns, hooks, security) |
| Skills | `typescript-strict`, `vue-best-practices`, `frontend-design` |
| Typecheck convention | `vue-tsc --noEmit` (never plain `tsc` for `.vue` files) |

## Adding a pack (e.g. `python`)

1. Create `rules/python/` with files that each start with:
   ```
   > This file extends [common/xxx.md](../common/xxx.md) with Python specific content.
   ```
   Minimum set: `coding-style.md`, `testing.md`, `patterns.md`, `hooks.md`, `security.md`.
2. If the stack needs a deep how-to skill, add `skills/<name>/SKILL.md` following the function-based naming rule (never brand-prefixed; language suffix is fine, e.g. `python-testing`).
3. Register the pack in `SOUL.md` §6 and this file.
4. Pack-specific commands (typecheck/test runners) belong in the pack's rules, not in core agents — core agents say "per the active pack".

## Rules for pack authors

- A pack **extends**, never replaces: core principles (SOUL.md §3) always apply.
- Core agents stay language-neutral. Language specifics live in rules files and skills the agents point to.
- Keep packs honest: if the repo has no code for a stack yet (current state), the pack documents the *planned* conventions (see CLAUDE.md "Tech Stack (Planned)").
