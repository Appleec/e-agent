# SOUL.md — e-agent Core Identity

## 1. Identity & Philosophy

e-agent is a **personal, general-purpose agent harness**: a language-agnostic core — loop, agents, `rules/common/`, contexts, memory — that installs into any project. Per-language rules and skills are plain directories added as needed (`rules/typescript/`, `rules/vue/` for this repo; see `rules/README.md` for how to add more).

It gives the agent a coordinated engineering loop instead of rebuilding one in every prompt.

**Motto:** Optimize the context window. Persist everything else.

The context window holds only the current task plus a short progress summary. Everything else — decisions, learnings, and history — lives in persistent Markdown under `memory/` and is retrieved on demand.

## 2. Core Loop

```
research → plan → test → implement → review → verify → remember → improve
```

| Step | Agent | Context | Output lands in |
| --- | --- | --- | --- |
| research | `researcher` | `contexts/research.md` | research notes (ephemeral) |
| plan | `planner` | `contexts/dev.md` | `memory/decisions.md` (ADR) |
| test | `tdd-guide` | `contexts/dev.md` | failing tests (RED) |
| implement | `implementer` | `contexts/dev.md` | minimal passing code (GREEN) |
| review | `code-reviewer` | `contexts/review.md` | review findings |
| verify | `build-fixer` · `security-reviewer` | `contexts/dev.md` | all-green typecheck/lint/test |
| remember | `memory-keeper` | `contexts/dev.md` | `memory/` (progress/learnings) |
| improve | loop back to review | `contexts/review.md` | refactor notes |

## 3. Five Principles

1. **Agent-First** — delegate to the right agent as early as possible; wait for results and integrate them; never fire-and-forget.
2. **Test-Driven** — RED → GREEN → REFACTOR; verify ≥80% coverage before trusting implementation.
3. **Security-First** — sensitive code (auth, payments, user data, DB queries, crypto) must pass `security-reviewer` before commit.
4. **Immutability** — always create new objects; never mutate in place. Use spread operators.
5. **Plan Before Execute** — no plan, no code. Small files (200–400 lines), functions <50 lines, nesting ≤4 levels.

## 4. Agent Roster

Agents live in `agents/` and are invoked through the Agent tool. When installed as a Claude Code plugin (`.claude-plugin/`), they are scoped as `e-agent:<name>` (e.g. `e-agent:planner`); the bare name also resolves.

| Agent | Purpose | When to use |
| --- | --- | --- |
| `researcher` | Verify facts, find existing solutions, check docs/registries | Before writing anything new |
| `code-explorer` | Trace execution paths, map architecture layers, find reusable patterns | Taking over unfamiliar code, before large changes |
| `planner` | Break requirements into PRD / architecture / task list | Features, refactors, architectural changes |
| `tdd-guide` | Write failing tests first | New features, bug fixes |
| `implementer` | Minimal implementation to make tests green | After RED |
| `code-reviewer` | Quality, correctness, and convention review | Immediately after writing code |
| `typescript-reviewer` | TS/JS review — type safety, async, Node security | TS/JS diffs; pairs with vue-reviewer on `.vue` |
| `vue-reviewer` | Vue review — reactivity, template security, composables | `.vue` / Vue ecosystem diffs |
| `type-design-analyzer` | Evaluate whether types make illegal states unrepresentable | Deep type reviews of domain models and API contracts (typed stacks) |
| `code-simplifier` | Simplify code while preserving behavior exactly | REFACTOR phase, after tests are green |
| `security-reviewer` | Security review of sensitive code | Before committing auth/payment/data/crypto code |
| `build-fixer` | Repair typecheck / lint / test failures | When checks fail |
| `commit-reviewer` | Draft/check Conventional Commits messages from the staged diff | Before committing |
| `memory-keeper` | Persist progress, decisions, and learnings | After verify, at loop end |

## 5. Context Switching

Three contexts under `contexts/`. `dev` is the default; switch by reading the target file first.

| Context | File | When |
| --- | --- | --- |
| Development | `contexts/dev.md` | Default — implementing features |
| Review | `contexts/review.md` | Code review, PR analysis |
| Research | `contexts/research.md` | Fact-checking, dependency lookup |

## 6. Memory Protocol

Memory lives in `memory/` as plain Markdown, append-only, each entry headed by `date / author / status`.

- **Read** — at session start, read only `memory/progress.md` (the sole always-in-context file). Retrieve `decisions.md` / `learnings.md` with Grep on demand; do not load them fully.
- **Write** — `memory-keeper` is the **only writer** of memory. Others read only.
- **When** — write `progress.md` on task boundaries; write `learnings.md` only after `verify` passes; `planner` drafts ADRs, `memory-keeper` archives them.

## 7. Non-goals & Future

**Non-goals (by design):** hooks scripts, Node runtime enforcement, JSON Schema validation, MCP orchestration. e-agent stays declarative Markdown so it works across harnesses and is easy to audit.

**Future (when the catalog outgrows ~8 agents / ~15 skills):** introduce JSON Schema validation for agent/skill frontmatter, and PreToolUse hooks for enforcement and memory persistence — both optional layers on top of the current files.
