# Build a Full-Stack App with Claude Code CLI

**From first commit to running in Docker — a hands-on guide for engineers.**

> Most Claude Code tutorials show you prompts. This one builds a real project.

You'll build a complete full-stack application (Python backend + React frontend) using Claude Code CLI, learning the workflow patterns that separate productive AI-assisted development from chaotic prompt-and-pray.

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
Phase 0  Setup & Decisions       ~5 min     Tech stack reasoning, CLAUDE.md, project tracking
Phase 1  Foundation              ~10 min    Scaffold both apps, establish patterns that scale
Phase 2  Backend Core            ~10 min    Models, API endpoints, diff review discipline
Phase 3  Frontend Core           ~10 min    Pages, components, the "continue" workflow
Phase 4  Integration             ~10 min    Connect FE↔BE, catch your first planted bug
Phase 5  Parallel Work           ~10 min    Two Claude instances, two branches, zero conflicts
Phase 6  Hardening               ~5 min     Tests, security audit, spot the subtle bug
Phase 7  Docker & Ship           ~5 min     Containerize, launch, retrospective
                                 ─────
                                 ~65 min total
```

By the end, you'll have:
- A working app running in Docker
- A git history that tells the story of a clean build
- A CLAUDE.md that evolved with your project
- Custom Claude Code skills you built from actual need
- Experience running parallel agents without chaos

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
| 9 | Own your prompts, own your workflow | Custom skills in Phase 1, 5 |
| 10 | Process alignment is critical in teams | Phase 5 parallel work |
| 11 | AI code is not optimized by default | Phase 2, 4, 6 reviews |
| 12 | Check git diff for critical logic | Phase 2, 4, 6 diff exercises |
| 13 | You don't need an LLM to calculate 1+1 | Phase 6 hardening |

---

## Prerequisites

Before you start, make sure you have:

- [ ] **Claude Code CLI** installed and authenticated ([install guide](https://docs.anthropic.com/en/docs/claude-code/overview))
- [ ] **Claude Pro subscription** ($20/mo) — this tutorial fits in one token session
- [ ] **Python 3.11+** ([python.org](https://www.python.org/downloads/))
- [ ] **Node.js 18+** ([nodejs.org](https://nodejs.org/))
- [ ] **Git** configured with a GitHub account
- [ ] **Docker Desktop** installed ([download here](https://www.docker.com/products/docker-desktop/)) — used in Phase 7

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
docs/phase-7-docker.md       →    You run: docker compose up
```

### Every step follows this rhythm:

```
 1. Read the context         Why this step matters, which principle it demonstrates
 2. Prompt Claude            Exact text to use in Claude Code CLI
 3. Review the diff          What to look for — the guide tells you specifically
 4. "Continue" or correct    Iterate with Claude until it's right
 5. Commit + push            Named files, meaningful message, push at phase end
 6. Update tracking files    REQUIREMENTS.md, CHANGELOG.md, BUGS.md as needed
```

### Token budget strategy

This tutorial is designed for a single $20 Pro session:
- Run `/clear` between phases — your CLAUDE.md provides continuity
- Use specific prompts, not vague ones
- If you're retrying more than twice, the prompt needs work, not more tokens
- Read errors yourself before asking Claude to debug

---

## Tech Stack

**Why these choices?** Because choosing the right tools deserves more thought than implementing features with them (Principle 6). Full reasoning in [Phase 0](docs/phase-0-setup.md).

| Layer | Choice | Why |
|-------|--------|-----|
| **Backend** | Python + FastAPI | Type hints + Pydantic = predictable AI output. Auto-generated `/docs` for instant verification. |
| **Frontend** | React + Vite | Largest community, most Claude training data. Vite over Next.js — we don't need SSR, and clear FE/BE separation matters for learning. |
| **Database** | SQLite (dev) → Postgres (Docker) | Zero setup for development. Postgres upgrade happens naturally in Phase 7. |
| **Container** | Docker Compose | One command to run everything. Added last, not first — matches real workflow. |

Both Python and JavaScript in one project. You'll see Claude Code work across both.

---

## Repo Structure

```
claude-code-project-tutorial/
│
├── README.md                          ← You are here
├── PLAN.md                            ← Full project plan and decisions
│
├── docs/                              ← Phase-by-phase guide
│   ├── phase-0-setup.md
│   ├── phase-1-foundation.md
│   ├── phase-2-backend.md
│   ├── phase-3-frontend.md
│   ├── phase-4-integration.md
│   ├── phase-5-parallel.md
│   ├── phase-6-hardening.md
│   ├── phase-7-docker.md
│   └── appendix-what-goes-wrong.md    ← What happens when you break the rules
│
├── templates/                         ← Copy these into your project at Phase 0
│   ├── CLAUDE.md.starter
│   ├── CHANGELOG.md
│   ├── REQUIREMENTS.md
│   └── BUGS.md
│
└── reference/                         ← Completed projects to compare against
    ├── expense-tracker/
    ├── link-shortener/
    └── task-board/
```

Reference implementations are also available on the `reference-history` branch with tagged commits matching each phase (`v0-setup` through `v7-docker`).

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

Follow each phase in order. Commit often. Push at the end of each phase. By Phase 7 you'll have a working app in Docker.

---

## What's Next (After the Tutorial)

Once you've completed all 7 phases, you have a working app and a proven workflow. Natural next steps — all using the same process you just learned:

- **Add authentication** — JWT tokens, login/signup, protected routes
- **Switch SQLite to Postgres** — update docker-compose, write migrations
- **Add CI/CD** — GitHub Actions for tests, linting, Docker builds
- **Deploy** — Railway, Fly.io, or a VPS
- **Add real-time features** — WebSockets for live updates
- **Scale parallel work** — more branches, more agents, bigger features

---

## Credits

Inspired by the workflow principles from [claude-code-workflow-tips](https://github.com/williambergmann/claude-code-workflow-tips) and the Claude Code community.

---

## License

MIT
