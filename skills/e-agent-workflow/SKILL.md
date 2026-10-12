---
name: e-agent-workflow
description: The e-agent core loop operating manual. Use when starting any feature, bug fix, or refactor to route work through research → plan → test → implement → review → verify → remember → improve, or when unsure which agent to invoke next.
---

# e-agent Workflow

The core loop that ties the whole framework together. Read `SOUL.md` first for identity and principles; this skill is the operating manual for the loop.

## The Loop

```
research → plan → test → implement → review → verify → remember → improve
```

Each step says: which agent to invoke, which context to read, and where the output lands.

## Steps

### 1. Research — `researcher`

- Read `contexts/research.md`.
- Invoke `researcher` before writing anything new: `gh search` → primary docs → npm → web.
- Output: research notes (ephemeral). Prefer adopting an existing solution over new code.

### 2. Plan — `planner`

- Read `contexts/dev.md` and `memory/progress.md`.
- Invoke `planner` to produce PRD, architecture, and a task list.
- Architectural decisions become ADR drafts, archived by `memory-keeper` into `memory/decisions.md`.

### 3. Test — `tdd-guide`

- Read `rules/common/testing.md` and the `tdd` skill.
- Invoke `tdd-guide` to write failing tests first (RED).

### 4. Implement — `implementer`

- Read `rules/common/coding-style.md` and `rules/typescript/`.
- Invoke `implementer` to write the minimal code that turns tests green (GREEN).

### 5. Review — `code-reviewer`

- Read `contexts/review.md`.
- Invoke `code-reviewer` immediately after writing code. Address CRITICAL and HIGH findings; fix MEDIUM where possible.

### 6. Verify — `build-fixer` · `security-reviewer`

- Run `npm run typecheck`, `npm run lint`, `npm run test`. If anything fails, invoke `build-fixer`.
- For sensitive code (auth, payments, user data, DB, crypto), invoke `security-reviewer` before commit.

### 7. Remember — `memory-keeper`

- After verify passes, invoke `memory-keeper` to persist progress and learnings into `memory/`.
- `memory-keeper` is the only writer of `memory/`.

### Improve

- Loop back to review for refactoring opportunities. Keep each iteration small and green.

## Rules of the Loop

- **Delegate and wait.** Never fire-and-forget; wait for each agent's result and integrate it.
- **No plan, no code.** Do not implement before `planner` (or an equivalent short plan) has run.
- **Memory is append-only.** Never edit past decisions or learnings.
