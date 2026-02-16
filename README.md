# Build a Full-Stack App with Claude Code

**From first commit to running in Docker — a hands-on guide for engineers.**

> Most Claude Code tutorials show you prompts. This one builds a real project.

You'll build a complete full-stack application (Python backend + React frontend) using Claude Code, learning the workflow patterns that separate productive AI-assisted development from chaotic prompt-and-pray.

---

## What You'll Build

Pick one of three projects — same tech stack, same workflow, different domain:

| | Expense Tracker | Link Shortener | Task Board |
|---|---|---|---|
| **What** | Log expenses, categorize, monthly summaries | Create short URLs, track clicks, analytics | Kanban board, create/move/assign tasks |
| **Backend** | FastAPI + SQLite | FastAPI + SQLite | FastAPI + SQLite |
| **Frontend** | React + Vite | React + Vite | React + Vite |
| **Docker** | docker-compose up | docker-compose up | docker-compose up |

Or bring your own idea — any CRUD app with a frontend and backend works.

---

## What You'll Learn

This isn't about learning to code. It's about learning to **work with an AI coding agent effectively**.

```
Phase 0  Setup & Decisions       ~5 min     /init, permissions, CLAUDE.md, .env config
Phase 1  Foundation              ~10 min    Plan mode, scaffold both apps, first skill
Phase 2  Backend Core            ~10 min    Models, Alembic migrations, API, diff review
Phase 3  Frontend Core           ~10 min    Components, second skill, "continue" workflow
Phase 4  Integration             ~10 min    Connect FE↔BE, catch the planted bug
Phase 5  Parallel Work           ~10 min    Git worktrees, GitHub MCP, two agents
Phase 6  Test Coverage & Gaps     ~10 min    Coverage analysis, fill gaps, edge cases
Phase 7  Hardening               ~5 min     Security audit, /review-diff skill, lint hook
Phase 8  CI/CD                   ~5 min     GitHub Actions workflow, branch protection
Phase 9  Docker & Ship           ~5 min     Containerize, launch, deploy (optional), retrospective
                                 ─────
                                 ~75 min total
```

By the end, you'll have:
- A working app running in Docker
- A git history that tells the story of a clean build
- A CLAUDE.md that evolved with your project
- 3 custom skills (`/add-endpoint`, `/add-component`, `/review-diff`) built from actual need
- Permission settings and a lint hook that make Claude safer and faster
- A project-scoped MCP config for GitHub integration
- Experience running parallel agents on git worktrees without chaos

---

## The 13 Principles Behind This Guide

Every phase maps back to these. They're not theory — they're the difference between shipping fast and drowning in tech debt.

