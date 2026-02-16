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

AI didn't speed up all steps equally (P6). Feature implementation? Way faster. But choosing your tech stack, designing your schema, defining your conventions — these decisions deserve *more* time now, not less. Because every choice you make here gets amplified across the entire codebase. Pick the wrong framework and Claude will happily build 10,000 lines of code on a shaky foundation.

This is a conversation, not a declaration. We picked a specific stack for this tutorial, but the *reasoning process* is what matters. When you start your next project, you'll make different choices — the point is to think through them deliberately.

### Why not just use one language for everything?

You could build the whole thing in JavaScript (Express + React). It would work. But we deliberately chose **Python for the backend and JavaScript for the frontend** because:

- It demonstrates that Claude Code works equally well across languages — you'll see it switch between Python and JS without breaking stride
- It forces a clean separation between frontend and backend — no temptation to blur the boundary
- It's closer to how production teams actually work (different languages for different layers)

If you're building your own project after this tutorial and want to use Express + React, go for it. The workflow is the same.

### Backend: Why FastAPI over Flask, Django, or Express?

**FastAPI vs Flask:**
FastAPI auto-generates interactive API docs at `/docs` (Swagger UI). This means you can test every endpoint instantly without Postman, curl, or writing a frontend. When Claude creates an endpoint, you verify it in 5 seconds. Flask can do this with extensions, but FastAPI has it built in.

**FastAPI vs Django:**
Django is powerful but opinionated — it comes with an admin panel, ORM, auth system, and more. For this tutorial, that's too much magic. We want to see exactly what Claude generates and understand every piece. FastAPI is minimal — you add only what you need.

**FastAPI vs Express:**
If we used Express, the entire project would be JavaScript. That misses the point (see above). Also, FastAPI's type hints + Pydantic models make Claude's output more predictable and reviewable — you can see the exact shape of every request and response.

**Why Python's type hints matter for AI coding:**
When Claude sees `amount: float` in a Pydantic model, it generates consistent, typed code. Without type hints, Claude guesses — and guesses differently each time. Typed code is more reviewable, which means you catch bugs faster (P12).

### Frontend: Why React + Vite over Next.js, Vue, or Svelte?

**React over Vue/Svelte:**
React has the largest community and the most training data in Claude's knowledge. That translates to better, more consistent code generation. Vue and Svelte are great frameworks — Claude handles them well too — but React gives us the highest chance of clean 1-shot results (P4).

**Vite over Next.js:**
Next.js blurs the line between frontend and backend (API routes, server components, SSR). That's powerful in production but confusing when you're trying to learn a clean FE/BE workflow. Vite keeps the frontend purely as a client-side app, which makes the architecture obvious and the parallel work demo (Phase 5) much cleaner.

**Vite over Create React App:**
CRA is deprecated. Don't use it.

### Database: Why SQLite now, Postgres later?

**SQLite for development:**
Zero infrastructure. One file. No Docker, no database server, no connection strings to debug. You run the app and it just works. This matters because debugging database connection issues during the learning phases is a waste of time and tokens.

**Postgres for production (Phase 9):**
When we containerize in Phase 9, we'll discuss upgrading to Postgres via Docker Compose. That's the natural point to make that decision — not now.

### Docker: Why added last?

Develop locally first. Containerize last. This matches how real projects work — you don't start by writing Dockerfiles for an app that doesn't exist yet. Docker adds a layer of debugging complexity (build caches, networking, file permissions) that's noise during the learning phases.

### The bigger point

You can swap any of these choices. The workflow — CLAUDE.md, permissions, skills, plan mode, diff review, parallel work — works with any stack. We chose this one because it gives us the clearest path to demonstrating the 13 principles. Your next project might use Go + HTMX + Postgres. The process is the same.

**Verify before continuing:**
- [ ] You understand why these choices were made
- [ ] You could explain the reasoning to a teammate (not just "the tutorial said so")
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
cp ../claude-code-project-tutorial/templates/CHANGELOG.md ./CHANGELOG.md
cp ../claude-code-project-tutorial/templates/REQUIREMENTS.md ./REQUIREMENTS.md
cp ../claude-code-project-tutorial/templates/BUGS.md ./BUGS.md
```

Now **edit CLAUDE.md** — replace the placeholders:
- `[YOUR PROJECT NAME]` → your actual project name
- `[One sentence: what this app does]` → one sentence description
- Review the conventions and adjust if you have preferences

This is your file. Make it yours (P9).

**Verify before continuing:**
- [ ] CLAUDE.md exists with your project name and description
- [ ] CHANGELOG.md, REQUIREMENTS.md, BUGS.md all exist
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
# Create .env.example
cat > .env.example << 'EOF'
DATABASE_URL=sqlite:///./app.db
CORS_ORIGINS=http://localhost:5173
EOF

# Create your local .env from the example
cp .env.example .env

# Make sure .env is gitignored
echo ".env" >> .gitignore
```

Your app will load config from `.env` using `python-dotenv` (we'll add this dependency in Phase 1). This ties directly to the permission deny rules from Step 0.6: Claude can't read `.env`, and now there's a reason — it contains environment-specific config that shouldn't be in the codebase.

**Verify before continuing:**
- [ ] `.env.example` exists with default values
- [ ] `.env` exists (copied from the example)
- [ ] `.gitignore` includes `.env`

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
