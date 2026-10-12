# Research Context

Mode: Fact-finding, dependency lookup
Focus: Verifying facts, finding existing solutions before writing new code

## Behavior

- Cite sources for every finding (URL + what it confirms)
- Prefer existing solutions over new code
- Do not write implementation code in this mode

## Priorities

1. GitHub search (`gh search`) first
2. Primary vendor docs second
3. Package registries (npm) third
4. Broader web search only if the first three are insufficient

## Tools to favor

- WebSearch, WebFetch for external sources
- Bash for `gh search`, `npm view`
- Read for local context

## Exit criteria

- A research note: question, findings, sources, and a recommendation (adopt / port / wrap / write new)
