# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Framework

e-agent is a personal, general-purpose agent harness: a language-agnostic core that installs into any project. Read `SOUL.md` first — it is the core identity and the five principles.

- **Agents**: `agents/` — 14 specialists, invoked via the Agent tool
- **Skills**: `skills/` — 20 skills, invoked via the Skill tool
- **Rules**: `rules/` — layered standards (common → typescript → vue)
- **Contexts**: `contexts/` — dev / review / research modes
- **Memory**: `memory/` — persistent progress, decisions, learnings

The framework is fully declarative: Markdown plus a plugin manifest — no hooks, no runtime scripts.

### Plugin Installation (local)

The repo doubles as a Claude Code plugin (`.claude-plugin/`). Agents under `agents/` and skills under `skills/` are auto-discovered by convention once installed:

```bash
claude plugin marketplace add /path/to/e-agent-workspace
claude plugin install e-agent@e-agent
```

Installed plugin agents are invoked with the `e-agent:` prefix (e.g. `e-agent:planner`).

Manifest rules (validated the hard way by ECC — see `.claude-plugin/` conventions): `version` is mandatory; `skills` must be an array; never declare `agents` or `hooks` fields — both are auto-loaded by convention and explicit declaration causes validation errors.

### Project MCP

The repo-root `.mcp.json` activates `chrome-devtools` for every session in this project (auto-discovered by Claude Code). It is kept to a single zero-key server on purpose; more servers live in `mcp-configs/mcp-servers.json` — copy what you need. The `"mcpServers": {}` opt-out in `plugin.json` keeps this file from being bundled into plugin installs.

### Session Guardrails

`.claude/rules/guardrails.md` is auto-injected into every session in this repo (Claude Code project-level rules), giving the main session the prompt-defense baseline that agents already embed. This mirrors ECC's dual-layer split: root directories ship as the plugin; `.claude/` serves local development and stays out of plugin distribution.

## Project

e-agent workspace — a Node.js project (CommonJS) with TypeScript and Vue 3. Currently in early bootstrapping phase with no application source code yet.

## Current State

- **No source code**: No `.ts`, `.vue`, or `.js` application files exist yet
- **No build tools configured**: TypeScript, Vite, Vitest, ESLint, and Prettier are planned but not yet installed or configured
- **Framework ready**: e-agent harness (SOUL.md + agents + skills + rules + contexts + memory) is in place

## Tech Stack (Planned)

- **Language**: TypeScript
- **Frontend**: Vue 3 (Composition API, `<script setup lang="ts">`)
- **State management**: Pinia (setup stores)
- **Routing**: vue-router (lazy-loaded)
- **Server cache**: @tanstack/vue-query
- **Validation**: Zod
- **Testing**: Vitest + @vue/test-utils (unit/component), Playwright (E2E)
- **Lint/Format**: ESLint flat config with `vue/vue3-recommended`, Prettier
- **Typecheck**: `vue-tsc --noEmit` (do not use plain `tsc` — it cannot read `.vue` SFCs)

## Commands (To Be Configured)

These commands will be available once the project is bootstrapped. Currently, no build tools are installed.

```bash
# Install dependencies (when configured)
npm install

# Development server (when configured)
npm run dev

# Type checking (when configured)
npm run typecheck  # vue-tsc --noEmit

# Linting (when configured)
npm run lint

# Formatting (when configured)
npm run format

# Testing (when configured)
npm run test           # Run all tests
npm run test:unit      # Unit tests only
npm run test:e2e       # E2E tests only
npm run test:coverage  # With coverage report

# Build for production (when configured)
npm run build
```

## Rules System

Rules are layered standards under `rules/`: `common/` (language-agnostic) → `typescript/` → `vue/`. Language-specific rules override common rules where idioms differ. See `rules/README.md`.

## Skills System

20 skills in `skills/`:

- **Workflow & meta**: dev-team, git-workflow, skill-creator, find-skills, karpathy-guidelines
- **TDD & quality**: tdd, code-review-checklist, refactor-cleanup, e2e-testing
- **Vue & frontend**: vue-best-practices, vue-patterns, vite-patterns, frontend-design, agent-browser, jishi-mcp (design-to-code via JiShi Design)
- **TypeScript & backend**: typescript-strict, error-handling, api-design
- **Security & docs**: security-checklist, docs-memory

Skills tell you _how_; rules tell you _what_.

## Agents

Development follows the core loop (see `SOUL.md` §2). Delegate to the right agent early:

| Agent | When to use |
| --- | --- |
| researcher | Before writing new code — verify facts, find existing solutions |
| code-explorer | Before large changes — map unfamiliar code, trace execution paths |
| planner | Features / refactors — PRD, architecture, task list |
| tdd-guide | New features / bug fixes — write failing tests first |
| implementer | After RED — minimal code to make tests green |
| code-reviewer | Immediately after writing code (general) |
| typescript-reviewer | TS/JS diffs — type safety, async, Node security |
| vue-reviewer | `.vue` / Vue ecosystem diffs — reactivity, template security, composables |
| type-design-analyzer | Deep type reviews — make illegal states unrepresentable |
| code-simplifier | REFACTOR phase — simplify while preserving behavior exactly |
| security-reviewer | Before committing sensitive code (auth, payments, user data, DB, crypto) |
| build-fixer | When typecheck / lint / test fail |
| commit-reviewer | Before committing — draft/check the Conventional Commits message |
| memory-keeper | After verify — persist progress, decisions, learnings |

When delegating: always wait for results and integrate them — never fire-and-forget.

## Contexts

Three modes under `contexts/`. Default is `dev`; switch by reading the target file first:

- `contexts/dev.md` — default, implementing features
- `contexts/review.md` — code review / PR analysis
- `contexts/research.md` — fact-checking / dependency lookup

## Memory

Persistent, append-only Markdown under `memory/`:

- **Read**: at session start, read only `memory/progress.md`. Grep `decisions.md` / `learnings.md` on demand — do not load them fully.
- **Write**: `memory-keeper` is the only writer. `learnings.md` only after verify passes.

## Key Conventions

These conventions are enforced by the rules system. The most critical ones:

- **Immutability is critical**: always create new objects, never mutate. Use spread operators.
- **TDD mandatory**: write test first (RED → GREEN → REFACTOR), verify 80%+ coverage
- **Small files**: 200–400 lines typical, 800 lines soft ceiling for source files
- **Small functions**: <50 lines
- **Max nesting depth**: 4 levels
- **No `any`**: use `unknown` + narrowing, `interface` for objects, `type` for unions/intersections
- **No `console.log`** in production code; use a proper logger
- **Conventional Commits**: `<type>: <description>` — types: feat, fix, refactor, docs, test, chore, perf, ci
- **API responses**: use a consistent envelope (`ApiResponse<T>` with `success`, `data`, `error`, `meta`)

For complete coding standards, refer to the rules in `rules/common/`, `rules/typescript/`, and `rules/vue/`.
