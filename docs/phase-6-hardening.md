# Phase 6: Hardening

**Time:** ~5 minutes | **Tokens:** Moderate

**Principles in action:**
- P11: AI code is not optimized by default
- P12: Check git diff for critical logic
- P13: You don't need an LLM call to calculate 1+1

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 6.1: Add API Tests

```
Add pytest tests for the [main resource] CRUD endpoints.
Create backend/tests/test_[resource].py with tests for:
- POST /api/[resource] — creates an item and returns 201
- GET /api/[resource] — returns a list
- GET /api/[resource]/{id} — returns one item
- GET /api/[resource]/999 — returns 404 for missing item
- DELETE /api/[resource]/{id} — deletes and returns 200

Use a test database (SQLite in-memory) so tests don't affect dev data.
Use FastAPI's TestClient.
```

**Review the diff:**
- [ ] Tests use an in-memory database, not your dev database
- [ ] Each test is independent (no shared state between tests)
- [ ] Assertions check both status codes AND response body content
- [ ] No excessive mocking — these are integration tests, not unit tests

Run the tests:
```bash
cd backend && python -m pytest -v
```

**Commit:**
```bash
git add backend/tests/
git commit -m "test: add CRUD endpoint tests for [main resource]"
```

**Verify before continuing:**
- [ ] All tests pass
- [ ] Tests run in under 5 seconds

> **Stuck?** Compare with `reference/expense-tracker/backend/tests/`

---

## Step 6.2: Security Review

Give Claude an explicit, specific security review prompt (P11):

```
Review this codebase for security issues. Check specifically for:
1. SQL injection — are we using parameterized queries everywhere?
2. CORS — is it restricted to specific origins?
3. Input validation — are all API inputs validated with Pydantic?
4. Error handling — do errors leak internal details?
5. Missing rate limiting or authentication (note it, we'll add auth later)

Don't add code. Just report what you find.
```

**What to watch for in Claude's response:**
- If Claude suggests adding an LLM-powered "smart validation" or "AI-driven security" — push back. That's P13. Simple validation functions are better.
- If Claude suggests adding a full auth system — acknowledge it but defer to "What's Next." We're hardening, not feature-creeping.
- If Claude finds real issues — fix them.

**Commit any fixes:**
```bash
git add backend/
git commit -m "fix: address security review findings"
```

---

## Step 6.3: Build the /review-diff Skill

This is the most powerful skill you'll build. It reviews your staged changes before you commit.

```bash
mkdir -p .claude/skills/review-diff
cp ../claude-code-project-tutorial/skills/review-diff/SKILL.md .claude/skills/review-diff/SKILL.md
```

Review the SKILL.md and adjust to your project. Key features:
- `context: fork` — runs in a subagent, doesn't pollute your main session
- `!`git diff --staged`` — injects the actual diff Claude will review
- `allowed-tools: Read, Grep, Glob` — Claude can check surrounding code, but can't edit anything

Test it by staging a file and running:
```
/review-diff
```

Claude should analyze the diff and report any real issues — or say "No issues found. Ready to commit."

**Commit:**
```bash
git add .claude/skills/review-diff/
git commit -m "feat: add /review-diff custom skill for pre-commit review"
```

**Verify before continuing:**
- [ ] `/review-diff` runs without errors
- [ ] It analyzes staged changes and gives actionable feedback
- [ ] It runs in a fork (doesn't add to your main context)

---

## Step 6.4: Set Up the Lint Hook

Add automatic linting after every Claude edit.

First, create the hook script:

```bash
mkdir -p .claude/hooks
```

Create `.claude/hooks/lint.sh`:
```bash
#!/bin/bash
# Run backend linter
cd "$(git rev-parse --show-toplevel)/backend" && python -m ruff check . --quiet 2>/dev/null

# Run frontend linter
cd "$(git rev-parse --show-toplevel)/frontend" && npx eslint . --quiet 2>/dev/null

exit 0  # Don't block Claude on lint warnings
```

Make it executable:
```bash
chmod +x .claude/hooks/lint.sh
```

Now add the hook to `.claude/settings.json`. Open the file and add the `hooks` section alongside the existing `permissions`:

```json
{
  "permissions": {
    ...existing permissions...
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/lint.sh",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

From now on, every time Claude edits a file, the linter runs automatically. Claude sees lint errors in real time and can self-correct.

**Commit:**
```bash
git add .claude/hooks/ .claude/settings.json
git commit -m "feat: add PostToolUse lint hook for automatic linting"
```

**Verify before continuing:**
- [ ] `.claude/hooks/lint.sh` is executable
- [ ] `.claude/settings.json` includes the hooks section
- [ ] Ask Claude to edit a file — lint output should appear after the edit

---

## Step 6.5: Diff Review Exercise

Before you commit anything else, try your new workflow:

1. Make a small change (or ask Claude to add a feature)
2. Stage the change: `git add <files>`
3. Run `/review-diff`
4. Review what Claude finds
5. Fix any issues, re-stage, commit

This is the workflow you should use for every meaningful commit going forward. It takes 30 seconds and catches real bugs.

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update tracking files for Phase 6"
git push
```

---

## Phase 6 Complete

You now have:
- API tests that run in under 5 seconds
- A security review with real findings addressed
- The `/review-diff` skill for pre-commit code review
- A lint hook that auto-catches style issues
- Three custom skills total: `/add-endpoint`, `/add-component`, `/review-diff`

**Why this phase matters:** AI code is not optimized by default (P11). Tests, security review, and pre-commit review are how you catch what Claude misses. The `/review-diff` skill alone will save you from more bugs than any other tool in this tutorial.

**Next:** [Phase 7 — Docker & Ship](phase-7-docker.md)
