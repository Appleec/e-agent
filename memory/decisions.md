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
