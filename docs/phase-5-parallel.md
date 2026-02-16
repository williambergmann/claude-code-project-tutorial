# Phase 5: Parallel Work

**Time:** ~10 minutes | **Tokens:** Moderate (split across two sessions)

**Principles in action:**
- P2: Parallel agents, zero chaos
- P10: Process alignment becomes critical in teams

> **Start by running `/clear`** in any open Claude session.

---

## Step 5.1: Set Up Git Worktrees

You're about to run two Claude instances simultaneously on two independent features. Git worktrees give you full filesystem isolation — better than just branches.

```bash
# From your project root
git worktree add ../my-app-search -b feature/search
git worktree add ../my-app-export -b feature/export
```

This creates two separate directories, each on its own branch, both sharing the same git history.

```
my-expense-tracker/           ← main (you are here)
../my-app-search/             ← feature/search branch
../my-app-export/             ← feature/export branch
```

**Verify before continuing:**
- [ ] `git worktree list` shows 3 worktrees
- [ ] `ls ../my-app-search/` shows your project files
- [ ] `ls ../my-app-export/` shows your project files

---

## Step 5.2: Install Dependencies in Each Worktree

Each worktree has its own filesystem — you need to install dependencies separately:

```bash
# Worktree 1
cd ../my-app-search/backend && pip install -r requirements.txt
cd ../my-app-search/frontend && npm install

# Worktree 2
cd ../my-app-export/backend && pip install -r requirements.txt
cd ../my-app-export/frontend && npm install
```

Go back to your main directory:
```bash
cd /path/to/my-expense-tracker
```

---

## Step 5.3: Run Two Claudes Simultaneously

Open **two terminal windows** (or tmux panes, or two VS Code terminals).

**Terminal 1:**
```bash
cd ../my-app-search
claude
```

Give it a focused prompt for a search/filter feature:

```
Add a search bar to the [resource] list page that filters items as the user types.
The search should:
- Filter on the client side for instant results
- Search across [name/description/relevant fields]
- Show "No results found" when nothing matches
- Clear button to reset the search
Follow existing component patterns.
Include a test for the search functionality.
```

**Terminal 2:**
```bash
cd ../my-app-export
claude
```

Give it a focused prompt for an export feature:

```
Add an "Export to CSV" button to the [resource] list page.
When clicked:
- Fetch all items from the API
- Generate a CSV string with headers matching the table columns
- Download it as a .csv file using a Blob and download link
- The file should be named [resource]-export-YYYY-MM-DD.csv
No external libraries — use plain JavaScript.
Include a test for the CSV generation logic.
```

**Let both agents work.** Watch them in parallel. Notice:
- Both agents read the same CLAUDE.md — same conventions, no coordination needed
- Both agents see the same project structure — consistent patterns
- They're working on independent features in independent directories

This is P2 in action. It only works because Phase 0-1 established clean patterns that both agents follow.

**Verify before continuing:**
- [ ] Terminal 1: search feature works in the browser
- [ ] Terminal 2: export feature works (downloads a CSV file)

---

## Step 5.4: Set Up GitHub MCP (Optional)

This step is optional but demonstrates team workflow (P10).

Copy the template into your main project directory:

```bash
cd /path/to/my-expense-tracker
cp ../claude-code-project-tutorial/templates/mcp.json .mcp.json
```

This gives Claude access to GitHub tools — creating PRs, commenting on issues, searching code.

**Why project-scoped:** This file gets committed to version control. Every contributor gets the same MCP config automatically. No "install this plugin" messages in Slack (P10).

---

## Step 5.5: Merge Both Features

Go back to main and merge:

```bash
cd /path/to/my-expense-tracker

# Merge feature/search
git merge feature/search

# Merge feature/export
git merge feature/export
```

**What to expect:**
- If your architecture is clean (independent components, no shared mutable state), these should merge without conflicts.
- If there ARE conflicts, they'll likely be in shared files like `App.jsx` (routing) or `main.py` (router registration). These are easy to resolve.

Resolve any conflicts, then verify both features work together:
```bash
cd backend && python -m uvicorn app.main:app --reload
# (new terminal)
cd frontend && npm run dev
```

Open the browser — search and export should both work.

**Clean up worktrees:**
```bash
git worktree remove ../my-app-search
git worktree remove ../my-app-export
git branch -d feature/search feature/export
```

**Commit + push:**
```bash
git add .mcp.json
git commit -m "feat: add GitHub MCP config for team workflow"
git add -A && git status  # review what's staged
git commit -m "feat: merge search and export features from parallel development"
git push
```

Wait — we just said never use `git add -A`. In this case, review `git status` first. The merge already combined the changes. Make sure nothing unexpected is in there.

---

## Step 5.6: Let Claude Update the Tracking Files

```
Review what we built in Phase 5. Then:
1. Add a Phase 5 entry to CHANGELOG.md — mention that two features were built in parallel, and whether the merge was clean
2. Check off completed items in REQUIREMENTS.md for Phase 5
3. Check BUGS.md — did the merge introduce any issues? Log them if so.
```

Review what Claude wrote.

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md BUGS.md
git commit -m "docs: update tracking files for Phase 5"
git push
```

---

## Phase 5 Complete

You now have:
- Two features built simultaneously by two Claude instances
- A clean merge with no (or minimal) conflicts
- A project-scoped `.mcp.json` that any team member can use
- Proof that clean architecture enables parallel work

**Why this phase matters:** This is the payoff for everything in Phases 0-1. Clean patterns + CLAUDE.md = multiple agents that stay aligned without talking to each other (P2). Messy foundations would have made this a conflict nightmare.

**Next:** [Phase 6 — Testing](phase-6-testing.md)
