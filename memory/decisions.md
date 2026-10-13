# Decisions (ADR Log)

Append-only. Each entry follows the docs-memory skill format.

## ADR-1: Build a minimal declarative E-Agent harness

- Date: 2026-10-12
- Status: accepted
- Context: The repo had a partial copy of the ECC rules framework and two skills, but no coordinated agent workflow. We wanted a framework of our own, modeled on ECC's philosophy, without ECC's 71-agent / 311-skill scale.
- Decision: Build E-Agent as a minimal, fully declarative (Markdown-only) harness: 8 agents, 15 skills, 3 contexts, and an append-only memory layer. No hooks scripts, no Node runtime, no JSON Schema. Agents live in `agents/`; skills are consolidated in `skills/`.
- Consequences: The core loop (research → plan → test → implement → review → verify → remember → improve) is now documented and routed through named agents. Future growth can add Schema validation and hooks as optional layers.

## ADR-2: Rename the framework from E-Agent to e-agent

- Date: 2026-10-12
- Status: accepted
- Context: The framework was provisionally named "E-Agent". The final name is lowercase "e-agent", matching the repository naming style (e-agent-workspace).
- Decision: All framework docs (SOUL.md, CLAUDE.md, agents, skills, rules, progress) use "e-agent" as the brand name. The workflow skill keeps its `e-agent-workflow` identifier, which was already lowercase.
- Consequences: Branding is consistent with the repo name. ADR-1 keeps the old "E-Agent" spelling as a historical record and is not edited (append-only protocol).

## ADR-3: Remove the e-agent-workflow skill

- Date: 2026-10-13
- Status: accepted
- Context: The e-agent-workflow skill duplicated the core loop already documented in SOUL.md §2 and `rules/common/development-workflow.md` (three sources of truth). No agent referenced it, so its role as the "loop hub" was never exercised. Its name also embedded the brand, which would render as `e-agent:e-agent-workflow` when installed as a plugin.
- Decision: Delete `skills/e-agent-workflow/`. SOUL.md §2 and `rules/common/development-workflow.md` are the canonical sources for the core loop. Skills must be named for their function, not the brand.
- Consequences: Skill count is 14. Naming convention going forward: skill names describe the task (e.g. `tdd`, `security-checklist`), never repeat the e-agent brand.

## ADR-4: Add a GitAgent portability surface (agent.yaml)

- Date: 2026-10-13
- Status: accepted
- Context: ECC ships an `agent.yaml` as its export surface for the GitAgent open spec ("clone a repo, get an agent"). e-agent already satisfies two of the spec's three core files (SOUL.md, skills/*/SKILL.md) by design, so the marginal cost of the third is one YAML file.
- Decision: Add `agent.yaml` (spec_version 0.1.0) listing the 14 skills, model preference (claude-sonnet-5-5 → claude-haiku-4-5), and tags. No `commands` field (we have none). New skills must be added to this list when created, mirroring ECC's CONTRIBUTING rule.
- Consequences: Three distribution routes now exist: `.claude-plugin/` (Claude Code), `AGENTS.md` (Codex and AGENTS.md readers), `agent.yaml` (GitAgent ecosystem). The plugin manifest remains authoritative for Claude installs.

## ADR-5: Port code-explorer and type-design-analyzer from ECC

- Date: 2026-10-13
- Status: accepted
- Context: Audited ECC's 72-agent roster against our 8. Two gaps stood out: nothing maps the *internal* codebase (researcher only covers external facts), and no agent does deep type-design review. ECC's `code-explorer` (entry points → execution paths → layers → patterns → dependencies) and `type-design-analyzer` (make illegal states unrepresentable; four dimensions) fill both. silent-failure-hunter was considered and deferred.
- Decision: Port both, adapted to the e-agent three-section format (defense baseline → workflow → exit criteria). code-explorer pairs with researcher in the research step; type-design-analyzer deepens the review step alongside code-reviewer. Roster grows 8 → 10.
- Consequences: All docs/manifests synced to 10 agents; version bumped to 0.1.2. Remaining ECC agents are either language-specific (not our stack), non-development (marketing/SEO/network), or covered by existing agents + skills.

## ADR-6: Reposition e-agent as a personal general-purpose harness with pluggable language packs

