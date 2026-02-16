# Phase 0: Setup & Decisions

**Time:** ~5 minutes | **Tokens:** Near zero (mostly manual setup)

**Principles in action:**
- P1: The first few thousand lines determine everything
- P6: AI didn't speed up all steps equally — these decisions deserve time
- P7: Complex agent setups suck — keep it simple

---

## Step 0.1: Verify Prerequisites

Before anything else, confirm your tools are installed.

Run this in your terminal:

```bash
echo "Claude Code: $(claude --version 2>/dev/null || echo 'NOT FOUND')"
echo "Python:      $(python3 --version 2>/dev/null || echo 'NOT FOUND')"
echo "Node.js:     $(node --version 2>/dev/null || echo 'NOT FOUND')"
echo "Git:         $(git --version 2>/dev/null || echo 'NOT FOUND')"
echo "GitHub CLI:  $(gh --version 2>/dev/null | head -1 || echo 'NOT FOUND')"
echo "Docker:      $(docker --version 2>/dev/null || echo 'NOT FOUND')"
```

**Verify before continuing:**
- [ ] All 6 show version numbers (no `NOT FOUND`)
- [ ] You have a Claude Pro ($20/mo) or Max subscription
- [ ] You have a GitHub account and `gh auth login` is authenticated

> **Stuck?** See the [Prerequisites section](../README.md#prerequisites) for install instructions.

---

## Step 0.2: Choose Your Project

Pick one. All three follow the same phases — only the domain logic differs.

### Option A: Expense Tracker
Log expenses, assign categories, view monthly summaries, set budget limits.
- Budget math is deterministic — no LLM needed for calculations (P13)
- Date handling traps: Claude loves using `created_at` where you need `transaction_date` (P12)
- Financial data means security review is non-trivial (P11)

### Option B: Link Shortener
Create short URLs, track clicks per link, view analytics dashboard.
- URL validation is a real security concern — open redirects (P11)
- Click counting needs performance thinking — caching? (P6)
- Dashboard + API are naturally independent = good parallel work (P2)

### Option C: Task Board
Kanban-style board, create/move/assign tasks, filter by status.
- Status transitions are clear business logic — deterministic (P13)
- Drag-and-drop adds frontend complexity
- Permissions and assignments bring security into play (P11)

### Option D: Your Own Idea
Any CRUD app with a frontend and backend works. Just make sure it has:
- At least two related resources (e.g., expenses + categories)
- Calculations or logic that should NOT be done with an LLM
- A reason to care about input validation

**Verify before continuing:**
- [ ] You've chosen a project

---

## Step 0.3: The Tech Stack Conversation

**Don't skip this.** This is one of the most important steps in the entire tutorial.

AI didn't speed up all steps equally (P6). Feature implementation? Way faster. But choosing your tech stack, designing your schema, defining your conventions — these decisions deserve *more* time now, not less. Every choice here gets amplified across the entire codebase. Pick the wrong framework and Claude will happily build 10,000 lines of code on a shaky foundation.

Here's our stack and why. Skim the headlines — read the details for anything you'd challenge.

| Layer | Choice | Key reason |
|-------|--------|------------|
| Backend | **FastAPI** (Python) | Auto-generated Swagger docs = verify endpoints in 5 seconds |
| Frontend | **React + Vite** | Largest training data = best 1-shot results (P4) |
| Database | **SQLite** → Postgres | Zero infrastructure for dev; upgrade in Phase 9 |
| Container | **Docker** (last) | Don't containerize what doesn't exist yet |

### Why two languages?

Python backend + JavaScript frontend is deliberate:
- Shows Claude works equally well across languages
- Forces clean FE/BE separation — no temptation to blur boundaries
- Matches how production teams actually work

Want to use Express + React instead? The workflow is the same.

### Backend: Why FastAPI?

| vs. Flask | vs. Django | vs. Express |
|-----------|-----------|-------------|
| Built-in Swagger UI — test endpoints instantly, no Postman needed | Django has too much magic (admin, ORM, auth). We want to see what Claude generates. | Would make the whole project JS — misses the two-language point |

**Why type hints matter for AI coding:** When Claude sees `amount: float` in a Pydantic model, it generates consistent, typed code. Without types, Claude guesses — and guesses differently each time (P12).

### Frontend: Why React + Vite?

- **React over Vue/Svelte** — larger community, more training data, more consistent Claude output
- **Vite over Next.js** — Next.js blurs FE/BE boundaries (API routes, SSR). Vite keeps it purely client-side, which makes the architecture obvious
- **Vite over CRA** — CRA is deprecated

### Database and Docker

**SQLite now:** Zero infrastructure. One file. No connection strings to debug. Upgrade to Postgres when we containerize in Phase 9.

**Docker last:** Develop locally first. Docker adds debugging complexity (build caches, networking, file permissions) that's noise during learning phases.

### The bigger point

You can swap any of these choices. The workflow — CLAUDE.md, permissions, skills, plan mode, diff review, parallel work — works with any stack. Your next project might use Go + HTMX + Postgres. The process is the same.

**Verify before continuing:**
- [ ] You understand why these choices were made (skim the table, read what caught your eye)
- [ ] You're ready to put this reasoning in your CLAUDE.md

---

## Step 0.4: Create Your Project Repo

```bash
# Create directory
mkdir my-expense-tracker   # or my-link-shortener, my-task-board
cd my-expense-tracker

# Initialize git
git init

# Create GitHub repo and link it
gh repo create my-expense-tracker --public --source . --push
```

Replace `my-expense-tracker` with your project name.

**Verify before continuing:**
- [ ] You're inside your new project directory
- [ ] `git status` shows a clean repo
- [ ] The repo exists on GitHub

---

## Step 0.5: Copy Starter Templates

From the tutorial repo, copy the templates into your project:

```bash
# Assuming the tutorial repo is cloned nearby
cp ../claude-code-project-tutorial/templates/CLAUDE.md.starter ./CLAUDE.md
cp ../claude-code-project-tutorial/templates/gitignore ./.gitignore
cp ../claude-code-project-tutorial/templates/CHANGELOG.md ./CHANGELOG.md
cp ../claude-code-project-tutorial/templates/REQUIREMENTS.md ./REQUIREMENTS.md
cp ../claude-code-project-tutorial/templates/BUGS.md ./BUGS.md
```

Now **edit CLAUDE.md** — replace the placeholders:
- `[YOUR PROJECT NAME]` → your actual project name
- `[One sentence: what this app does]` → one sentence description
- Review the conventions and adjust if you have preferences

This is your file. Make it yours (P9).

### Why these files matter for agentic programming

These aren't just files for you to read. They're files Claude reads too.

**CLAUDE.md** is the obvious one — Claude reads it at the start of every session. But the tracking files serve a dual purpose:

- **REQUIREMENTS.md** tells Claude what's been built and what's left. When you prompt "update REQUIREMENTS.md to reflect what we just built," Claude reviews its own work and checks off completed items. You review what it checked off. This is the agentic workflow: *prompt → agent acts → you verify*.
- **BUGS.md** gives Claude context about known issues. When you prompt "check if any open bugs in BUGS.md are affected by this change," Claude reads the file and cross-references.
- **CHANGELOG.md** trains Claude on what happened in each phase. When you `/clear` and start a new session, Claude reads CHANGELOG.md alongside CLAUDE.md to understand project history.

The pattern throughout this tutorial: **don't manually update tracking files — prompt Claude to update them, then review what it writes.** This keeps the agent in the loop and gives you practice reviewing AI-generated documentation, not just AI-generated code.

**Verify before continuing:**
- [ ] CLAUDE.md exists with your project name and description
- [ ] .gitignore, CHANGELOG.md, REQUIREMENTS.md, BUGS.md all exist
- [ ] You've read through CLAUDE.md and it makes sense

---

## Step 0.6: Set Up Claude Code Permissions

Create the settings directory and copy the permissions file:

```bash
mkdir -p .claude
cp ../claude-code-project-tutorial/templates/settings.json .claude/settings.json
```

This does two things:
1. **Allows** common dev commands (pytest, npm run, git operations) so you don't click "approve" 50 times
2. **Denies** destructive commands (rm -rf, force push) and reading `.env` files — security from minute one

**Verify before continuing:**
- [ ] `.claude/settings.json` exists
- [ ] You've reviewed the allow/deny lists and they make sense

---

## Step 0.7: Set Up Environment Config

Create a `.env.example` with default values for development:

```bash
# Create .env.example with default dev values
cat > .env.example << 'EOF'
DATABASE_URL=sqlite:///./app.db
CORS_ORIGINS=http://localhost:5173
EOF

# Create your local .env from the example
cp .env.example .env
```

The `.gitignore` you copied in Step 0.5 already excludes `.env` files (but not `.env.example`).

Your app will load config from `.env` using `python-dotenv` (added to requirements in Phase 1). This ties directly to the permission deny rules from Step 0.6: Claude can't read `.env`, and now there's a reason — it contains environment-specific config that shouldn't be in the codebase.

**Verify before continuing:**
- [ ] `.env.example` exists with default values
- [ ] `.env` exists (copied from the example)
- [ ] `git status` does NOT show `.env` as an untracked file (gitignore is working)

---

## Step 0.8: Run `/init` and Compare

Now let's see what Claude Code generates on its own:

```bash
claude
```

Once Claude starts, type:

```
/init
```

Claude will analyze your project and suggest additions to CLAUDE.md. **Don't auto-accept.** Compare what Claude suggests with what you already have. You'll notice:
- Claude might add useful details you missed
- Claude might also add things that are wrong or too verbose
- The point: AI gives you a draft, you make it yours (P9)

Merge anything useful into your CLAUDE.md. Discard the rest. Then exit Claude (`Ctrl+C` or type `exit`).

**Verify before continuing:**
- [ ] You ran `/init` and reviewed the suggestions
- [ ] CLAUDE.md reflects your decisions, not just Claude's defaults

---

## Step 0.9: First Commit

```bash
git add CLAUDE.md CHANGELOG.md REQUIREMENTS.md BUGS.md .claude/settings.json .env.example .gitignore
git commit -m "initial commit: project setup with CLAUDE.md, tracking files, and permissions"
git push -u origin main
```

**Verify before continuing:**
- [ ] `git log` shows your first commit
- [ ] `git status` is clean
- [ ] The commit is visible on GitHub

---

## Phase 0 Complete

You now have:
- A project repo on GitHub with clean tracking files
- A CLAUDE.md tailored to your project
- Permission settings that reduce friction and prevent accidents
- Environment config with `.env.example` committed and `.env` gitignored
- A REQUIREMENTS.md with all features mapped to phases

**Total tokens used:** Nearly zero. The only Claude interaction was `/init`.

**Next:** [Phase 1 — Foundation](phase-1-foundation.md)
