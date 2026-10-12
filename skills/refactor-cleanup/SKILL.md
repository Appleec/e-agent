---
name: refactor-cleanup
description: Simplification and cleanup checklist for changed code. Use to remove dead code, reduce complexity, and improve naming without changing behavior.
---

# Refactor & Cleanup

Quality-only cleanup. The full standard lives in `rules/common/patterns.md`; the global `simplify` skill provides the general recipe. This file pins project specifics.

## Rules

- Never change behavior while refactoring — keep tests green.
- Run tests after every step; stop at the first failure and revert that step.

## Checklist

- [ ] Dead code removed (unused imports, variables, functions)
- [ ] Duplication extracted into a shared helper or composable
- [ ] Deep nesting flattened to ≤4 levels (early returns, guard clauses)
- [ ] Long functions split to <50 lines
- [ ] Names clarify intent, not implementation
- [ ] Magic numbers/strings extracted to named constants
- [ ] Complex conditionals replaced with named predicates

## Output

A list of applied cleanups (file:line, before → after) plus confirmation that tests still pass.
