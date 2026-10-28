---
description: Comprehensive PR review using specialized agents (code-reviewer, typescript-reviewer, vue-reviewer, silent-failure-hunter, pr-test-analyzer, type-design-analyzer, code-simplifier). Use for a multi-agent PR review pass; for the standalone step-by-step checklist review use /code-review.
---

> Adapted from ECC (everything-claude-code) `commands/review-pr.md`, MIT. Only ECC's `comment-analyzer` has no e-agent equivalent — `pr-test-analyzer` and `silent-failure-hunter` were ported at v0.2.27 and restored to the agent list. The `/orch-review` reference was removed.

Run a comprehensive multi-perspective review of a pull request.

## Usage

`/review-pr [PR-number-or-URL] [--focus=errors|tests|types|code|simplify]`

If no PR is specified, review the current branch's PR. If no focus is specified, run the full review stack.

## Steps

1. Identify the PR:
   - use `gh pr view` to get PR details, changed files, and diff
2. Find project guidance:
   - look for `CLAUDE.md`, lint config, TypeScript config, repo conventions
3. Run specialized review agents:
   - `code-reviewer`
   - `typescript-reviewer`
   - `vue-reviewer`
   - `silent-failure-hunter`
   - `pr-test-analyzer`
   - `type-design-analyzer`
   - `code-simplifier`
4. Aggregate results:
   - dedupe overlapping findings
   - rank by severity
5. Report findings grouped by severity

## Confidence Rule

Only report issues with confidence >= 80:

- Critical: bugs, security, data loss
- Important: missing tests, quality problems, style violations
- Advisory: suggestions only when explicitly requested
