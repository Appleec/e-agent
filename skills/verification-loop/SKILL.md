---
name: verification-loop
description: Run a phased verification of a session's work — surface detection, build, type check, lint, tests with coverage, security grep, and diff review — then produce a PASS/FAIL/SKIPPED verification report. Use when verifying work after completing a feature or refactor, before creating a PR, or when quality gates must pass.
---

# Verification Loop Skill

> Adapted from ECC (everything-claude-code) `skills/verification-loop`, MIT. Adds Phase 0 surface detection and quick/full modes so repos without complete tooling degrade honestly instead of reporting false failures.

A comprehensive verification system for coding sessions.

## When to Use

Invoke this skill:
- After completing a feature or significant code change
- Before creating a PR
- When you want to ensure quality gates pass
- After refactoring

## Modes

| Mode | Phases | Use |
|---|---|---|
| **quick** | 0 + 2 + 4 (detect, types, tests) | Mid-work checkpoints (`/e-agent:checkpoint`) |
| **full** | All phases | Before a PR or quality gate |

Default to full when unspecified.

## Verification Phases

### Phase 0: Surface Detection

Before running anything, detect which phases this project actually has. Do NOT run a phase that is not there — a missing tool reports SKIPPED, never FAIL.

1. List the available scripts (`npm run` with no args; or read `pyproject.toml` / `Makefile` / `go.mod` as appropriate)
2. A phase counts as available only if its script/tool exists **and is not a placeholder** — an npm-init stub like `"test": "echo \"Error: no test specified\" && exit 1"` is absence, not failure
3. Map the surface:
   - Build → `build` script exists
   - Types → `typecheck` script, or `tsconfig.json` + `tsc`/`vue-tsc` available
   - Lint → `lint` script exists
   - Tests → real `test` script exists
4. If every runnable phase is SKIPPED, the git gate stands alone: a clean `git status --porcelain` (or a diff the user confirms is intentional) is the only pass condition

### Phase 1: Build Verification
```bash
set -o pipefail  # report the build's exit status, not tail's
# Check if project builds
npm run build 2>&1 | tail -20
# OR
pnpm build 2>&1 | tail -20
```

If build fails, STOP and fix before continuing.

### Phase 2: Type Check
```bash
set -o pipefail
# TypeScript projects
npx --no-install tsc --noEmit 2>&1 | sed -n '1,30p'
# Vue projects use vue-tsc instead of plain tsc
# Python projects
pyright . 2>&1 | sed -n '1,30p'
```

Report all type errors. Fix critical ones before continuing.

### Phase 3: Lint Check

Use `sed` to limit displayed output while consuming the whole stream. This keeps a
successful linter from failing on SIGPIPE when `pipefail` is enabled.
```bash
set -o pipefail
# JavaScript/TypeScript
npm run lint 2>&1 | sed -n '1,30p'

# Python
ruff check . 2>&1 | sed -n '1,30p'
```

### Phase 4: Test Suite
```bash
set -o pipefail
# Run tests with coverage
npm run test -- --coverage 2>&1 | tail -50

# Check coverage threshold
# Target: 80% minimum
```

Report:
- Total tests: X
- Passed: X
- Failed: X
- Coverage: X%

### Phase 5: Security Scan
```bash
# Check for secrets
grep -rn "sk-" --include="*.ts" --include="*.js" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.ts" --include="*.js" . 2>/dev/null | head -10

# Check for console.log
grep -rn "console.log" --include="*.ts" --include="*.tsx" src/ 2>/dev/null | head -10
```

### Phase 6: Diff Review
```bash
# Show what changed
git diff --stat
git diff HEAD~1 --name-only
```

Review each changed file for:
- Unintended changes
- Missing error handling
- Potential edge cases

## Output Format

After running all phases, produce a verification report:

```
VERIFICATION REPORT
==================

Build:     [PASS/FAIL/SKIPPED]
Types:     [PASS/FAIL/SKIPPED] (X errors)
Lint:      [PASS/FAIL/SKIPPED] (X warnings)
Tests:     [PASS/FAIL/SKIPPED] (X/Y passed, Z% coverage)
Security:  [PASS/FAIL/SKIPPED] (X issues)
Diff:      [X files changed]

Overall:   [READY/NOT READY] for PR

Issues to Fix:
1. ...
2. ...
```

A SKIPPED phase never blocks the overall verdict on its own.

## Continuous Verification

For long sessions, verify at natural checkpoints:
- After completing each function
- After finishing a component
- Before moving to the next task

Use `/e-agent:checkpoint` to snapshot progress alongside these verification passes.
