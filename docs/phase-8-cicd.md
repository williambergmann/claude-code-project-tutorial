# Phase 8: CI/CD

**Time:** ~5 minutes | **Tokens:** Low

**Principles in action:**
- P10: Process alignment becomes critical in teams — CI enforces shared standards
- P2: Parallel agents, zero chaos — CI catches broken merges

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 8.1: Create the GitHub Actions Workflow

CI YAML is where Claude saves the most tedious time — one prompt generates a complete workflow.

```
Create a GitHub Actions workflow at .github/workflows/ci.yml that runs on every push and pull request to main.

It should:
1. Install Python 3.11 and Node 18
2. Install backend dependencies (pip install -r requirements.txt)
3. Install frontend dependencies (npm ci)
4. Run backend tests with pytest
5. Run frontend tests with vitest
6. Run backend linting with ruff
7. Run frontend linting with eslint

Cache pip and npm dependencies between runs.
Keep it in a single job — this project is small enough.
```

**Review the diff:**
- [ ] Triggers on `push` and `pull_request` to `main`
- [ ] Uses specific version numbers for Python and Node (not `latest`)
- [ ] Dependencies are cached (pip cache, npm cache)
- [ ] All 4 checks run: backend tests, frontend tests, backend lint, frontend lint
- [ ] No secrets are hardcoded
- [ ] Working directory is set correctly for backend vs frontend commands

**Commit + push:**
```bash
git add .github/workflows/ci.yml
git commit -m "ci: add GitHub Actions workflow for tests and linting"
git push
```

**Verify before continuing:**
- [ ] Go to your repo on GitHub → Actions tab
- [ ] The workflow should be running (triggered by your push)
- [ ] Wait for it to complete — all checks should pass
- [ ] If something fails, read the error log and fix it

> **Common issues:** Wrong working directory for commands, missing `requirements.txt` dependencies (like `pytest` or `ruff`), Node version mismatch.

---

## Step 8.2: Branch Protection (Optional)

This step is optional for solo projects but essential for teams (P10).

```
Show me the gh CLI commands to set up branch protection on main:
- Require CI status checks to pass before merging
- Require at least 1 pull request review (optional for solo work)
```

Run the commands Claude provides, or set up manually:
1. Go to your repo on GitHub → Settings → Branches
2. Add a branch protection rule for `main`
3. Enable "Require status checks to pass before merging"
4. Select your CI workflow as a required check

**Why this matters:** With branch protection + CI, parallel work from Phase 5 has a safety net. Two agents can work on separate branches, but nothing merges to main unless tests pass (P2 + P10).

Let Claude update the tracking files:

```
Review what we set up in Phase 8. Then:
1. Add a Phase 8 entry to CHANGELOG.md — mention the CI workflow and branch protection
2. Check off completed items in REQUIREMENTS.md for Phase 8
```

Review what Claude wrote.

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update tracking files for Phase 8"
git push
```

---

## Phase 8 Complete

You now have:
- A GitHub Actions CI pipeline that runs tests and linting on every push
- (Optional) Branch protection preventing broken code from merging to main
- Confidence that parallel work won't break the build

**Why this phase matters:** CI YAML is tedious for humans and trivial for Claude. One prompt saves 30+ minutes of googling YAML syntax and debugging runner configurations. And CI catches issues that local testing might miss — different OS, clean install, no cached state.

**What's Next teaser:** Claude Code can run headless in CI (`claude --headless`) for automated PR review. That's beyond this tutorial, but now you have the CI infrastructure to try it.

**Next:** [Phase 9 — Docker & Ship](phase-9-docker.md)
