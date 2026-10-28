# Progress

## Current

- e-agent framework bootstrapped: SOUL.md, 29 agents, 33 skills, 24 commands, 3 contexts, memory layer.
- **Positioning (ADR-6..8)**: personal general-purpose harness — language-agnostic core is the focus. Per-language rules/skills are plain directories (no pack concept, no LANGUAGE-PACKS.md).
- Rules layer in place and localized to e-agent (`rules/common` + `rules/typescript` + `rules/vue`; ECC references removed).
- Plugin shipped and **installed locally** (user scope, enabled). Plugin cache updated and verified at **v0.2.28** (updates from 0.2.20 through 0.2.28 all succeeded; cache confirmed with 29 agents, 24 command files, and 33 skills incl. all 11 new at 0.2.28); v0.2.20–v0.2.23 changes committed as `e8d121c` on develop; v0.2.24–v0.2.28 committed as `7f09cb5` on develop.
- ECC naming alignment in effect (**ADR-19**): `build-fixer` fully renamed to `build-error-resolver`. Invocation is now `e-agent:build-error-resolver`; the old name no longer resolves (breaking).
- Repo distribution files in place: README.md, LICENSE (MIT), AGENTS.md (cross-harness), package.json aligned.
- No application source code yet; no build tools installed.

## Next

- [ ] Handle the still-uncommitted `mcp-configs/mcp-servers.json` change (adds an MCP directory entry; not part of the v0.2.20–v0.2.23 work — origin to be confirmed, already explained to the user separately; now the sole uncommitted file in the working tree)
- [ ] User re-test of `/e-agent:checkpoint` degradation fix (live since 0.2.21; no record of an actual re-test in a toolchain-less repo)
- [ ] New session smoke test: invoke `e-agent:planner` to confirm plugin-scoped agents resolve
- [ ] Bootstrap build tools: TypeScript, Vite, Vitest, ESLint, Prettier (per CLAUDE.md tech stack)
- [ ] Write the first feature through the full core loop (research → plan → test → implement → review → verify → remember)

## History

## 2026-10-27 / memory-keeper / done — v0.2.20

Ported 2 commands from ECC → new `commands/` directory (`aside`, `checkpoint`). `plugin.json` adds `"commands": ["./commands/"]` array declaration; CLAUDE.md gains a Commands entry. Command count 0 → 2.

## 2026-10-27 / memory-keeper / done — v0.2.21

