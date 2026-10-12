# Rules

## Structure

Rules are organized into a **common** layer plus **language-specific** directories:

```
rules/
├── common/          # Language-agnostic principles (always apply)
│   ├── coding-style.md
│   ├── git-workflow.md
│   ├── testing.md
│   ├── performance.md
│   ├── patterns.md
│   ├── hooks.md
│   ├── agents.md
│   ├── code-review.md
│   ├── development-workflow.md
│   └── security.md
├── typescript/      # TypeScript/JavaScript specific
│   ├── coding-style.md
│   ├── testing.md
│   ├── patterns.md
│   ├── hooks.md
│   └── security.md
└── vue/             # Vue 3 specific
    ├── coding-style.md
    ├── testing.md
    ├── patterns.md
    ├── hooks.md
    └── security.md
```

- **`common/`** contains universal principles — no language-specific code examples.
- **Language directories** extend the common rules with framework-specific patterns, tools, and code examples. Each file references its common counterpart.

## Rules vs Skills

- **Rules** define standards, conventions, and checklists that apply broadly (e.g., "80% test coverage", "no hardcoded secrets").
- **Skills** (`.claude/skills/`) provide deep, actionable reference material for specific tasks (e.g., `tdd`, `typescript-strict`, `security-checklist`).

Language-specific rule files reference relevant skills where appropriate. Rules tell you _what_ to do; skills tell you _how_ to do it.

## Adding a New Rule Set

To add support for a new language or domain (e.g., `python/`):

1. Create a `rules/python/` directory
2. Add files that extend the common rules:
   - `coding-style.md` — formatting tools, idioms, error handling patterns
   - `testing.md` — test framework, coverage tools, test organization
   - `patterns.md` — language-specific design patterns
   - `hooks.md` — hooks for formatters, linters, type checkers
   - `security.md` — secret management, security scanning tools
3. Each file should start with:
   ```
   > This file extends [common/xxx.md](../common/xxx.md) with <Language> specific content.
   ```
4. Reference existing skills if available, or create new ones under `.claude/skills/`.

## Rule Priority

When language-specific rules and common rules conflict, **language-specific rules take precedence** (specific overrides general). This follows the standard layered configuration pattern.

- `rules/common/` defines universal defaults applicable to all projects.
- `rules/typescript/`, `rules/vue/`, etc. override those defaults where language idioms differ.

### Common rules with override notes

Rules in `rules/common/` that may be overridden by language-specific files are marked with:

> **Language note**: This rule may be overridden by language-specific rules for languages where this pattern is not idiomatic.