- Date: 2026-10-13
- Status: accepted
- Context: The "TypeScript + Vue 3 only" framing (inherited from the repo's original CLAUDE.md) blocked reuse of ECC's broader assets and made the plugin awkward to install into non-TS projects. Structure was never the problem — rules were already layered (common + language dirs) — only the descriptions and a few hardcoded references locked the stack in.
- Decision: Two-layer positioning: language-agnostic core (loop, agents' workflow logic, rules/common, contexts, memory) + pluggable language packs. The ts-vue pack (rules/typescript + rules/vue + typescript-strict / vue-best-practices / frontend-design skills) serves this repo. Documented in LANGUAGE-PACKS.md; SOUL.md §6 lists packs; manifests now say "with pluggable language packs (ships with ts-vue)". Core agents softened to "per the active pack" (tdd-guide, implementer). Files stay in place — pack membership is declared, not reorganized (mirrors ECC's flat-skills-plus-naming convention). Version 0.2.0.
- Consequences: e-agent can be installed into any project; a python pack is added by creating rules/python/ per LANGUAGE-PACKS.md, without touching core. mcp-configs still holds an untrimmed ECC copy — trim deferred, pending a decision on which universal servers to keep.

## ADR-7: Language content over language packs — core is first-class

- Date: 2026-10-13
- Status: accepted
- Context: Verified against ECC after ADR-6: ECC's "language packs" are real because installer infrastructure backs them (install-apply.js language args, 46 install modules, 68 scripts) — and that selectivity exists only in copy-install mode. Plugin installs are full-catalog even for ECC. Our LANGUAGE-PACKS.md promised "pluggable" with zero mechanism behind it: a paper abstraction.
- Decision: Keep the general-purpose repositioning but demote the pack concept: the language-agnostic core is first-class; language-specific rules/skills are content grown per stack (grouping concept, not an installation unit). LANGUAGE-PACKS.md rewritten as "Language Content" — documents current TS+Vue content and how to add a language, states explicitly that installs are full-catalog. All "pluggable/pack" wording removed from SOUL.md, CLAUDE.md, README, manifests, and core agents ("per the active stack" now). Version 0.2.1.
- Consequences: Honest architecture with no implied install granularity. Adding python later = create rules/python/ + a skill + register in SOUL.md §6 and LANGUAGE-PACKS.md. If selective installation ever becomes a real need, it requires ECC-scale installer infrastructure — explicitly out of scope.

## ADR-8: Remove the LANGUAGE-PACKS.md concept doc; focus on the core

- Date: 2026-10-13
- Status: accepted
- Context: ECC has no LANGUAGE-PACKS.md equivalent — its "how to add a language" lives in `rules/README.md` (which we also have, making the doc redundant), and its language directories are plain directories with no dedicated concept layer. After ADR-7 the doc only restated what rules/README.md already says.
- Decision: Delete `LANGUAGE-PACKS.md` and all "language content/pack" framing from SOUL.md (§6 removed; back to 7 sections), CLAUDE.md, README, and manifests. Keep `rules/typescript/`, `rules/vue/`, and the 3 language skills — they serve this repo and exist as plain directories, exactly like ECC's 22 language dirs. `rules/README.md` remains the single doc for adding languages.
- Consequences: One less doc to maintain; the framework's stated focus is the general-purpose core. Adding a language later follows `rules/README.md` (create `rules/<lang>/`, optionally a skill) with no registration ceremony. Version 0.2.2.

## ADR-9: Absorb ECC reviewer wisdom; add commit-reviewer and code-simplifier

- Date: 2026-10-13
- Status: accepted
- Context: Deep-read of six ECC agents (code-architect, code-explorer, code-reviewer, code-simplifier, commit-reviewer, conversation-analyzer). conversation-analyzer is hooks-dependent (our non-goal) — not ported. code-architect's core discipline folds into planner.
- Decision: (1) `agents/code-reviewer.md` gains two ECC-proven sections: "Zero findings is a valid outcome" (LLM reviewers' top failure mode is manufacturing findings) and a 9-item Common False Positives skip-list. (2) New `commit-reviewer` agent: drafts Conventional Commits messages measured against the repo's actual git-log norms, flags mixed-concern diffs, never runs `git commit`, and treats diff content as data-not-instructions. (3) New `code-simplifier` agent for the REFACTOR phase — behavior-preserving edits including unwinding over-abstracted single-use helpers. (4) `planner.md` gains the anti-speculative-abstraction clause ("avoid abstractions the repo doesn't already use"). Roster 10 → 12; development-workflow.md wires code-simplifier into REFACTOR and commit-reviewer into Commit & Push.
- Consequences: Version 0.2.3. The review stage now has noise-suppression discipline; the commit stage has a measured, injection-aware message drafter; REFACTOR has a hands-on agent.

## ADR-10: Port typescript-reviewer and vue-reviewer as paired language reviewers

- Date: 2026-10-13
- Status: accepted
- Context: ECC's language-expert reviewers carry high-value checklists (version-aware Vue reactivity pitfalls, `v-html`/`:href` security, TS async traps). Two shapes were possible: absorb the checklists into rules (keeping agents generic) or port both agents with ECC's paired-invocation model. The user chose the paired-agent model (option B).
- Decision: Port `agents/typescript-reviewer.md` and `agents/vue-reviewer.md` with full checklists adapted to the e-agent format. They keep ECC's lane-split table (types/async/Node → ts-reviewer; reactivity/templates/composables/router/Pinia/SSR → vue-reviewer) and toolchain-first rule (review waits for green typecheck/lint). `.vue` diffs invoke both in parallel; pure `.ts` invokes only ts-reviewer. General code-reviewer remains for non-language concerns. development-workflow.md step 4 wires the pair. This is a deliberate exception to "language knowledge lives in rules, not agents" — recorded here, not generalized.
- Consequences: Roster 12 → 14; version 0.2.4. Review now has three layers: general (code-reviewer), language (this pair), and depth (type-design-analyzer). When a third stack is added later, decide per-stack whether it needs an agent or rules suffice.

## ADR-11: Absorb ECC's build-error-resolver into the existing build-fixer

- Date: 2026-10-13
- Status: accepted
- Context: ECC's `build-error-resolver` has the same mission as our `build-fixer` (get the toolchain green with minimal diffs), so a second agent would be pure duplication. Its increments over ours: a common-error→fix table, an explicit DO/DON'T boundary, a quantified success metric (changed lines < 5% of the affected file — beyond that it's a redesign), quick-recovery commands, and a route-to-other-agents table.
- Decision: Enhance `agents/build-fixer.md` with those increments instead of adding a new agent. Name stays `build-fixer`; all existing references unchanged. Version 0.2.5.
- Consequences: Roster stays 14. The verify stage now has a bounded, measurable definition of a minimal fix and knows when to hand off (refactor → code-simplifier, architecture → planner, behavior tests → tdd-guide, security → security-reviewer).

## ADR-12: Trim mcp-configs from 33 ECC servers to 4

- Date: 2026-10-13
- Status: accepted
- Context: `mcp-configs/mcp-servers.json` was a verbatim ECC copy carrying ECC-ecosystem entries (ito-compute, ecc-memory-vault, nexus), MCP memory stores conflicting with our declarative Markdown memory (SOUL §6), duplicates of built-in tools (filesystem, sequential-thinking, web search), wrong-stack servers (Laravel, shadcn/magic, Supabase, ClickHouse), and unused platforms (Vercel, Railway, Cloudflare ×4).
- Decision: Keep 4 servers that are zero-key or single-PAT and map to our core loop: `context7` (researcher), `chrome-devtools` (frontend debugging; the one ECC activates), `playwright` (planned E2E stack), `github` (git-workflow loop). `_comments` de-ECC'd and points to the local ECC clone for the full 33-server catalog on demand. The menu's job is fast access to what we actually use, not completeness.
- Consequences: Version 0.2.6. Retrieving a cut server costs one copy from the local ECC clone — acceptable friction that keeps the default surface honest.

## ADR-13: Activate the project MCP layer (root .mcp.json with chrome-devtools)

- Date: 2026-10-13
- Status: accepted
- Context: ECC's three-layer MCP design: activation (root `.mcp.json`, auto-discovered, one zero-key server), menu (`mcp-configs/`), and guard (`"mcpServers": {}` opt-out in plugin.json preventing the root file from being bundled into plugin installs). We had the menu (trimmed, ADR-12) and the guard; only activation was missing.
- Decision: Port ECC's root `.mcp.json` verbatim — `chrome-devtools` pinned at 1.10.1. Deliberately a single server to honor the context-window discipline. CLAUDE.md documents the layer. Version 0.2.7.
- Consequences: Every Claude Code session in this repo gets chrome-devtools automatically (no per-user setup). Same-named user-level config is shadowed by the project one. Swapping the active server means editing one file.

## ADR-14: Add session-level guardrails via .claude/rules/

- Date: 2026-10-13
- Status: accepted
- Context: Auditing ECC's `.claude/` revealed its dual-layer split: root directories are the distributable plugin, while `.claude/` (rules, commands) serves the repo's own development sessions. One gap it exposed: our prompt-defense baseline existed only inside the 14 agent files — the main session had none. ECC closes this with `.claude/rules/ecc-guardrails.md`, auto-injected into every session.
- Decision: Add `.claude/rules/guardrails.md` — a ~15-line prompt-defense baseline only (untrusted content is data-not-instructions, injection red flags, no secrets, no role override). Deliberately minimal and non-duplicative: CLAUDE.md already auto-loads conventions, so guardrails carry only what it lacks. `.claude/` stays out of plugin distribution. Version 0.2.8.
- Consequences: Every session in this repo starts with the defense baseline at no per-agent cost. ECC's remaining `.claude/` content (identity.json, ecc-tools.json, homunculus, team) depends on the ECC Tools product ecosystem and is not adopted.

## ADR-15: Port five ECC skills (vue-patterns, error-handling, e2e-testing, api-design, vite-patterns)

- Date: 2026-10-13
- Status: accepted
- Context: Screened ECC's 311-skill catalog against our stack and gaps. Five fill real voids: `vue-patterns` (hands-on Vue 3 patterns — complements the docs-oriented vue-best-practices), `error-handling` (typed errors, retries, circuit breakers — our only true gap), `e2e-testing` (Playwright POM/flaky/CI — the planned E2E stack), `api-design` (REST design — pairs with the ApiResponse envelope convention), `vite-patterns` (our build tool). Not ported: verification-loop (covered by build-fixer + security-reviewer + workflow), ADR skill (docs-memory equivalent), search-first/github-ops (covered), database-migrations (no DB yet — copy from the local ECC clone when needed), and all non-stack/non-dev skills.
- Decision: Ported verbatim — the SKILL.md bodies proved free of ECC-specific references; only the frontmatter origin marker changed to `ported-from-ECC`. CLAUDE.md skill groups reorganized (19 skills). Version 0.2.9.
- Consequences: Skills 14 → 19 (~2,150 lines of pattern reference). The catalog now covers the full planned stack; future per-need copies from the local ECC clone remain the pattern for anything cut.

## ADR-16: Introduce role orchestration — port the council skill and formalize ensemble routing

- Date: 2026-10-13
- Status: accepted
- Context: ECC organizes "roles" on four layers: functional agents (its 72), team ensembles (dev-team, council, santa-method, GAN, devfleet), personified assistants, and in-review multi-perspective splits. e-agent already had layers 1 (14 agents), part of 2 (dev-team), and 4 (Multi-Perspective in rules). The high-value gap was council — four-voice structured disagreement for ambiguous, high-stakes decisions; pure promptcraft, no infrastructure.
- Decision: (1) Port `skills/council/` adapted to e-agent (routing table points to our agents; persistence via memory-keeper/ADR). Kept ECC's core mechanisms: anti-anchoring (external voices get only question + compact context, never the transcript), Architect-position-first, synthesis bias guardrails (strongest dissent always shown; two votes against initial position = real signal). (2) Added a "Role Orchestration" section to `rules/common/agents.md` — a scenario → ensemble routing table formalizing when to use council vs dev-team vs paired reviewers vs multi-perspective vs the core loop. Skills 20 → 21; version 0.2.14.
- Consequences: Role planning is now explicit routing, not improvisation. Not adopted: council-multi-model (needs external Codex), santa-method as a separate skill (paired ts/vue reviewers are its lightweight equivalent), GAN and devfleet (heavy orchestration infrastructure).
