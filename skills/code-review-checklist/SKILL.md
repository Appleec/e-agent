---
name: code-review-checklist
description: Project-level code review checklist. Use when reviewing a diff for correctness, simplicity, and convention compliance.
---

# Code Review Checklist

Project-level checklist. The full standard lives in `rules/common/code-review.md`; the global `code-review` skill provides the general review recipe. This file pins the project specifics.

## Before reporting a finding

- [ ] Can I cite the exact file:line?
- [ ] Can I describe the concrete failure mode (input → state → bad outcome)?
- [ ] Have I read the surrounding code (callers, imports, tests)?
- [ ] Is the severity defensible?

Only report findings you are >80% confident about.

## Checklist

- **Correctness** — logic errors, edge cases, error handling
- **Immutability** — no in-place mutation (see `rules/common/coding-style.md`)
- **TypeScript** — no `any`, `unknown` + narrowing, `interface` vs `type` (see `typescript-strict` skill)
- **Vue** — Composition API + `<script setup>` (see `vue-best-practices` skill)
- **Conventions** — file size 200–400 lines, functions <50 lines, nesting ≤4
- **Security** — for sensitive code, escalate to `security-reviewer`

## Output

Group findings by severity (CRITICAL → HIGH → MEDIUM → LOW), each with file:line, failure scenario, and suggested fix.
