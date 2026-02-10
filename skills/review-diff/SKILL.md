---
name: review-diff
description: Review staged git changes for bugs, security issues, and convention violations
disable-model-invocation: true
context: fork
allowed-tools: Read, Grep, Glob
---

Review the following staged changes for issues:

!`git diff --staged`

Check for:
1. **Security issues** — SQL injection, XSS, open redirects, hardcoded secrets, CORS misconfiguration
2. **Logic bugs** — Wrong variable names, off-by-one errors, timezone mismatches, using `created_at` for business dates
3. **Convention violations** — Does the new code match existing patterns in the codebase?
4. **Missing validation** — Input validation at API boundaries, error handling for external calls
5. **Over-engineering** — Unnecessary abstractions, premature optimization, dead code

Do NOT:
- Suggest style changes or formatting tweaks
- Add comments to working code
- Flag things that are fine but "could be improved"
- Recommend adding type annotations to code you didn't write

Report only real issues. If everything looks clean, say: "No issues found. Ready to commit."
