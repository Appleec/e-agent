# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Framework

E-Agent is a minimal, declarative agent harness for this repository. Read `SOUL.md` first — it is the core identity and the five principles.

- **Agents**: `agents/` — 8 specialists, invoked via the Agent tool
- **Skills**: `skills/` — 15 skills, invoked via the Skill tool
- **Rules**: `rules/` — layered standards (common → typescript → vue)
- **Contexts**: `contexts/` — dev / review / research modes
- **Memory**: `memory/` — persistent progress, decisions, learnings

The framework is fully declarative: Markdown only, no hooks, no runtime scripts.

## Project

E-Agent workspace — a Node.js project (CommonJS) with TypeScript and Vue 3. Currently in early bootstrapping phase with no application source code yet.

## Current State

- **No source code**: No `.ts`, `.vue`, or `.js` application files exist yet
- **No build tools configured**: TypeScript, Vite, Vitest, ESLint, and Prettier are planned but not yet installed or configured
- **Framework ready**: E-Agent harness (SOUL.md + agents + skills + rules + contexts + memory) is in place

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

15 skills in `skills/`:

- **Workflow & meta**: e-agent-workflow, dev-team, git-workflow, skill-creator, find-skills, karpathy-guidelines
- **TDD & quality**: tdd, code-review-checklist, refactor-cleanup
- **Vue & frontend**: vue-best-practices, frontend-design, agent-browser
- **TypeScript & security & docs**: typescript-strict, security-checklist, docs-memory

Skills tell you _how_; rules tell you _what_.

## Agents

Development follows the core loop (see `SOUL.md` §2). Delegate to the right agent early:

| Agent | When to use |
| --- | --- |
| researcher | Before writing new code — verify facts, find existing solutions |
| planner | Features / refactors — PRD, architecture, task list |
| tdd-guide | New features / bug fixes — write failing tests first |
| implementer | After RED — minimal code to make tests green |
| code-reviewer | Immediately after writing code |
| security-reviewer | Before committing sensitive code (auth, payments, user data, DB, crypto) |
| build-fixer | When typecheck / lint / test fail |
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