Ported ECC `skills/verification-loop` (six-phase verification + a new Phase 0 toolchain check: npm-init placeholder `test` scripts are detected as "missing" rather than "failing", fixing checkpoint's degradation in repos without a toolchain). `/e-agent:checkpoint` step 1 now delegates to this skill's quick mode. Skills 21 → 22.

## 2026-10-27 / memory-keeper / done — v0.2.22

`agents/build-fixer.md` gains an ECC provenance line ("Adapted from ECC ...").

## 2026-10-27 / memory-keeper / done — v0.2.23

Full-chain rename `build-fixer` → `build-error-resolver` (git mv, frontmatter, SOUL.md ×2, CLAUDE.md, `rules/common/agents.md` ×2, `rules/common/development-workflow.md`). ADR-19 appended to `decisions.md`. Invocation is now `e-agent:build-error-resolver` — old name no longer resolves (breaking). Motivation: align with ECC naming so the two `agents/` trees can be compared file by file (user-confirmed preference, 2026-10-27).

Uncommitted git snapshot at session start (porcelain; `RM` = rename staged + post-rename edits):

```
 M .claude-plugin/marketplace.json          # version bump
 M .claude-plugin/plugin.json               # version bump + commands array (0.2.20)
 M CLAUDE.md                                # Commands entry (0.2.20), agent rename (0.2.23)
 M SOUL.md                                  # agent rename ×2 (0.2.23)
 M agent.yaml                               # version bump
RM agents/build-fixer.md -> agents/build-error-resolver.md   # 0.2.23 rename (staged)
 M mcp-configs/mcp-servers.json             # NOT part of the 0.2.20–0.2.23 work; origin to be confirmed
 M memory/decisions.md                      # ADR-19 (already appended, do not repeat)
 M package.json                             # version bump
 M rules/common/agents.md                   # agent rename ×2 (0.2.23)
 M rules/common/development-workflow.md     # agent rename (0.2.23)
?? commands/                                # new in 0.2.20
?? skills/verification-loop/                # new in 0.2.21
```

## 2026-10-27 / memory-keeper / done — commit e8d121c

All v0.2.20–v0.2.23 changes committed to develop as `e8d121c` (14 files, +468/-19, Conventional Commit with a BREAKING CHANGE footer for the build-error-resolver rename). Still uncommitted: `mcp-configs/mcp-servers.json` (adds an MCP directory entry; not part of the v0.2.20–v0.2.23 work — origin to be confirmed, already explained to the user separately). The git snapshot in the v0.2.23 entry above predates this commit.

## 2026-10-27 / memory-keeper / done — v0.2.24

Ported ECC `commands/build-fix.md` (multi-stack build detection + single-error fix loop + guardrails + recovery strategy; 67 lines, self-contained; body ported verbatim, only an ECC provenance line added). CLAUDE.md Commands entry 2 → 3; all four manifests bumped to 0.2.24 (descriptions synced to 3 commands). Plugin validate + update passed; cache `0.2.24/commands/` confirmed to hold aside / build-fix / checkpoint. Not committed — left in the working tree together with the memory/progress.md edits and the `mcp-configs/mcp-servers.json` change, awaiting user decision.

## 2026-10-27 / memory-keeper / done — v0.2.25

Ported ECC `commands/code-review.md` (290 lines, dual mode: Local Review of uncommitted changes + GitHub PR Review eight-phase flow fetch / context / review / validate / decide / report / publish / output; sole external dependency is the `gh` CLI, with degradation when `gh` is absent). Two adaptations: (1) ECC's dangling `/review-pr` and `/orch-review` routes in the description replaced with e-agent's code-reviewer / typescript-reviewer / vue-reviewer agents; (2) the provenance line notes the PR review mode originates from PRPs-agentic-eng by Wirasm, keeping ECC's original dual attribution. Body otherwise ported verbatim. CLAUDE.md Commands entry 3 → 4; all four manifests bumped to 0.2.25 (descriptions synced to 4 commands). Plugin validate + update passed; cache `0.2.25/commands/` confirmed to hold aside / build-fix / checkpoint / code-review. Not committed — left in the working tree awaiting user decision.

## 2026-10-27 / memory-keeper / done — v0.2.26

Batch port of ECC `commands/`: 20 of the 25 user-specified commands ported (plus the existing 4 = **24 commands**). 5 excluded for ECC runtime-script dependencies: `plan-canvas` (ecc-plan-canvas CLI), `sessions` (session-manager.js embedded script), `skill-health` (skills-health.js), `skill-host-compat` (validate-skill-host-compat.js), `security-scan` (AgentShield scanner + `ecc:` agent frontmatter; e-agent's security-reviewer agent + security-checklist skill already cover it).

Ported 20: feature-dev, learn, learn-eval, marketing-campaign, plan, plan-prd, pr, prp-commit, prp-implement, prp-plan, prp-pr, prp-prd, python-review, refactor-clean, resume-session, review-pr, save-session, skill-create, update-docs, vue-review. All follow "body verbatim + ECC provenance line + dangling-reference fixes": feature-dev's `code-architect` → planner; plan drops `/plan-canvas` and `/multi-plan` references and maps `tdd-workflow` → tdd; python-review switched to inline review (ECC's python-reviewer agent does not exist); review-pr's agent list reduced to e-agent's five (code-reviewer / typescript-reviewer / vue-reviewer / type-design-analyzer / code-simplifier); marketing-campaign switched to inline execution (ECC's marketing agents do not exist); learn description drops the continuous-learning-v2 reference; skill-create drops `--instincts` and instinct-* commands; save-session inlines the session-manager.js reference as filename rules. CLAUDE.md Commands entry 4 → 24 (grouped by function); all four manifests bumped to 0.2.26 (descriptions synced to 24 commands). Plugin validate + update passed; cache `0.2.26/commands/` confirmed to hold 24 files. Not committed — pending together with v0.2.24 / v0.2.25, awaiting user decision.

## 2026-10-27 / memory-keeper / done — v0.2.27

Batch port of ECC `agents/`: 16 of the 17 user-specified agents landed (15 new + `build-error-resolver` replaced with the full ECC version — ADR-20), agents/ count 14 → 29. One skipped: `doc-updater` (depends on the `scripts/codemaps/generate.ts` runtime script; doc syncing is already covered by the `/update-docs` command + docs-memory skill).

Three user decisions: (1) build-error-resolver replaced with the full ECC version (ADR-20, superseding ADR-11's compression); (2) docs-lookup ported verbatim — keeps `mcp__context7__*` tools, context7 must be enabled from mcp-configs, degrades to knowledge-based answers when not enabled; (3) doc-updater skipped.

System adaptations: ECC's 8-line Prompt Defense long baseline uniformly compressed to e-agent's 3-line guardrails form (pointing to `rules/common/security.md`); `model:` / `color:` frontmatter stripped; a few ECC skill references dropped (seo / accessibility / python-patterns → `rules/python/`, agent-self-evaluation, `scripts/evaluate.py`).

Knock-on command updates: `python-review.md` restores agent delegation; `review-pr.md` adds back pr-test-analyzer + silent-failure-hunter (only comment-analyzer has no e-agent counterpart); `feature-dev.md` restores the code-architect delegation. Roster tables updated in three places (CLAUDE.md / SOUL.md / `rules/common/agents.md`); all four manifests bumped to 0.2.27 (29 agents). Plugin validate + update passed; cache confirmed with 29 agents. Not committed — accumulating with v0.2.24–v0.2.26, awaiting user decision.

## 2026-10-27 / memory-keeper / done — v0.2.28

ECC skills analysis and batch port. Analysis covered 311 skills, sorted into four categories (already-covered equivalents / non-matching stacks / ECC infrastructure / ECC-domain-specific → ~290 skipped); the user confirmed Tier 1 + Tier 2, **11 skills ported**: accessibility, agent-self-evaluation, seo, python-patterns, conventional-commit-review, github-ops, documentation-lookup, ui-to-vue, search-first, token-budget-advisor, design-system. Left out: deep-research (depends on firecrawl/exa MCP keys) and database-migrations (kept on-demand per existing ADR).

Adaptations: agent-self-evaluation drops `scripts/evaluate.py` and hook-integration references, porting `templates/evaluation-report.md` + `references/evaluation-criteria.md` as plain Markdown; github-ops drops the ECC-specific release-checklist section; token-budget-advisor inlines the context-budget link into two estimation rules; search-first drops iterative-retrieval references, switches agent delegation to `e-agent:researcher`, and changes MCP check paths to `.mcp.json` / `mcp-configs/`; accessibility / seo / conventional-commit-review Related lists pruned to e-agent's directories. All carry ECC provenance lines.

Four agent references restored (skills now in place, returning to original form): a11y-architect → accessibility; agent-evaluator → agent-self-evaluation; seo-specialist → seo (its Reference section had been removed wholesale during the v0.2.27 port — restored now); python-reviewer → python-patterns (coexists with `rules/python/`: skill = pattern library, rules = enforced standard).

CLAUDE.md skills 22 → 33 (new Context & utility category); agent.yaml skill list +11; all four manifests bumped to 0.2.28 (33 skills). Plugin validate + update passed; cache `0.2.28/skills/` confirmed with 33 skills incl. all 11 new. Not committed — accumulating with v0.2.24–v0.2.27 (five batches: 0.2.24–0.2.28), awaiting user decision.

## 2026-10-28 / memory-keeper / done — commit 7f09cb5

The five v0.2.24–v0.2.28 batches committed to develop as `7f09cb5` (60 files, +8695/-56) — a single `feat` commit merging all five versions, following the e8d121c precedent; the message was drafted after a commit-reviewer check. Sole remaining uncommitted file: `mcp-configs/mcp-servers.json` (not from the port work; origin awaiting user confirmation). The "not committed" notes in the v0.2.24–v0.2.28 entries above predate this commit.





