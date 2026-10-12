---
name: docs-memory
description: Conventions for writing READMEs, ADRs, and the memory/ directory. Use when persisting progress, decisions, or learnings.
---

# Docs & Memory Conventions

How to write docs and the `memory/` files consistently. Used by `memory-keeper` (the only writer of `memory/`).

## Memory file format

Plain Markdown, append-only. Every entry headed by `date / author / status`.

```markdown
## 2026-10-12 / memory-keeper / done

One or two lines of the fact or outcome.
```

Never edit past entries — append new ones only.

## `memory/progress.md`

Current progress, WIP, next steps. Read at every session start.

```markdown
# Progress

## Current
- <what is in flight>

## Next
- [ ] <next step>
```

## `memory/decisions.md` (ADRs)

One entry per architectural decision, with the standard five fields:

```markdown
## ADR-<n>: <title>
- Date: <yyyy-mm-dd>
- Status: proposed | accepted | superseded
- Context: <why the decision is needed>
- Decision: <what was decided>
- Consequences: <what follows from it>
```

## `memory/learnings.md`

Lessons written only after `verify` passes.

```markdown
## 2026-10-12 / memory-keeper / done
- <lesson> — <context>
```

## README and docs

- Keep files 200–400 lines; split long files instead of growing them.
- Link related docs with relative paths.
- Write in the same language and tone as the surrounding docs.
