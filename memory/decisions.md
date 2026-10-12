# Decisions (ADR Log)

Append-only. Each entry follows the docs-memory skill format.

## ADR-1: Build a minimal declarative E-Agent harness

- Date: 2026-10-12
- Status: accepted
- Context: The repo had a partial copy of the ECC rules framework and two skills, but no coordinated agent workflow. We wanted a framework of our own, modeled on ECC's philosophy, without ECC's 71-agent / 311-skill scale.
- Decision: Build E-Agent as a minimal, fully declarative (Markdown-only) harness: 8 agents, 15 skills, 3 contexts, and an append-only memory layer. No hooks scripts, no Node runtime, no JSON Schema. Agents live in `agents/`; skills are consolidated in `skills/`.
- Consequences: The core loop (research → plan → test → implement → review → verify → remember → improve) is now documented and routed through named agents. Future growth can add Schema validation and hooks as optional layers.
