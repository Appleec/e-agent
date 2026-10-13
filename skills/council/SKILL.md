---
name: council
description: Convene a four-voice council for ambiguous decisions, tradeoffs, and go/no-go calls. Use when multiple valid paths exist and you need structured disagreement before choosing.
metadata:
  origin: ported-from-ECC
---

# Council

Convene four advisors for ambiguous decisions:
- the in-context Claude voice (Architect position)
- a Skeptic subagent
- a Pragmatist subagent
- a Critic subagent

This is for **decision-making under ambiguity**, not code review, implementation planning, or architecture design.

## When to Use

Use council when:
- a decision has multiple credible paths and no obvious winner
- you need explicit tradeoff surfacing
- the user asks for second opinions, dissent, or multiple perspectives
- conversational anchoring is a real risk
- a go / no-go call would benefit from adversarial challenge

Examples: monorepo vs polyrepo · ship now vs hold for polish · feature flag vs full rollout · simplify scope vs keep strategic breadth.

## When NOT to Use

| Instead of council | Use |
| --- | --- |
| Feature design / proposal review with fixed roles | `dev-team` skill (PM / Architect / Developer / QA) |
| Verifying whether output is correct | the paired reviewers (`typescript-reviewer` + `vue-reviewer`, both must pass) |
| Breaking a feature into implementation steps | `planner` agent |
| Reviewing code for bugs or security | `code-reviewer` / `security-reviewer` agents |
| Formalizing a decision that was made | `docs-memory` skill (ADR in `memory/decisions.md`) |
| Straight factual questions | just answer directly |

## Roles

| Voice | Lens |
| --- | --- |
| Architect | correctness, maintainability, long-term implications |
| Skeptic | premise challenge, simplification, assumption breaking |
| Pragmatist | shipping speed, user impact, operational reality |
| Critic | edge cases, downside risk, failure modes |

The three external voices must be launched as fresh subagents with **only the question and relevant context**, not the full ongoing conversation. That is the anti-anchoring mechanism.

## Workflow

### 1. Extract the real question

Reduce the decision to one explicit prompt: what are we deciding, which constraints matter, what counts as success? If the question is vague, ask one clarifying question before convening the council.

### 2. Gather only the necessary context

If codebase-specific: collect the relevant files/snippets/metrics, keep it compact. If strategic/general: skip repo snippets unless they materially change the answer.

### 3. Form the Architect position first

Before reading other voices, write down: your initial position, the three strongest reasons for it, the main risk in your preferred path. Do this first so the synthesis does not simply mirror the external voices.

### 4. Launch three independent voices in parallel

Each subagent gets the decision question, compact context, a strict role, and no conversation history. Prompt shape:

```text
You are the [ROLE] on a four-voice decision council.

Question:
[decision question]

Context:
[only the relevant snippets or constraints]

Respond with:
1. Position — 1-2 sentences
2. Reasoning — 3 concise bullets
3. Risk — biggest risk in your recommendation
4. Surprise — one thing the other voices may miss

Be direct. No hedging. Keep it under 300 words.
```

Role emphasis: **Skeptic** — challenge framing, question assumptions, propose the simplest credible alternative. **Pragmatist** — optimize for speed, simplicity, real-world execution. **Critic** — surface downside risk, edge cases, reasons the plan could fail.

### 5. Synthesize with bias guardrails

- do not dismiss an external view without explaining why
- if an external voice changed your recommendation, say so explicitly
- always include the strongest dissent, even if you reject it
- if two voices align against your initial position, treat that as a real signal
- keep the raw positions visible before the verdict

### 6. Present a compact verdict

```markdown
## Council: [short decision title]

**Architect:** [position] — [why]
**Skeptic:** [position] — [why]
**Pragmatist:** [position] — [why]
**Critic:** [position] — [why]

### Verdict
- **Consensus:** [where they align]
- **Strongest dissent:** [most important disagreement]
- **Premise check:** [did the Skeptic challenge the question itself?]
- **Recommendation:** [the synthesized path]
```

Keep it scannable.

## Persistence Rule

Only persist a decision when it changes something real. If the council materially changes the recommendation, hand the outcome to the `memory-keeper` agent (ADR in `memory/decisions.md` if it becomes long-lived policy). Do not write ad-hoc notes to shadow paths.

## Anti-Patterns

- using council for code review or plain implementation work
- feeding the subagents the entire conversation transcript
- hiding disagreement in the final verdict
- persisting every decision regardless of importance

## Example

Question: "Ship the first feature now or hold until the build toolchain is fully polished?"

Likely shape: Architect pushes structural integrity · Skeptic questions whether polish is actually the gating factor · Pragmatist asks what ships now without harming trust · Critic focuses on expectation debt and rework. The value is not unanimity — it is making the disagreement legible before choosing.
