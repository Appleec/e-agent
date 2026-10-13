# Development Workflow

> This file extends [common/git-workflow.md](./git-workflow.md) with the full feature development process that happens before git operations.

The Feature Implementation Workflow describes the development pipeline: research, planning, TDD, implementation, code review, verification, committing to git, and persisting memory. See `SOUL.md` §2 for the core loop.

## Feature Implementation Workflow

0. **Research & Reuse** _(mandatory before any new implementation)_
   - Use the **researcher** agent.
   - **GitHub code search first:** Run `gh search repos` and `gh search code` to find existing implementations, templates, and patterns before writing anything new.
   - **Library docs second:** Use primary vendor docs to confirm API behavior, package usage, and version-specific details before implementing.
   - **Check package registries:** Search npm and other registries before writing utility code. Prefer battle-tested libraries over hand-rolled solutions.
   - **Search for adaptable implementations:** Look for open-source projects that solve 80%+ of the problem and can be forked, ported, or wrapped.
   - Prefer adopting or porting a proven approach over writing net-new code when it meets the requirement.

1. **Plan First**
   - Use **planner** agent to create implementation plan
   - Generate planning docs before coding: PRD, architecture, system_design, tech_doc, task_list
   - Identify dependencies and risks
   - Break down into phases
   - Architectural decisions become ADR drafts, archived by **memory-keeper**

2. **TDD Approach**
   - Use **tdd-guide** agent
   - Write tests first (RED)
   - Implement to pass tests (GREEN)
   - Refactor (IMPROVE) — use **code-simplifier** agent for behavior-preserving simplification
   - Verify 80%+ coverage

3. **Implement**
   - Use **implementer** agent to write the minimal code that turns tests green
   - No speculative features; keep files 200–400 lines, functions <50 lines, nesting ≤4

4. **Code Review**
   - Use **code-reviewer** agent immediately after writing code
   - Address CRITICAL and HIGH issues
   - Fix MEDIUM issues when possible

5. **Verify**
   - Run typecheck (`npm run typecheck`), lint (`npm run lint`), and tests (`npm run test`)
   - If anything fails, use **build-fixer** agent until all green
   - For security-sensitive code (auth, payments, user data, DB queries, crypto), stop and use **security-reviewer** agent before committing

6. **Commit & Push**
   - Use **commit-reviewer** agent to draft the message from the staged diff (matches the repo's actual history norms; flags mixed-concern diffs that should be split)
   - Detailed commit messages
   - Follow conventional commits format
   - See [git-workflow.md](./git-workflow.md) for commit message format and PR process

7. **Remember**
   - After verify passes, use **memory-keeper** agent to persist progress, decisions, and learnings to `memory/`

8. **Pre-Review Checks**
   - Verify all automated checks (CI/CD) are passing
   - Resolve any merge conflicts
   - Ensure branch is up to date with target branch
   - Only request review after these checks pass
