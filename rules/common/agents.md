# Agent Orchestration

## Available Agents

e-agent agents live in `agents/` and are invoked through the Agent tool. When the repo is installed as a plugin, agents are scoped as `e-agent:<name>` (e.g. `e-agent:planner`); the bare name also resolves. See `SOUL.md` §4 for the full roster.

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| researcher | Verify facts, find existing solutions | Before writing new code |
| code-explorer | Map unfamiliar code, trace execution paths | Before large changes |
| planner | Implementation planning | Complex features, refactoring |
| tdd-guide | Test-driven development | New features, bug fixes |
| implementer | Minimal implementation to green | After RED tests |
| code-reviewer | Code review | After writing code |
| typescript-reviewer | TS/JS review (type safety, async, Node security) | TS/JS diffs |
| vue-reviewer | Vue review (reactivity, template security, composables) | .vue / Vue ecosystem diffs |
| type-design-analyzer | Deep type design review | Domain models, API contracts |
| code-simplifier | Behavior-preserving simplification | REFACTOR phase |
| security-reviewer | Security analysis | Before committing sensitive code |
| build-fixer | Fix typecheck/lint/test failures | When checks fail |
| commit-reviewer | Conventional Commits message drafting | Before committing |
| memory-keeper | Persist progress/decisions/learnings | After verify |

## Immediate Agent Usage

No user prompt needed:
1. Complex feature requests - Use **planner** agent
2. Code just written/modified - Use **code-reviewer** agent
3. Bug fix or new feature - Use **tdd-guide** agent
4. Verify failures (typecheck/lint/test) - Use **build-fixer** agent
5. Sensitive code before commit - Use **security-reviewer** agent

## Parallel Task Execution

ALWAYS use parallel Task execution for independent operations:

```markdown
# GOOD: Parallel execution
Launch 3 agents in parallel:
1. Agent 1: Security analysis of auth module
2. Agent 2: Performance review of cache system
3. Agent 3: Type checking of utilities

# BAD: Sequential when unnecessary
First agent 1, then agent 2, then agent 3
```

## Delegation Completion Contract

Applies to every agent at every depth (parent, child, grandchild):

1. **Your final message IS the deliverable.** Never end your turn with "waiting for background agents" — a spawned task is not a completed task. Ending your turn while children are running orphans their results (completed children cannot notify a parent whose turn has ended).
2. **If you delegate, you own collection.** Wait for results, integrate them, then return. Fire-and-forget delegation is forbidden.
3. **Decompose only when the work cannot fit in one context.** Do not re-delegate a task already sized for a single agent — depth is an outcome, not a plan.

> Rationale: observed failure mode — research agents followed "Parallel Task Execution" above, spawned children, and returned "waiting" as their final answer. All children completed successfully but their results were orphaned. The parallel rule without a completion contract produces zombie tasks.

## Multi-Perspective Analysis

For complex problems, use split role sub-agents:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker

## Role Orchestration

Pick the role ensemble by scenario — don't improvise a new one each time:

| Scenario | Ensemble | Shape |
| --- | --- | --- |
| Ambiguous, high-stakes decision (go/no-go, tradeoffs) | `council` skill | Four voices: Architect (in-context, position first) + Skeptic / Pragmatist / Critic as fresh subagents with no conversation history (anti-anchoring) |
| Feature design / proposal review | `dev-team` skill | Fixed four roles: PM, Architect, Developer, QA respond in parallel |
| Code review gatekeeping | `typescript-reviewer` + `vue-reviewer` (parallel) | Split lanes, each issues its own Approve/Block verdict — both must pass |
| Deep-dive on one concern | single specialist agent | e.g. `security-reviewer` for auth changes, `type-design-analyzer` for domain models |
| Complex problem analysis | Multi-Perspective (above) | Five lenses as split sub-agents |
| Everyday feature work | core loop | The 14 specialists stepped through research → plan → test → implement → review → verify → remember |

Rules: external voices in any ensemble get only the question plus compact context, never the full transcript. Never hide disagreement in the synthesis. Persist an ensemble's outcome via `memory-keeper` only when it changes something real.