| # | Principle | Where You'll See It |
|---|-----------|-------------------|
| 1 | The first few thousand lines determine everything | Phase 0-1 |
| 2 | Parallel agents, zero chaos | Phase 5 |
| 3 | AI is a force multiplier in whatever direction you're going | Phase 3 |
| 4 | The 1-shot prompt test | Phase 1, 3, 4 health checks |
| 5 | Technical vs non-technical AI coding | This whole guide (it's for engineers) |
| 6 | AI didn't speed up all steps equally | Phase 0 tech stack decisions |
| 7 | Complex agent setups suck | One CLAUDE.md, not a pile of .md files |
| 8 | Agent experience is a priority | CLAUDE.md evolves every phase |
| 9 | Own your prompts, own your workflow | Custom skills in Phase 1, 3, 7 |
| 10 | Process alignment is critical in teams | Phase 5 parallel work, Phase 8 CI |
| 11 | AI code is not optimized by default | Phase 2, 4, 6, 7 reviews |
| 12 | Check git diff for critical logic | Phase 2, 4, 7 diff exercises |
| 13 | You don't need an LLM to calculate 1+1 | Phase 7 hardening |

---

## Prerequisites

You need 7 things installed. We'll help you check each one.

### 1. Claude Code + Claude Pro subscription

You need an active **Claude Pro** ($20/mo) or **Max** subscription. The tutorial fits in one token session.

**Claude Code runs in:**
- **Terminal (CLI)** — the primary interface. Install globally via npm.
- **VS Code** — install the [Claude Code extension](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code). Works in any VS Code-compatible editor (VS Code, Cursor, Windsurf, etc.)
- **JetBrains IDEs** — install the [Claude Code plugin](https://plugins.jetbrains.com/plugin/claude-code) for IntelliJ, WebStorm, PyCharm, etc.

```bash
# Install Claude Code CLI
npm install -g @anthropic-ai/claude-code

# Verify it works (this will prompt you to authenticate if needed)
claude --version
```

If you don't have npm yet, install Node.js first (step 4 below).

[Full install guide](https://docs.anthropic.com/en/docs/claude-code/overview)

> **What about other AI coding tools?** Tools like [Antigravity](https://antigravity.dev) and [Cursor](https://cursor.com) can use Claude models (including Opus) through their own integrations. The 13 principles in this tutorial apply to any AI coding tool. However, the specific workflow features we teach — custom skills, hooks, MCP servers, permission settings, plan mode — are Claude Code features. If you're using a different tool, adapt those sections to your tool's equivalents.

### 2. Python 3.11+

```bash
# Check if you have it
python3 --version

# macOS (with Homebrew)
brew install python@3.11

# Windows
# Download from https://www.python.org/downloads/
# Check "Add to PATH" during installation

# Linux (Ubuntu/Debian)
sudo apt update && sudo apt install python3.11 python3.11-venv
```

### 3. Node.js 18+

```bash
# Check if you have it
node --version

# macOS (with Homebrew)
brew install node

# Windows / Linux — use the official installer or nvm
# https://nodejs.org/
# Or with nvm: nvm install 18
```

### 4. Git + GitHub account

```bash
# Check if you have it
git --version

# macOS
brew install git

# Configure (if not already done)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

You also need a [GitHub account](https://github.com/signup) and the GitHub CLI:

```bash
# Install GitHub CLI
brew install gh          # macOS
# Or: https://cli.github.com/ for other platforms

# Authenticate
gh auth login
```

### 5. Docker Desktop

Used in Phase 9 to containerize your app. Install now so it's ready when you need it.

[Download Docker Desktop](https://www.docker.com/products/docker-desktop/)

```bash
# Verify after installation
docker --version
docker compose version
```

### Quick check — run this to verify everything:

```bash
echo "Claude Code: $(claude --version 2>/dev/null || echo 'NOT FOUND')"
echo "Python:      $(python3 --version 2>/dev/null || echo 'NOT FOUND')"
echo "Node.js:     $(node --version 2>/dev/null || echo 'NOT FOUND')"
echo "Git:         $(git --version 2>/dev/null || echo 'NOT FOUND')"
echo "GitHub CLI:  $(gh --version 2>/dev/null | head -1 || echo 'NOT FOUND')"
echo "Docker:      $(docker --version 2>/dev/null || echo 'NOT FOUND')"
```

All 6 should show version numbers. If any say `NOT FOUND`, install it using the instructions above.

---

## How This Guide Works

### You follow the guide. You build the project.

This repo contains the tutorial. Your project lives in its own repo that you create, commit to, and push to GitHub as you go.

```
This repo (the guide)              Your repo (the project)
─────────────────────              ──────────────────────
docs/phase-0-setup.md        →    You create: my-expense-tracker/
docs/phase-1-foundation.md   →    You scaffold backend + frontend
docs/phase-2-backend.md      →    You build models + API
...                                ...
docs/phase-9-docker.md       →    You run: docker compose up
```

### One step at a time — no walls of text

Every step fits on one screen and ends with a checkpoint:

```
 1. Read the context         Why this step matters, which principle it demonstrates
 2. Prompt Claude            Exact text to use in Claude Code CLI
 3. Review the diff          What to look for — the guide tells you specifically
 4. "Continue" or correct    Iterate with Claude until it's right
 5. Verify                   Checklist to confirm it works before moving on
 6. Commit                   Named files, meaningful message
```

At every checkpoint you have three options:

```
 Ready?   → Move to the next step
 Stuck?   → Read the troubleshooting tip for that step
 Off?     → Update CLAUDE.md or fix the issue before continuing
```

You never have to scroll back to figure out where you are.

### Token budget strategy

This tutorial is designed for a single $20 Pro session:
- Run `/clear` between phases — your CLAUDE.md provides continuity
- Use specific prompts, not vague ones
- If you're retrying more than twice, the prompt needs work, not more tokens
- Read errors yourself before asking Claude to debug

---

## Tech Stack

AI made feature implementation faster — but it didn't speed up tech stack decisions (Principle 6). In fact, these decisions matter *more* now because every choice gets amplified across thousands of generated lines. Phase 0 includes a full [tech stack conversation](docs/phase-0-setup.md#step-03-the-tech-stack-conversation) walking through why each choice was made and what alternatives we rejected.

| Layer | Choice | Why (short version) |
|-------|--------|-----|
| **Backend** | Python + FastAPI | Type hints + Pydantic = predictable AI output. Auto-generated `/docs` for instant verification. |
| **Frontend** | React + Vite | Largest community, most Claude training data. Vite over Next.js — clear FE/BE separation matters. |
| **Database** | SQLite (dev) → Postgres (Docker) | Zero setup for development. Postgres upgrade happens naturally in Phase 9. |
| **Container** | Docker Compose | One command to run everything. Added last, not first — matches real workflow. |

Two languages in one project (Python + JavaScript). You'll see Claude Code switch between both without missing a beat. The workflow — CLAUDE.md, skills, permissions, parallel work — works with any stack.

---

## Claude Code Features You'll Use

Beyond generating code, you'll learn the workflow features that make Claude Code predictable and efficient:

| Feature | Phase | What You'll Do |
|---------|-------|---------------|
| **`/init`** | 0 | Generate a baseline CLAUDE.md, then refine it yourself |
| **Permission settings** | 0 | Allow dev commands, deny destructive ones and `.env` reads |
| **Plan Mode** | 1 | Shift+Tab to analyze before coding — read-only brainstorm |
| **Custom skills** | 1, 3, 7 | Build 3 reusable slash commands from observed patterns |
| **Git worktrees** | 5 | Full filesystem isolation for parallel Claude instances |
| **GitHub MCP** | 5 | Project-scoped `.mcp.json` — team gets it automatically |
| **Lint hook** | 7 | Auto-run linter after every Claude edit — self-correcting |
| **`/review-diff`** | 7 | Pre-commit review skill that injects live `git diff` |

Each feature is introduced when you need it. No front-loaded setup.

---

## Repo Structure

```
claude-code-project-tutorial/
│
├── README.md                          ← You are here
├── PLAN.md                            ← Full project plan and decisions
├── LICENSE                            ← MIT
│
├── docs/                              ← Phase-by-phase guide
│   ├── phase-0-setup.md
│   ├── phase-1-foundation.md
│   ├── phase-2-backend.md
│   ├── phase-3-frontend.md
│   ├── phase-4-integration.md
│   ├── phase-5-parallel.md
│   ├── phase-6-testing.md
│   ├── phase-7-hardening.md
│   ├── phase-8-cicd.md
│   ├── phase-9-docker.md
│   └── appendix-what-goes-wrong.md    ← What happens when you break the rules
│
├── templates/                         ← Copy these into your project at Phase 0
│   ├── CLAUDE.md.starter
│   ├── settings.json                  ← Permission rules for .claude/settings.json
│   ├── gitignore                      ← Starter .gitignore (Python + Node + .env)
│   ├── mcp.json                       ← GitHub MCP config (used in Phase 5)
│   ├── CHANGELOG.md
│   ├── REQUIREMENTS.md
│   └── BUGS.md
│
└── skills/                            ← Custom skills built during the tutorial
    ├── add-endpoint/SKILL.md          ← Phase 1: scaffold API endpoints
    ├── add-component/SKILL.md         ← Phase 3: scaffold React components
    └── review-diff/SKILL.md           ← Phase 7: pre-commit diff review
```

There are no pre-built reference implementations. You build the project yourself — that's the point. Each "Stuck?" hint provides specific troubleshooting advice for common issues at that step.

---

## Get Started

**1. Clone this guide**
```bash
git clone https://github.com/williambergmann/claude-code-project-tutorial.git
cd claude-code-project-tutorial
```

**2. Open Phase 0**

Start with [docs/phase-0-setup.md](docs/phase-0-setup.md) — it walks you through choosing a project, creating your repo, and setting up your CLAUDE.md.

**3. Build**

Follow each phase in order. Commit often. Push at the end of each phase. By Phase 9 you'll have a working app in Docker with tests and CI.

---

## What's Next (After the Tutorial)

Once you've completed all 10 phases, you have a working app with tests, CI/CD, and Docker — plus a proven workflow. Natural next steps:

**Project extensions:**
- **Add authentication** — JWT tokens, login/signup, protected routes
- **Switch SQLite to Postgres** — update docker-compose, production migrations
- **Deploy to production** — if you skipped the optional deploy step in Phase 9, try Railway, Fly.io, Render, or a VPS
- **Add real-time features** — WebSockets for live updates

**Advanced Claude Code features:**
- **Custom MCP servers** — build one tailored to your project's API or database
- **Agent teams** — coordinated multi-agent workflows for large codebases
- **Headless/CI mode** — run Claude Code headless in GitHub Actions for automated PR review
- **CLAUDE.md imports** — `@path/to/file` for modular instructions in large projects
- **Custom subagents** — specialized agents for domain-specific tasks

---

## Credits

Inspired by the workflow principles from [claude-code-workflow-tips](https://github.com/williambergmann/claude-code-workflow-tips) and the Claude Code community.

---

## License

MIT
