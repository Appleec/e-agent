# e-agent

A minimal, declarative agent harness — modeled on [ECC](https://github.com/affaan-m/ECC)'s philosophy, scaled to what one repo actually needs.

> Optimize the context window. Persist everything else.

Instead of rebuilding an engineering process in every prompt, e-agent installs it once: a coordinated loop of specialist agents, skills, layered rules, mode contexts, and persistent memory.

```
research → plan → test → implement → review → verify → remember → improve
```

## What's inside

| Layer | Location | Count |
| --- | --- | --- |
| Identity & principles | `SOUL.md` | — |
| Agents | `agents/` | 8 specialists (research → memory) |
| Skills | `skills/` | 14 |
| Rules | `rules/` | common → typescript → vue |
| Contexts | `contexts/` | dev / review / research |
| Memory | `memory/` | progress / decisions (ADR) / learnings |

Fully declarative: Markdown plus a plugin manifest — no hooks, no runtime scripts.

## Install (Claude Code)

```bash
claude plugin marketplace add /path/to/e-agent
claude plugin install e-agent@e-agent
```

Or from GitHub once published:

```bash
claude plugin marketplace add Appleec/e-agent
claude plugin install e-agent@e-agent
```

Installed agents are scoped as `e-agent:<name>` (e.g. `e-agent:planner`); skills load with the plugin.

## Tech stack served

TypeScript · Vue 3 (Composition API, `<script setup>`) · Pinia · vue-router · @tanstack/vue-query · Zod · Vitest + @vue/test-utils · Playwright · ESLint + Prettier

## License

[MIT](LICENSE)
