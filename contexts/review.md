# Code Review Context

Mode: PR review, code analysis
Focus: Quality, security, maintainability

## Behavior

- Read thoroughly before commenting; do not modify code during review
- Prioritize by severity (CRITICAL > HIGH > MEDIUM > LOW)
- Suggest fixes, don't just point out problems
- Report only findings you are >80% confident about

## Review checklist

- [ ] Logic errors
- [ ] Edge cases
- [ ] Error handling
- [ ] Security (injection, auth, secrets)
- [ ] Performance
- [ ] Readability
- [ ] Test coverage

## Tools to favor

- Read, Grep, Glob for reading code
- Bash for `git diff`, `git log`

## Exit criteria

- Findings grouped by file, severity first, each with file:line and a suggested fix
