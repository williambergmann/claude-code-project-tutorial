# Phase 7: Hardening

**Time:** ~5 minutes | **Tokens:** Moderate

**Principles in action:**
- P11: AI code is not optimized by default — you must explicitly ask for security
- P13: You don't need an LLM call to calculate 1+1
- P12: Check git diff for critical logic
- P9: Own your prompts, own your workflow — build the /review-diff skill

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 7.1: Security Review

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

## Step 7.2: Accessibility Audit

```
Audit the frontend for basic accessibility issues:
- Missing alt text on images
- Form inputs without labels
- Missing keyboard navigation (can you tab through the app?)
- Color contrast issues
- Missing ARIA attributes on interactive elements

Report what you find. Fix the straightforward issues.
```

**Commit any fixes:**
```bash
git add frontend/src/
git commit -m "fix: address accessibility audit findings"
```

---

## Step 7.3: Build the /review-diff Skill

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

## Step 7.4: Set Up the Lint Hook

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

## Step 7.5: Diff Review Exercise

Before you commit anything else, try your new workflow:

1. Make a small change (or ask Claude to add a feature)
2. Stage the change: `git add <files>`
3. Run `/review-diff`
4. Review what Claude finds
5. Fix any issues, re-stage, commit

This is the workflow you should use for every meaningful commit going forward. It takes 30 seconds and catches real bugs.

Now let Claude wrap up the tracking files:

```
Review what we built in Phase 7. Then:
1. Add a Phase 7 entry to CHANGELOG.md — mention security findings, a11y fixes, the new skill, and the lint hook
2. Check off completed items in REQUIREMENTS.md for Phase 7
3. Update BUGS.md — log any security or accessibility issues found (even if already fixed)
```

Review what Claude wrote.

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md BUGS.md
git commit -m "docs: update tracking files for Phase 7"
git push
```

---

## Phase 7 Complete

You now have:
- A security review with real findings addressed
- An accessibility audit with fixes applied
- The `/review-diff` skill for pre-commit code review
- A lint hook that auto-catches style issues
- Three custom skills total: `/add-endpoint`, `/add-component`, `/review-diff`

**Why this phase matters:** AI code is not optimized by default (P11). Security review, accessibility audit, and pre-commit review are how you catch what Claude misses. The `/review-diff` skill alone will save you from more bugs than any other tool in this tutorial.

**Next:** [Phase 8 — CI/CD](phase-8-cicd.md)
