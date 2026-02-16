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
│   ├── phase-6-testing.md        # Backend tests, frontend tests, coverage
│   ├── phase-7-hardening.md      # Security audit, /review-diff skill, lint hook
│   ├── phase-8-cicd.md           # GitHub Actions, branch protection
│   ├── phase-9-docker.md         # Containerize, docker-compose up, done
│   └── appendix-what-goes-wrong.md  # What happens when you break the principles
├── templates/                    # Starter files the user copies into their project
│   ├── CLAUDE.md.starter         # Minimal Phase 0 version
│   ├── settings.json             # Permission rules (.claude/settings.json)
│   ├── gitignore                 # Starter .gitignore (Python + Node + .env)
│   ├── mcp.json                  # GitHub MCP config (Phase 5)
│   ├── CHANGELOG.md              # Format example + a couple entries
│   ├── REQUIREMENTS.md           # Pre-populated with all features by phase
│   └── BUGS.md                   # Empty with format shown
├── skills/                       # Custom skills built during the tutorial
│   ├── add-endpoint/
│   │   └── SKILL.md              # Built in Phase 1 — scaffold a new API endpoint
│   ├── add-component/
│   │   └── SKILL.md              # Built in Phase 3 — scaffold a React component
│   └── review-diff/
│       └── SKILL.md              # Built in Phase 7 — review staged changes
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
- **SQLite over Postgres for development:** Zero infrastructure. One file. No Docker dependency during development. The Docker phase (Phase 9) shows upgrading to Postgres as a production conversation.

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
- Link to download [Docker Desktop](https://www.docker.com/products/docker-desktop/) — install now, use in Phase 9
- Choose project from the 3 options (or bring your own)
- Read through tech stack reasoning
- Create GitHub repo, clone locally
- Copy starter templates into project: CLAUDE.md, .gitignore, CHANGELOG.md, REQUIREMENTS.md, BUGS.md
- REQUIREMENTS.md comes pre-populated with all features organized by phase

**Claude Code setup:**
- Run `claude` in the project, then use `/init` to generate a baseline CLAUDE.md
- Review what `/init` produces — then replace/merge with the starter template. The point: AI gives you a draft, you make it yours (Principle 9)
- Set up `.claude/settings.json` with permission rules:
  ```json
  {
    "permissions": {
      "allow": [
        "Bash(python -m pytest *)",
        "Bash(npm run *)",
        "Bash(git diff *)",
        "Bash(git status)",
        "Bash(git log *)"
      ],
      "deny": [
        "Bash(rm -rf *)",
        "Read(./.env)",
        "Read(./.env.*)"
      ]
    }
  }
  ```
- This reduces permission-click fatigue during the tutorial and teaches security hygiene from the start. Claude cannot read `.env` files — ever.

**Environment config:**
- Create `.env.example` with dummy values for any secrets the project will need (e.g., `DATABASE_URL=sqlite:///./app.db`)
- Create `.env` from the example: `cp .env.example .env`
- Add `.env` to `.gitignore` — secrets never get committed
- Install `python-dotenv` in requirements — the app loads config from `.env`
- This ties directly to the permission deny rules: Claude can't read `.env`, and now there's a reason

**Commits:**
```
git add CLAUDE.md CHANGELOG.md REQUIREMENTS.md BUGS.md README.md .claude/settings.json .env.example .gitignore
git commit -m "initial commit: project setup with CLAUDE.md, tracking files, and permissions"
git push -u origin main
```

**Token cost:** Minimal — this phase is mostly manual setup. The only Claude interaction is `/init`.

---

### Phase 1: Foundation (~10 min)

**Principles demonstrated:** 1 (first lines determine everything), 3 (force multiplier), 8 (agent experience), 9 (own your prompts)

**What happens:**
- Open Claude Code CLI in the project directory
- **Press Shift+Tab twice** to enter Plan Mode before the first prompt
- Claude reads CLAUDE.md automatically and can only analyze, not edit — use this to validate your approach
- **Prompt (in plan mode):** "I need to scaffold a FastAPI backend with SQLite and a React+Vite frontend. Review my CLAUDE.md and propose the project structure, folder layout, and key files before we start."
- Review the plan. Say "continue" or adjust. **Then exit plan mode (Shift+Tab)** so Claude can write code.
- **Prompt:** Scaffold the backend — FastAPI project structure, database connection, health endpoint
- **Review diff:** Check folder structure, naming conventions, import patterns
- **Commit + push**
- **Prompt:** Scaffold the frontend — Vite + React, routing setup, placeholder page
- **Review diff:** Check component patterns, folder structure
- **Commit + push**
- **Health check:** Ask Claude to add a `/api/ping` endpoint. Does it match existing patterns in 1 shot? If yes → foundation is solid.
- Update CHANGELOG.md

**Build your first custom skill:**

Now that you've established a backend pattern, create a skill so Claude replicates it consistently. Create `.claude/skills/add-endpoint/SKILL.md`:

```yaml
---
name: add-endpoint
description: Scaffold a new FastAPI endpoint following project conventions
disable-model-invocation: true
argument-hint: "[resource-name]"
allowed-tools: Read, Edit, Write
---

Create a new FastAPI endpoint for $ARGUMENTS following the existing patterns in the codebase.

Current project structure:
!`find backend/app -type f -name "*.py" | head -20`

Requirements:
- Follow the same file organization as existing endpoints
- Include Pydantic request/response models
- Add input validation
- Register the router in the main app
- Do NOT create tests yet (we handle those separately)
```

The `!`find ...`` syntax injects live project context before Claude starts — it always sees the current file layout, not a stale snapshot.

**Key teaching moments:**
- Plan mode first, code second — especially when starting a phase
- Skills emerge from actual patterns, not premature templates (Principle 9)
- The first diff is worth 10x more attention than the 50th — these patterns get replicated everywhere

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

**Database migrations (Alembic):**
- After models are created, set up Alembic for schema migrations
- **Prompt:** "Set up Alembic for database migrations. Create the initial migration from the current models."
- Generate the initial migration: `alembic revision --autogenerate -m "initial schema"`
- Run it: `alembic upgrade head`
- Then demonstrate a schema change: add a `notes` field to the main resource
- Generate a new migration and apply it
- This teaches the real-world workflow: models change → migration generated → migration applied (P6)

**Key teaching moments:**
- The planted diff review: guide points out a realistic mistake Claude commonly makes (date field naming, missing validation, overly permissive defaults)
- Show how updating CLAUDE.md prevents the same mistake in future prompts
- Alembic migrations show that schema decisions (P6) have consequences — you can't just change a model and hope

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

**Build your second custom skill:**

Same idea as Phase 1, but for the frontend. Create `.claude/skills/add-component/SKILL.md`:

```yaml
---
name: add-component
description: Scaffold a React component following project conventions
disable-model-invocation: true
argument-hint: "[ComponentName]"
allowed-tools: Read, Edit, Write
---

Create a new React component called $ARGUMENTS following the existing patterns.

Current components:
!`find frontend/src -type f -name "*.jsx" -o -name "*.tsx" | head -20`

Requirements:
- Match the file naming and export pattern of existing components
- Include prop types or TypeScript interfaces if the project uses them
- Follow the same styling approach (CSS modules, Tailwind, etc.)
- Place in the appropriate directory based on component type
```

**Key teaching moments:**
- Principle 3 in action: because Phase 1's patterns were clean, Claude replicates them consistently without extra instruction
- Show the "continue" workflow: prompt → review output → "looks good, continue" or "change X first"
- Two skills now exist — both emerged from need, not premature planning

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

**Set up git worktrees** (better than plain branches — full filesystem isolation):
```bash
# From your project root
git worktree add ../my-app-search -b feature/search
git worktree add ../my-app-export -b feature/export
```

Each worktree is a separate directory with its own files, its own `node_modules`, its own Claude session. No interference.

**Run two Claude instances simultaneously:**
- Terminal 1: `cd ../my-app-search && claude`
- Terminal 2: `cd ../my-app-export && claude`
- Give each a focused, specific prompt for its feature
- Both agents read the same CLAUDE.md — consistent context without coordination overhead
- Both agents work simultaneously without stepping on each other

**Set up project-scoped MCP for GitHub** (optional but demonstrates team workflow):

Create `.mcp.json` in the project root:
```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
```

This lets Claude create PRs, comment on issues, and interact with GitHub directly from the CLI. Committed to version control = every team member gets it automatically (Principle 10).

**Merge and clean up:**
```bash
cd /path/to/my-app   # back to main worktree
git merge feature/search
git merge feature/export
git worktree remove ../my-app-search
git worktree remove ../my-app-export
```

Resolve any conflicts (guide explains why there should be few if architecture is clean).

- **Commit + push**
- Optionally use Claude with GitHub MCP to create a PR summarizing both features

**Key teaching moments:**
- This phase only works because of Phase 0-1 discipline — that's the entire point
- CLAUDE.md is what keeps parallel agents aligned — both read it, both follow the same conventions
- Git worktrees > plain branches for parallel AI work (full isolation, no stashing, no switching)
- Project-scoped `.mcp.json` = team alignment baked into the repo (Principle 10)

---

### Phase 6: Testing (~10 min)

**Principles demonstrated:** 11 (not optimized by default), 4 (1-shot test)

Test generation is AI's sweet spot — boring but necessary work that Claude does well. This phase covers both backend and frontend testing.

**What happens:**

**Backend tests (pytest):**
- **Prompt:** Create pytest tests for all CRUD endpoints on the main resource. Use FastAPI's TestClient with an in-memory SQLite database. Test success cases and error cases (404, 422).
- **Review:** Are tests independent? Do they use a test database? Do assertions check response body, not just status codes?
- **Commit**

**Frontend tests (Vitest + React Testing Library):**
- **Prompt:** Set up Vitest and React Testing Library. Write tests for the main list page component and the form component. Test rendering, user interactions (click, type, submit), and API error states.
- **Review:** Are tests testing behavior (what the user sees) not implementation details (internal state)?
- **Commit**

**Test coverage:**
- Run `pytest --cov` and `npx vitest --coverage` to see coverage
- Ask Claude to fill gaps on critical paths — not aiming for 100%, just covering the paths that matter
- **Commit + push**

**Key teaching moments:**
- AI-generated tests save massive time, but you still need to verify they test the right things
- Frontend tests should test behavior (clicks, renders) not implementation (state, refs)
- Coverage is a guide, not a goal — 80% on critical paths beats 100% on boilerplate

---

### Phase 7: Hardening (~5 min)

**Principles demonstrated:** 11 (not optimized by default), 13 (don't use LLM for 1+1), 12 (check git diff)

**What happens:**
- **Prompt:** "Review this codebase for security issues — check specifically for SQL injection, XSS, open redirects, CORS misconfiguration, and missing input validation"
- **Review:** Did Claude over-engineer? Add unnecessary abstractions? Suggest LLM calls for simple calculations? (Principle 13)
- **Commit**
- **Prompt:** "Audit the frontend for basic accessibility issues — missing alt text, form labels, keyboard navigation, color contrast"
- **Commit**
- **Diff review exercise:** The guide presents a realistic diff with a subtle logic bug. User must spot it before continuing.
- **Commit + push**

**Build your third custom skill — `/review-diff`:**

This is the most powerful skill in the tutorial. Create `.claude/skills/review-diff/SKILL.md`:

```yaml
---
name: review-diff
description: Review staged git changes for bugs, security issues, and convention violations
disable-model-invocation: true
context: fork
allowed-tools: Read, Grep, Glob
---

Review the following staged changes for issues:

!`git diff --staged`

Check for:
1. Security issues (SQL injection, XSS, open redirects, hardcoded secrets)
2. Logic bugs (wrong variable names, off-by-one, timezone mismatches)
3. Convention violations (does it match existing patterns in the codebase?)
4. Missing validation or error handling at system boundaries
5. Unnecessary complexity or over-engineering

Do NOT suggest style changes or add comments to working code.
Report only real issues. If everything looks clean, say so.
```

Key details:
- `context: fork` runs this in a subagent — it doesn't pollute your main conversation context
- `!`git diff --staged`` injects the actual diff before Claude sees it
- `allowed-tools: Read, Grep, Glob` lets Claude check the surrounding code for context — read-only, no edits

**Set up a PostToolUse lint hook:**

Add to `.claude/settings.json`:
```json
{
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

Create `.claude/hooks/lint.sh`:
```bash
#!/bin/bash
cd backend && python -m ruff check . --quiet 2>/dev/null
cd ../frontend && npx eslint . --quiet 2>/dev/null
```

**Key teaching moments:**
- Principle 13: if Claude suggests calling an API or LLM for something a simple function handles, push back
- The `/review-diff` skill becomes a permanent part of your workflow beyond this tutorial
- One hook is enough to demonstrate the concept — don't over-build (Principle 7)
- Accessibility is an underappreciated AI use case — Claude catches issues humans miss

---

### Phase 8: CI/CD (~5 min)

**Principles demonstrated:** 10 (process alignment in teams), 2 (parallel work is safer with CI)

CI/CD is one of AI's highest-value use cases. Writing GitHub Actions YAML is tedious, error-prone, and Claude nails it.

**What happens:**
- **Prompt:** "Create a GitHub Actions workflow at `.github/workflows/ci.yml` that runs on every push and pull request to main. It should: install Python and Node dependencies, run backend tests with pytest, run frontend tests with vitest, run backend linting with ruff, run frontend linting with eslint. Use a matrix strategy for Python 3.11 and Node 18."
- **Review:** Is the YAML valid? Are dependencies cached? Are secrets handled correctly?
- **Commit + push** — watch the workflow run on GitHub

**Branch protection:**
- **Prompt:** "Show me the gh CLI commands to set up branch protection on main: require CI to pass before merging, require at least 1 review."
- Run the commands (or set up manually in GitHub settings)
- This means parallel work from Phase 5 now has a safety net — CI catches broken merges

**Key teaching moments:**
- CI YAML is where Claude saves the most tedious time — one prompt generates a complete workflow
- Branch protection + CI = parallel agents can't break main (P2 + P10)
- Show how CI catches issues that local testing might miss (different OS, clean install)
- Mention headless Claude Code in CI as a "What's Next" teaser

---

### Phase 9: Docker & Ship (~5 min)

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

## Areas Ranked by Value for Claude Code

What AI does best for this tutorial, ranked by value-to-effort ratio:

| Rank | Area | Value | Why |
|------|------|-------|-----|
| 1 | **Test generation** | Very High | Boring but necessary. Claude writes good tests fast. Backend + frontend. |
| 2 | **CI/CD YAML** | Very High | GitHub Actions YAML is tedious and error-prone. Claude nails it in one shot. |
| 3 | **Docker config** | High | Dockerfiles + compose are boilerplate-heavy. One prompt = working setup. |
| 4 | **Security audit** | High | Claude checks for OWASP top 10 issues you'd miss. Cheap to run. |
| 5 | **Accessibility audit** | Medium | Catches missing alt text, labels, keyboard nav. Underappreciated use case. |
| 6 | **Database migrations** | Medium | Alembic setup is fiddly. Claude handles the boilerplate, you own the schema. |
| 7 | **Environment config** | Medium | .env patterns are simple but easy to forget. Good early habit. |

---

## Claude Code Workflow Features Used in This Tutorial

This tutorial doesn't just use Claude Code to generate code — it teaches the workflow features that make the agent predictable, efficient, and safe. Each feature is introduced when it's needed, not all at once.

### Features by Phase

| Phase | Feature Introduced | Why Here |
|-------|-------------------|----------|
| 0 | `/init`, permission settings (`.claude/settings.json`) | Establish guardrails before any code is generated |
| 1 | Plan Mode (Shift+Tab), custom skill `/add-endpoint` | Plan before coding; build a skill from observed patterns |
| 3 | Custom skill `/add-component` | Second skill, frontend side — skills emerge from need |
| 5 | Git worktrees, project-scoped `.mcp.json` (GitHub MCP) | Full isolation for parallel work; team-shareable MCP config |
| 7 | Custom skill `/review-diff`, PostToolUse lint hook | Pre-commit review skill; auto-lint on every edit |
| 9 | Retrospective on all features | What worked, what you'd change |

### Custom Skills (3 total)

Skills are folders in `.claude/skills/` with a `SKILL.md` file. They replace the older `~/.claude/commands/` system.

| Skill | Phase | What It Does | Key Feature |
|-------|-------|-------------|-------------|
| `/add-endpoint` | 1 | Scaffolds a FastAPI endpoint matching project conventions | `!`find`` injects live file tree; `allowed-tools` auto-approves Read/Edit/Write |
| `/add-component` | 3 | Scaffolds a React component matching project conventions | Same pattern, frontend side |
| `/review-diff` | 7 | Reviews `git diff --staged` for bugs and security issues | `context: fork` runs in subagent; read-only tools only |

**Design principles for skills in this tutorial:**
- Each one emerges from a repeated action during the build (Principle 9)
- `disable-model-invocation: true` on all three — only the user triggers them
- `!`command`` syntax injects live project context — skills stay accurate as the project grows
- Skills are committed to `.claude/skills/` in the project repo — team members get them automatically

### MCP (Model Context Protocol)

One MCP server is introduced: **GitHub**, via project-scoped `.mcp.json` in Phase 5.

**Why only one:** Principle 7 (complex setups suck). One well-chosen MCP server demonstrates the concept. Users can add more after the tutorial.

**Why GitHub:** It's the most universally useful. Create PRs from Claude, comment on issues, search code across repos. And the config file is committed to version control — team alignment for free (Principle 10).

**Why project-scoped:** `.mcp.json` at the project root means every contributor gets the same MCP configuration automatically. No per-user setup. No "it works on my machine."

### Hooks

One hook is introduced: **PostToolUse lint check** in Phase 7.

**What it does:** Every time Claude edits or writes a file, the linter runs automatically. Claude sees lint errors in real time and can self-correct before you review.

**Why only one:** Same reasoning as MCP — demonstrate the concept cleanly. The hook system supports `PreToolUse` (block dangerous commands), `Stop` (validate task completion), `UserPromptSubmit` (prompt validation), and more. But one practical example teaches more than a catalog.

### Permission Settings

Set up in Phase 0 via `.claude/settings.json`:
- **Allow:** Common dev commands (pytest, npm run, git diff/status/log) — reduces permission-click fatigue
- **Deny:** Destructive commands (rm -rf) and sensitive files (.env) — security from minute one

This is the simplest feature but arguably the most impactful for daily workflow. Less clicking = more flow state.

### Plan Mode

Used at the start of Phase 1 (and recommended at the start of every phase):
- **Shift+Tab twice** to enter plan mode
- Claude can analyze the codebase but cannot edit anything
- Review the plan, iterate, then exit plan mode to implement
- Think of it as a "read-only brainstorm" before writing code

### What's NOT in This Tutorial (and Why)

| Feature | Why It's Excluded |
|---------|------------------|
| Custom MCP servers | Building one is too complex for a ~75 min tutorial |
| Agent teams | Overkill — git worktrees achieve the same parallel work goal more simply |
| Headless/CI mode | We set up CI in Phase 8, but headless Claude in CI is advanced — mentioned in What's Next |
| Multiple hook types | One command hook demonstrates the concept; prompt/agent hooks are advanced |
| CLAUDE.md imports (`@path`) | Principle 7 — one CLAUDE.md is enough for this project |
| Custom subagents | Advanced topic better suited for post-tutorial exploration |
| Extended thinking toggle | Nice to know, not workflow-critical for this tutorial |

These are all mentioned in the "What's Next" section so users know they exist.

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

### Page-at-a-Time Pacing

Every step in every phase doc fits roughly one screen. Each step ends with a checkpoint: verification tasks, a commit command, and three options (continue / compare with reference / fix something). No scrolling back to catch up. The user is always in control of the pace.

### CLAUDE.md Evolution

The guide shows CLAUDE.md at the end of each phase with additions highlighted. It starts minimal in Phase 0 and grows organically as conventions emerge. By Phase 9 it's a mature, useful document — not because it was templated, but because it was built through experience (Principle 8).

---

## Token Usage Guidance

This tutorial is designed to fit within one $20 Claude Code Pro session (~60-75 min of active use).

**How to stay within budget:**
- `/clear` between phases — CLAUDE.md provides continuity
- Write specific prompts, not vague ones. "Create a FastAPI endpoint for POST /api/expenses with Pydantic validation for amount, category, and date" burns fewer tokens than "add an endpoint for expenses"
- If you're retrying the same prompt more than twice, stop. Either the prompt is too vague, the codebase needs cleanup, or the problem needs breaking down (Principle 4)
- Avoid debugging spirals — if something breaks, read the error yourself first before asking Claude
- Use the "continue" pattern instead of re-explaining context

---

## Tracking Files (Agent-Populated)

These files serve a dual purpose: they track progress for the human AND provide context for the agent. Claude reads them when it `/clear`s and starts fresh. The key pattern: **don't update these manually — prompt Claude to update them, then review what it writes.**

### REQUIREMENTS.md
Pre-populated with all features organized by phase. At the end of each phase, prompt Claude: "Check off completed items in REQUIREMENTS.md." Claude reads the file, evaluates what's done, and updates it. You review the changes — same as reviewing code.

### CHANGELOG.md
Updated at the end of each phase via prompt. Claude summarizes what was built, including specifics it knows (coverage numbers, feature names, issues fixed). This gives Claude history to reference when it `/clear`s and re-reads project files.

### BUGS.md
Starts empty (format shown). Gets its first real entry in Phase 4 when Claude introduces a CORS bug and is prompted to log it. From then on, any time a bug is found during development, Claude is prompted to add it to BUGS.md. This makes the file a living record, not a retrospective chore.

### CLAUDE.md
The core project context file. Starts as a starter template in Phase 0. Evolves through every phase. References the tracking files so Claude knows to read them for context. This is the most important file in the project for Claude Code workflow.

---

## What's Next (End of Tutorial)

After completing all 10 phases, users have a working full-stack app in Docker with tests, CI/CD, and a proven Claude Code workflow. Natural next steps:

- **Add authentication:** JWT tokens, login/signup flow, protected routes
- **Switch SQLite → Postgres:** Update docker-compose, add production migration scripts
- **Deploy:** Railway, Fly.io, or a VPS — using Claude Code to scaffold the deployment config
- **Add real-time features:** WebSockets for live updates (especially relevant for Task Board)
- **Scale the parallel workflow:** More branches, more agents, larger features

Each of these follows the same process: update REQUIREMENTS.md, update CLAUDE.md, prompt Claude, review diff, commit, push.

**Claude Code features to explore after the tutorial:**
- **Custom MCP servers** — build one tailored to your project's API or database
- **Agent teams** — coordinated multi-agent workflows for large codebases
- **Headless/CI mode** — run Claude Code headless in GitHub Actions for automated PR review
- **Prompt and agent hooks** — beyond command hooks, validate prompts or run multi-turn agents on events
- **CLAUDE.md imports** — `@path/to/file` syntax for modular instructions in large projects
- **Custom subagents** — specialized agents in `.claude/agents/` for domain-specific tasks
- **Extended thinking** — Option+T to toggle deeper reasoning for complex problems

---

## Decisions (Resolved)

### 1. Reference implementations: both folders AND branches
Completed implementations live in `reference/` folders for easy GitHub browsing. A separate `reference-history` branch contains the same code with tagged commits matching each phase (`v0-setup`, `v1-foundation`, etc.) so users can compare their git log against the intended progression.

### 2. Tracking files: mandatory in this tutorial, not universal
CHANGELOG.md, REQUIREMENTS.md, and BUGS.md are mandatory steps in this tutorial — every phase includes updating them. But the guide frames this honestly: "This is how we structure this project. It's not required in every situation. Adapt to your own workflow." (Principle 9)

### 3. "What goes wrong" detour: included
An appendix (`docs/appendix-what-goes-wrong.md`) walks through what happens when you violate the principles — vague mega-prompts, skipped diff reviews, bloated context. It's part of the tutorial but positioned after the main path so it doesn't slow down the primary flow. Makes the principles tangible instead of preachy.

### 4. Page-at-a-time pacing with checkpoints
Each phase doc is broken into small steps (one screen of content each). Every step ends with a verification checkpoint before the user moves on. No walls of text. The user controls the pace.

Step format:
```markdown
### Step 1.2: Scaffold the Backend

**Principle in action:** The first few thousand lines determine everything (P1)

[2-3 paragraphs of context + the prompt to use]

**Verify before continuing:**
- [ ] `backend/` directory exists with the expected structure
- [ ] `python -m uvicorn app.main:app` starts without errors
- [ ] `http://localhost:8000/docs` shows the Swagger UI

**Commit:**
git add backend/
git commit -m "feat: scaffold FastAPI backend with health endpoint"

Ready? → Step 1.3
Stuck? → Compare with reference/expense-tracker/
Need to adjust? → Update CLAUDE.md before continuing
```

This prevents the "scroll back to catch up" problem and gives the user three clear options at every gate: continue, compare, or course-correct.
