# Phase 1: Foundation

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P1: The first few thousand lines determine everything
- P3: AI is a force multiplier in whatever direction you're going
- P8: Agent experience is a priority
- P9: Own your prompts, own your workflow

> **Start by running `/clear`** if you have a Claude session open from Phase 0.

---

## Step 1.1: Enter Plan Mode

Open Claude Code in your project directory:

```bash
claude
```

Before writing any code, enter **Plan Mode**:

**Press `Shift+Tab` twice.**

You should see the mode indicator change. In plan mode, Claude can analyze your project but cannot edit files — it's a read-only brainstorm.

Type this prompt:

```
I need to scaffold a FastAPI backend with SQLite and a React+Vite frontend.
Review my CLAUDE.md and propose:
1. The folder structure for both backend and frontend
2. Key files that need to be created
3. The order we should create them
Don't write any code yet — just the plan.
```

Claude will propose a structure. Review it:
- Does the backend structure match what CLAUDE.md describes?
- Are there unnecessary files or over-engineering?
- Does the frontend structure look clean?

Say "continue" if the plan looks good, or adjust it: "Move the database config into `database.py` not `db.py`" — whatever matches your preferences.

**Then exit plan mode: press `Shift+Tab` to cycle back to normal mode.**

**Verify before continuing:**
- [ ] You reviewed Claude's proposed structure
- [ ] You're back in normal mode (not plan mode)

---

## Step 1.2: Scaffold the Backend

Now Claude can write code. Give it a specific prompt:

```
Scaffold the FastAPI backend following the plan we just discussed.
Create:
- backend/app/main.py with a FastAPI app and a GET /api/health endpoint
- backend/app/database.py with SQLite connection setup using SQLAlchemy
- backend/requirements.txt with fastapi, uvicorn, sqlalchemy, pydantic, python-dotenv, alembic
Keep it minimal. No models or routers yet — just the skeleton.
```

Claude will create the files. **Review the diff carefully.**

This is the most important diff review in the entire tutorial. These patterns — import style, naming conventions, file organization — will be replicated across everything Claude generates from here on (P1).

Check:
- [ ] File names match CLAUDE.md conventions (snake_case)
- [ ] Import style is consistent
- [ ] The health endpoint returns a simple JSON response
- [ ] No unnecessary files were created (no `utils.py`, no `config.py` you didn't ask for)

If something's off, tell Claude: "Rename `db.py` to `database.py`" or "Remove the `utils.py` file, we don't need it."

**Commit:**
```bash
git add backend/
git commit -m "feat: scaffold FastAPI backend with health endpoint and SQLite setup"
```

**Verify before continuing:**
- [ ] `cd backend && pip install -r requirements.txt` installs without errors
- [ ] `python -m uvicorn app.main:app --reload` starts the server
- [ ] `http://localhost:8000/api/health` returns JSON
- [ ] `http://localhost:8000/docs` shows the Swagger UI

> **Stuck?** Compare with `reference/expense-tracker/backend/`
> **Off?** Fix the issue, update CLAUDE.md if a convention was wrong, then re-commit.

---

## Step 1.3: Scaffold the Frontend

Stop the backend server (`Ctrl+C`). Now scaffold the frontend:

```
Scaffold the React + Vite frontend.
Create:
- frontend/ with a standard Vite + React setup (use npm create vite)
- A clean App.jsx with react-router-dom routing
- Two placeholder pages: HomePage and a page for the main resource (e.g., ExpensesPage)
- A simple layout component with navigation between pages
Keep it minimal. No API calls yet.
```

Review the diff:
- [ ] Component files use PascalCase
- [ ] One component per file
- [ ] Routing is clean and follows React Router patterns
- [ ] No unnecessary dependencies were added

**Commit:**
```bash
git add frontend/
git commit -m "feat: scaffold React + Vite frontend with routing and placeholder pages"
```

**Verify before continuing:**
- [ ] `cd frontend && npm install` installs without errors
- [ ] `npm run dev` starts the dev server
- [ ] `http://localhost:5173` shows the app with navigation between pages

> **Stuck?** Compare with `reference/expense-tracker/frontend/`

---

## Step 1.4: Health Check — The 1-Shot Test

This is your first test of project health (P4).

Ask Claude to do something new that should follow the established pattern:

```
Add a GET /api/ping endpoint that returns {"message": "pong"}. Follow the same pattern as the health endpoint.
```

**What to look for:**
- Did Claude put it in the right file?
- Does it match the style of the health endpoint?
- Did it do this in one shot without confusion?

If yes → your foundation is solid. The patterns are working.
If no → something in the scaffold needs fixing before you build on top of it.

**Commit:**
```bash
git add backend/app/main.py
git commit -m "feat: add ping endpoint"
```

---

## Step 1.5: Build Your First Custom Skill

You've now scaffolded a backend endpoint twice (health + ping). You see the pattern. Let's capture it as a reusable skill (P9).

Create the skill directory and file:

```bash
mkdir -p .claude/skills/add-endpoint
```

Copy the skill from the tutorial repo:

```bash
cp ../claude-code-project-tutorial/skills/add-endpoint/SKILL.md .claude/skills/add-endpoint/SKILL.md
```

Or create it yourself — the point is to own it, not copy-paste blindly. The skill file at `skills/add-endpoint/SKILL.md` in the tutorial repo is a good starting point, but modify it to match YOUR project's patterns.

Test it:

```
/add-endpoint items
```

(Replace `items` with a resource name relevant to your project.)

Claude should scaffold a new endpoint that matches your existing patterns perfectly. If it doesn't, tweak the SKILL.md until it does.

**Commit:**
```bash
git add .claude/skills/add-endpoint/
git commit -m "feat: add /add-endpoint custom skill"
```

**Verify before continuing:**
- [ ] `/add-endpoint` creates files matching your project's conventions
- [ ] The skill uses `!`command`` to inject live project context

---

## Step 1.6: Let Claude Update the Tracking Files

Don't update these manually — prompt Claude to do it. This is part of the agentic workflow.

```
Review what we built in this phase. Then:
1. Add a Phase 1 entry to CHANGELOG.md summarizing what was created
2. Check off completed items in REQUIREMENTS.md for Phase 1
3. If anything in REQUIREMENTS.md doesn't match what we actually built, note the difference
```

**Review what Claude wrote:**
- [ ] CHANGELOG.md entry is accurate (not overstated, not missing anything)
- [ ] REQUIREMENTS.md has the right items checked off
- [ ] Claude didn't check off items that aren't actually done

This is the same review discipline as code — AI-generated documentation needs verification too.

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update changelog and requirements for Phase 1"
git push
```

---

## Phase 1 Complete

You now have:
- A working backend (FastAPI + SQLite) with Swagger docs
- A working frontend (React + Vite) with routing
- Your first custom skill (`/add-endpoint`)
- Clean patterns that Claude will replicate going forward

**Why this phase matters:** Every line Claude writes from here on will mimic what was established in these first files. If the patterns are clean, the whole project stays clean (P1, P3). If they're messy, it compounds.

**Next:** [Phase 2 — Backend Core](phase-2-backend.md)
