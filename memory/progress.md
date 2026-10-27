# Progress

## Current

- e-agent framework bootstrapped: SOUL.md, 14 agents, 22 skills, 2 commands, 3 contexts, memory layer.
- **Positioning (ADR-6..8)**: personal general-purpose harness — language-agnostic core is the focus. Per-language rules/skills are plain directories (no pack concept, no LANGUAGE-PACKS.md).
- Rules layer in place and localized to e-agent (`rules/common` + `rules/typescript` + `rules/vue`; ECC references removed).
- Plugin shipped and **installed locally** (user scope, enabled). Plugin cache updated and verified at **v0.2.23** (updates at 0.2.20 / 0.2.21 / 0.2.23 all succeeded; `build-error-resolver.md` in place, 14 agents; `commands/` and `skills/verification-loop` confirmed in their cache versions); repo changes not yet committed.
- ECC naming alignment in effect (**ADR-19**): `build-fixer` fully renamed to `build-error-resolver`. Invocation is now `e-agent:build-error-resolver`; the old name no longer resolves (breaking).
- Repo distribution files in place: README.md, LICENSE (MIT), AGENTS.md (cross-harness), package.json aligned.
- No application source code yet; no build tools installed.

## Next

- [ ] Commit all v0.2.20–v0.2.23 changes (see git snapshot in History — everything is still uncommitted; current session already runs on the 0.2.23 cache)
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
