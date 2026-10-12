---
name: security-checklist
description: Security review checklist for auth, user input, DB queries, crypto, and Vue XSS vectors. Use before committing security-sensitive code.
---

# Security Checklist

Extends `rules/common/security.md`, `rules/typescript/security.md`, and `rules/vue/security.md` with a concrete audit list.

## Auth

- [ ] No secrets in client code, env vars committed, or logs
- [ ] Sessions/tokens validated server-side, not only client-side
- [ ] No hardcoded credentials; secrets come from environment or a vault

## User input

- [ ] All external input validated (Zod at the boundary) and treated as untrusted
- [ ] No SQL/command injection from concatenated strings
- [ ] Output is escaped on render

## DB queries

- [ ] Parameterized queries only — never string-built SQL
- [ ] No mass assignment from raw request bodies

## Crypto

- [ ] No hand-rolled crypto; use battle-tested libraries
- [ ] Hashes salted; keys rotated; no secrets in git history

## Vue XSS vectors (frontend)

- [ ] Avoid `v-html` with untrusted content; prefer interpolation (`{{ }}`)
- [ ] No dynamic components from untrusted input
- [ ] Sanitize `props` before passing into `v-html` or `href`/`src` bindings
- [ ] No `eval`/`new Function` with user data

## Report format

For each finding: severity, file:line, exploit scenario, remediation. Give a go / no-go verdict for commit.
