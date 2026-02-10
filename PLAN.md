# PLAN.md — claude-code-project-tutorial

## What This Is

A hands-on tutorial that walks technical users through building a real full-stack application using Claude Code CLI. Unlike prompt-only guides, every step produces actual code, actual commits, and a working app running in Docker by the end.

Inspired by [claude-code-workflow-tips](https://github.com/williambergmann/claude-code-workflow-tips) — but instead of teaching concepts in isolation, this guide applies them while building something real.

---

## Target Audience

Software engineers who have built projects before. They understand architecture, system design, and git workflows. They want to learn how to use Claude Code CLI effectively — not how to code.

Non-technical users need a fundamentally different guide with more guardrails. That's out of scope here.

---

## 13 Guiding Principles

These principles shape every decision in the tutorial. Each phase maps back to specific principles.

1. **The first few thousand lines determine everything** — Obsess over early patterns. The agent replicates them across 100k+ lines.
2. **Parallel agents, zero chaos** — Only possible when principle 1 is nailed.
3. **AI is a force multiplier in whatever direction you're already going** — Clean code gets cleaner. Messy code gets messier faster.
4. **The 1-shot prompt test** — If you can't do something in 1 shot, either the code is messy, you don't understand the system, or the problem needs breaking down.
5. **Technical vs non-technical AI coding** — Engineers know what to watch for. This tutorial is for engineers.
6. **AI didn't speed up all steps equally** — Framework/schema/dependency decisions deserve more time, not less.
7. **Complex agent setups suck** — Simplicity wins. One CLAUDE.md, not a pile of role-playing .md files.
8. **Agent experience is a priority** — Monitor and optimize how the agent uses your codebase.
9. **Own your prompts, own your workflow** — Don't copy-paste black boxes. Modify based on what you observe.
10. **Process alignment becomes critical in teams** — Shared process, shared updates.
11. **AI code is not optimized by default** — You must explicitly ask for security, performance, scalability — then verify.
12. **Check git diff for critical logic** — The agent might use `created_at` as fallback for `birth_date`. You won't catch that from just testing.
13. **You don't need an LLM call to calculate 1+1** — Use deterministic functions when they're the right tool.

---

## Deliverables

### Repository Structure

```
claude-code-project-tutorial/
├── PLAN.md                       # This file — the overall vision
├── README.md                     # Overview, prerequisites, how to use this guide
├── docs/
│   ├── phase-0-setup.md          # Prerequisites, project choice, tech stack reasoning
│   ├── phase-1-foundation.md     # CLAUDE.md, scaffolding, first patterns
│   ├── phase-2-backend.md        # Models, API, validation
│   ├── phase-3-frontend.md       # Pages, components, API client
│   ├── phase-4-integration.md    # Connect FE↔BE, CORS, end-to-end
│   ├── phase-5-parallel.md       # Two Claude instances, branches, merge
│   ├── phase-6-hardening.md      # Tests, security audit, diff review exercise
│   └── phase-7-docker.md         # Containerize, docker-compose up, done
├── templates/                    # Starter files the user copies into their project
│   ├── CLAUDE.md.starter         # Minimal Phase 0 version
│   ├── CHANGELOG.md              # Format example + a couple entries
│   ├── REQUIREMENTS.md           # Pre-populated with all features by phase
│   └── BUGS.md                   # Empty with format shown
└── reference/                    # Completed implementations (one per project choice)
    ├── expense-tracker/
    ├── link-shortener/
    └── task-board/
```

The user creates their **own separate repo** and builds into it following the guide.

### Reference Implementations

Each project choice has a completed implementation in `reference/`. These include the full git history with tagged commits matching each phase so users can compare with `git log`.

Tags follow the pattern: `v0-setup`, `v1-foundation`, `v2-backend`, etc.

---

## The 3 Project Choices

Users pick one at the start of Phase 0. All three use the same tech stack and follow the same phases — only the domain logic differs. Users can also bring their own project idea if it fits the pattern (CRUD app with frontend + backend).

| Project | What It Does | Why It's Good for the Tutorial |
|---------|-------------|-------------------------------|
| **Expense Tracker** | Log expenses, categorize, monthly summaries, budget limits | Budget math is deterministic (P13). Date vs created_at trap (P12). Financial data = security matters (P11). |
| **Link Shortener** | Create short URLs, track clicks, analytics dashboard | URL validation = security (P11). Click counting = performance/caching (P6). Dashboard = parallel FE/BE work (P2). |
| **Task Board** | Kanban board, create/move/assign tasks, filter by status | Drag-and-drop = FE complexity. Permissions = security. Status transitions = clear business logic (P13). |

---

## Tech Stack Decisions

This section is written as reasoning, not declarations. It will appear in Phase 0 so users understand WHY, not just WHAT. (Principle 6: these decisions deserve more time than adding a feature.)

### Backend: Python + FastAPI + SQLite

- **Python over Node for backend:** We use JS on the frontend — showing both languages demonstrates Claude Code works across stacks. Python's type hints + FastAPI's Pydantic make Claude's generated code more predictable and reviewable.
- **FastAPI over Flask:** Auto-generated Swagger docs at `/docs` give instant API verification without Postman or curl. Async support. Modern Python patterns Claude knows well.
- **FastAPI over Express:** We want both languages in the tutorial. If we used Express, the whole project would be JS and miss the point.
- **SQLite over Postgres for development:** Zero infrastructure. One file. No Docker dependency during development. The Docker phase (Phase 7) shows upgrading to Postgres as a production conversation.

### Frontend: React + Vite

- **React over Vue/Svelte:** Largest community, Claude has the most training data on it, most transferable knowledge for the user.
- **Vite over Next.js:** We don't need SSR. Vite keeps the frontend simple and clearly separated from the backend. Next.js would blur the FE/BE boundary and confuse the parallel work demo.
- **Vite over CRA:** CRA is deprecated.

### Docker: Added Last, Not First

- Develop locally first, containerize last. Matches real workflow. Avoids Docker debugging noise during the learning phases.
- Docker Compose orchestrates both services (+ optionally Postgres for production parity).

---

## Phase Breakdown

### Phase 0: Setup & Decisions (~5 min, near-zero tokens)

**Principles demonstrated:** 1 (first lines matter), 5 (technical users), 6 (decisions deserve time), 7 (simplicity wins)

**What happens:**
- Prerequisites checklist: Claude Code CLI, Python 3.11+, Node 18+, Git, GitHub account
- Link to download [Docker Desktop](https://www.docker.com/products/docker-desktop/) — install now, use in Phase 7
- Choose project from the 3 options (or bring your own)
- Read through tech stack reasoning
- Create GitHub repo, clone locally
- Copy starter templates into project: CLAUDE.md, CHANGELOG.md, REQUIREMENTS.md, BUGS.md
- REQUIREMENTS.md comes pre-populated with all features organized by phase

**Commits:**
```
git add CLAUDE.md CHANGELOG.md REQUIREMENTS.md BUGS.md README.md
git commit -m "initial commit: project setup with CLAUDE.md and tracking files"
git push -u origin main
```

**Token cost:** Minimal — this phase is manual setup, not Claude Code prompting.

---

### Phase 1: Foundation (~10 min)

**Principles demonstrated:** 1 (first lines determine everything), 3 (force multiplier), 8 (agent experience), 9 (own your prompts)

**What happens:**
- Open Claude Code CLI in the project directory
- Claude reads CLAUDE.md automatically
- **Prompt:** Scaffold the backend — FastAPI project structure, database connection, health endpoint
- **Review diff:** Check folder structure, naming conventions, import patterns
- **Commit + push**
- **Prompt:** Scaffold the frontend — Vite + React, routing setup, placeholder page
- **Review diff:** Check component patterns, folder structure
- **Commit + push**
- **Health check:** Ask Claude to add a `/api/ping` endpoint. Does it match existing patterns in 1 shot? If yes → foundation is solid.
- Update CHANGELOG.md
- Build a `/add-endpoint` custom skill in `~/.claude/commands/` based on the pattern established (Principle 9 — skills emerge from actual need)

**Key teaching moments:**
- Show the user what a GOOD scaffold looks like vs a messy one
- Explain why reviewing the first diff carefully is worth 10x more than reviewing the 50th

---

### Phase 2: Backend Core (~10 min)

**Principles demonstrated:** 6 (schema decisions take time), 12 (check git diff), 11 (not optimized by default)

**What happens:**
- **Prompt:** Create database models for the main resource
- **Review diff:** The guide highlights what to look for — did Claude use `created_at` where it should use `transaction_date`? Proper constraints? Indexes?
- **Commit**
- **Prompt:** Create CRUD endpoints for the main resource
- **Review diff:** Input validation? Error handling? SQL injection safety?
- **Commit**
- **Prompt:** Add secondary resource (categories / analytics / statuses)
- **Commit + push**
- Update CLAUDE.md with backend conventions that emerged
- Update REQUIREMENTS.md — check off completed features

**Key teaching moments:**
- The planted diff review: guide points out a realistic mistake Claude commonly makes (date field naming, missing validation, overly permissive defaults)
- Show how updating CLAUDE.md prevents the same mistake in future prompts

---

### Phase 3: Frontend Core (~10 min)

**Principles demonstrated:** 3 (force multiplier), 4 (1-shot test), 9 (own your prompts)

**What happens:**
- **Prompt:** Create the main list/table page
- **Review:** Does it follow the component pattern from Phase 1 scaffold?
- **Commit**
- **Prompt:** Create the add/edit form
- **Commit**
- **Prompt:** Create the API client module (fetch wrapper)
- **Commit + push**
- **Health check:** Ask Claude to add a "delete" button with confirmation. 1 shot? Clean? Matches patterns?

**Key teaching moments:**
- Principle 3 in action: because Phase 1's patterns were clean, Claude replicates them consistently without extra instruction
- Show the "continue" workflow: prompt → review output → "looks good, continue" or "change X first"

---

### Phase 4: Integration (~10 min)

**Principles demonstrated:** 4 (1-shot test), 11 (not optimized by default), 12 (check git diff)

**What happens:**
- **Prompt:** Connect frontend to backend, add CORS config
- **Review:** Is CORS config `*` or properly restricted? (Principle 11)
- **Commit**
- **Prompt:** Wire up full create → list → delete flow end-to-end
- **Verify in browser:** Add an item, see it in the list, delete it
- **Commit + push**
- **Health check:** Ask Claude to "add filter by category" in 1 shot. Works? → architecture holds. Doesn't? → investigate what got messy (Principle 4)
- Log any bugs found in BUGS.md

**Key teaching moments:**
- This is where the planted bug lives — Claude introduces a subtle issue (e.g., timezone mismatch between FE and BE, or CORS set to `*`). The user catches it in diff review, logs it in BUGS.md, and fixes it. This makes BUGS.md feel real.
- Verification is non-negotiable: actually open the browser and test

---

### Phase 5: Parallel Work (~10 min)

**Principles demonstrated:** 2 (parallel agents, zero chaos), 10 (process alignment)

**What happens:**
- Create two branches: `feature/search` and `feature/export`
- Open two terminal windows (or tmux panes)
- Terminal 1: checkout `feature/search`, run `claude`
- Terminal 2: checkout `feature/export`, run `claude`
- Give each a focused, specific prompt for its feature
- Show how CLAUDE.md provides consistent context to both agents
- Both agents work simultaneously without stepping on each other
- Merge both branches into main
- Resolve any conflicts (guide explains why there should be few if architecture is clean)
- **Commit + push**

**Key teaching moments:**
- This phase only works because of Phase 0-1 discipline — that's the entire point
- Show that CLAUDE.md is what keeps parallel agents aligned
- Demonstrate that independent features on independent branches = clean merges

---

### Phase 6: Hardening (~5 min)

**Principles demonstrated:** 11 (not optimized by default), 13 (don't use LLM for 1+1), 12 (check git diff)

**What happens:**
- **Prompt:** Add tests for core API endpoints
- **Commit**
- **Prompt:** "Review this codebase for security issues"
- **Review:** Did Claude over-engineer? Add unnecessary abstractions? Suggest LLM calls for simple calculations? (Principle 13)
- **Commit**
- **Diff review exercise:** The guide presents a realistic diff with a subtle logic bug. User must spot it before continuing.
- **Commit + push**
- Update CHANGELOG.md

**Key teaching moments:**
- Principle 13: if Claude suggests calling an API or LLM for something a simple function handles, push back
- Security review prompts should be explicit: "check for SQL injection, XSS, open redirects, CORS misconfiguration" — not just "make it secure"
- Tests should cover the critical paths, not aim for 100% coverage in a tutorial

---

### Phase 7: Docker & Ship (~5 min)

**Principles demonstrated:** 8 (agent experience is a priority)

**What happens:**
- **Prompt:** Create Dockerfile for backend
- **Prompt:** Create Dockerfile for frontend (or combined with nginx)
- **Prompt:** Create docker-compose.yml orchestrating both services
- `docker compose up --build`
- Open browser → see the working app
- **Final commit + push**
- `git tag v1.0`
- Final CLAUDE.md update reflecting the complete project state
- Final CHANGELOG.md entry

**Retrospective questions (for the user to reflect on):**
- What would you change about CLAUDE.md if starting over? (Principle 8)
- Which prompts could be 1-shot but weren't? Why? (Principle 4)
- Where did Claude surprise you — positively or negatively?

---

## Key Patterns Across All Phases

### The Commit Rhythm

Every step that changes code ends with specific `git add <files>` + `git commit -m "..."`. Never `git add .` — always name the files. This trains discipline and makes diffs reviewable (Principle 12).

Push to GitHub after each phase (not each step). Tag phase completions.

### The "Continue" Workflow

The guide explicitly shows this pattern throughout:
1. Give Claude a prompt
2. Claude generates code
3. Review the output in your editor
4. Say "continue" / "looks good" / "change X before continuing"
5. Commit when satisfied

This is NOT one-shot-and-done. It's iterative and intentional.

### The `/clear` Checkpoints

At the start of each phase, run `/clear` in Claude Code to reset context. CLAUDE.md provides continuity — Claude reads it fresh every session. This keeps token usage lean and quality high.

### CLAUDE.md Evolution

The guide shows CLAUDE.md at the end of each phase with additions highlighted. It starts minimal in Phase 0 and grows organically as conventions emerge. By Phase 7 it's a mature, useful document — not because it was templated, but because it was built through experience (Principle 8).

---

## Token Usage Guidance

This tutorial is designed to fit within one $20 Claude Code Pro session (~45-60 min of active use).

**How to stay within budget:**
- `/clear` between phases — CLAUDE.md provides continuity
- Write specific prompts, not vague ones. "Create a FastAPI endpoint for POST /api/expenses with Pydantic validation for amount, category, and date" burns fewer tokens than "add an endpoint for expenses"
- If you're retrying the same prompt more than twice, stop. Either the prompt is too vague, the codebase needs cleanup, or the problem needs breaking down (Principle 4)
- Avoid debugging spirals — if something breaks, read the error yourself first before asking Claude
- Use the "continue" pattern instead of re-explaining context

---

## Tracking Files

### REQUIREMENTS.md
Pre-populated with all features organized by phase. Users check them off as they go. Minimal format — just checkboxes and short descriptions.

### CHANGELOG.md
Updated at the end of each phase. Format: date, phase number, what was added. A few starter entries shown as examples. Not a detailed log — just enough to see progress.

### BUGS.md
Starts empty (format shown). Gets its first real entry in Phase 4 when the planted bug surfaces. This makes it feel useful rather than ceremonial.

### CLAUDE.md
The core project context file. Starts as a starter template in Phase 0. Evolves through every phase. This is the most important file in the project for Claude Code workflow.

---

## What's Next (End of Tutorial)

After completing all 7 phases, users have a working full-stack app in Docker and a proven Claude Code workflow. Natural next steps:

- **Add authentication:** JWT tokens, login/signup flow, protected routes
- **Switch SQLite → Postgres:** Update docker-compose, migration scripts
- **Add CI/CD:** GitHub Actions for tests, linting, Docker build
- **Deploy:** Railway, Fly.io, or a VPS — using Claude Code to scaffold the deployment config
- **Add real-time features:** WebSockets for live updates (especially relevant for Task Board)
- **Scale the parallel workflow:** More branches, more agents, larger features

Each of these follows the same process: update REQUIREMENTS.md, update CLAUDE.md, prompt Claude, review diff, commit, push.

---

## Open Questions (Awaiting Input)

### 1. Reference implementations: branches or folders?
- **Branches:** Cleaner git history, can use `git log` and `git diff` between tags. Harder to browse on GitHub (must switch branches).
- **Folders:** Easier to browse side-by-side on GitHub. No git history per project. More files in the repo.
- **Recommendation:** Folders in the repo for easy browsing, PLUS a `reference-history` branch with tagged commits for users who want to compare git logs.

### 2. CHANGELOG/REQUIREMENTS/BUGS: mandatory or recommended?
- **Option A:** Mandatory — every phase step includes updating them.
- **Option B:** Recommended sidebar — mentioned in Phase 0, with occasional reminders, but not blocking progress.
- **Recommendation:** Introduced as mandatory in Phase 0, but framed as "this is how we do it in this tutorial — adapt to your own workflow" (Principle 9).

### 3. "What goes wrong" detour: include or skip?
- **Include:** Makes principles tangible. ~5 min detour. Could be an optional appendix.
- **Skip:** Keeps tutorial on happy path, respects time budget.
- **Recommendation:** Include as an optional appendix (`docs/appendix-what-goes-wrong.md`) so it doesn't slow down the main path but is available for curious users.

### 4. Anything else to add or change before we start building?
