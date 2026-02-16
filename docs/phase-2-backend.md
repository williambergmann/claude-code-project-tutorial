# Phase 2: Backend Core

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P6: AI didn't speed up all steps equally — schema design deserves time
- P11: AI code is not optimized by default — verify security yourself
- P12: Check git diff for critical logic — the agent might use wrong field names

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md automatically.

---

## Step 2.1: Design the Database Schema (Don't Rush This)

Before prompting Claude, think about your schema. This is one of the decisions AI didn't speed up (P6). Get it wrong and you'll refactor later.

**For Expense Tracker:**
- `expenses`: id, amount, description, category_id, transaction_date, created_at
- `categories`: id, name, color

**For Link Shortener:**
- `links`: id, original_url, short_code, click_count, created_at
- `click_events`: id, link_id, clicked_at, referrer, user_agent

**For Task Board:**
- `tasks`: id, title, description, status, position, assignee, created_at, updated_at
- `columns`: id, name, position

Now prompt Claude:

```
Create the SQLAlchemy models for my database schema.

I need two tables:
- [your main table with specific fields listed]
- [your secondary table with specific fields listed]

Put them in backend/app/models/ with one file per model.
Use proper column types, constraints, and a foreign key relationship.
Important: use specific date fields (like transaction_date) — do NOT use created_at for business dates.
```

**Review the diff — this is critical (P12):**

| Check | Why |
|-------|-----|
| Are date fields named correctly? | Claude loves using `created_at` for everything. `transaction_date` ≠ `created_at`. |
| Are there proper NOT NULL constraints? | Claude often makes everything nullable by default. |
| Is the foreign key relationship correct? | Wrong direction = broken queries later. |
| Are column types appropriate? | `Float` for money is wrong. Use `Numeric` or `Integer` (cents). |

If you see issues, tell Claude: "Change `created_at` to `transaction_date` on the expenses table. Amount should be Integer storing cents, not Float."

**Commit:**
```bash
git add backend/app/models/
git commit -m "feat: add database models for [your resources]"
```

**Verify before continuing:**
- [ ] Models have correct field names (NOT generic `created_at` for business dates)
- [ ] Proper constraints (NOT NULL where appropriate)
- [ ] Foreign key relationship is correct
- [ ] No `Float` for money — use `Integer` (cents) or `Numeric`

> **Stuck?** If field names or types look wrong, tell Claude exactly what to change: "Change `created_at` to `transaction_date`. Use Integer for amount (storing cents)."

---

## Step 2.2: Create CRUD Endpoints — Main Resource

```
Create CRUD API endpoints for [your main resource]:
- POST /api/[resource] — create a new item
- GET /api/[resource] — list all items
- GET /api/[resource]/{id} — get one item
- PUT /api/[resource]/{id} — update an item
- DELETE /api/[resource]/{id} — delete an item

Create:
- A Pydantic schema in backend/app/schemas/ for request/response validation
- A router in backend/app/routers/ with all five endpoints
- Register the router in main.py

Use proper HTTP status codes: 201 for create, 404 for not found, 422 for validation errors.
```

**Review the diff (P11 — not optimized by default):**

| Check | Why |
|-------|-----|
| Is input validated? | Every field in the request body should be validated by Pydantic. |
| Are error responses consistent? | 404 should return `{"detail": "Not found"}`, not crash. |
| Is there any raw SQL? | Should use SQLAlchemy ORM, not string interpolation (SQL injection risk). |
| Are response models defined? | Endpoints should return Pydantic response models, not raw dicts. |

**Commit:**
```bash
git add backend/app/schemas/ backend/app/routers/ backend/app/main.py
git commit -m "feat: add CRUD endpoints for [main resource]"
```

**Verify before continuing:**
- [ ] Server starts: `python -m uvicorn app.main:app --reload`
- [ ] Swagger UI at `/docs` shows all 5 endpoints
- [ ] Create an item via Swagger → returns 201
- [ ] Get the item back → returns 200 with the data
- [ ] Delete it → returns 200
- [ ] Get it again → returns 404

> **Stuck?** If the server won't start, check the import in `main.py` — the router needs to be registered. If Swagger shows no endpoints, the router prefix might be wrong.

---

## Step 2.3: Write Tests for the Main Resource

Don't wait until Phase 6. Write tests now while the endpoints are fresh.

```
Write pytest tests for the [main resource] CRUD endpoints we just created.
Create backend/tests/test_[resource].py with tests for:
- POST /api/[resource] — creates and returns 201
- GET /api/[resource] — returns a list
- GET /api/[resource]/{id} — returns one item
- GET /api/[resource]/999 — returns 404
- DELETE /api/[resource]/{id} — returns 200

Use FastAPI's TestClient with an in-memory SQLite database so tests don't touch dev data.
```

Run them:
```bash
cd backend && python -m pytest -v
```

**Review the diff:**
- [ ] Tests use in-memory database, not your dev database
- [ ] Each test is independent (no shared state)
- [ ] Assertions check both status codes AND response body

**Commit:**
```bash
git add backend/tests/
git commit -m "test: add CRUD tests for [main resource]"
```

**Verify before continuing:**
- [ ] All tests pass
- [ ] Tests run in under 5 seconds

> **Why now?** These tests are your safety net for everything that follows. When Phase 4 connects the frontend, or Phase 5 runs parallel agents, these tests catch regressions immediately. Testing after the fact is always harder than testing alongside.

---

## Step 2.4: Add Secondary Resource

```
Create the [secondary resource] model endpoints following the same pattern as [main resource].
Use /add-endpoint skill pattern: schema file, router file, register in main.py.
```

Or use your custom skill:

```
/add-endpoint categories
```

**Review the diff:**
- Does it follow the exact same pattern as the main resource?
- If not, the skill needs tuning or the first resource's pattern wasn't clear enough.

**Commit:**
```bash
git add backend/app/schemas/ backend/app/routers/ backend/app/models/ backend/app/main.py
git commit -m "feat: add CRUD endpoints for [secondary resource]"
```

**Verify before continuing:**
- [ ] Both resources have full CRUD at `/docs`
- [ ] Foreign key relationship works (e.g., expense has a valid category_id)

---

## Step 2.5: Set Up Database Migrations (Alembic)

Now that your models exist, set up Alembic so schema changes are tracked properly. This is a decision that deserves time (P6) — you can't just change a model and hope the database updates itself.

```
Set up Alembic for database migrations.
- Initialize Alembic in the backend directory
- Configure it to use our SQLAlchemy models and database URL
- Generate the initial migration from the current models
```

Run the migration:
```bash
cd backend && alembic upgrade head
```

Now demonstrate the real workflow — add a small schema change:

```
Add a "notes" field (optional string, max 500 chars) to the [main resource] model.
Then generate an Alembic migration for this change.
```

```bash
alembic revision --autogenerate -m "add notes field to [resource]"
alembic upgrade head
```

**Review the diff:**
- [ ] `alembic/` directory exists with `env.py` configured correctly
- [ ] Initial migration creates all tables with correct columns
- [ ] Second migration adds only the `notes` column (not recreating everything)
- [ ] Model and migration are in sync

**Commit:**
```bash
git add backend/alembic/ backend/alembic.ini backend/app/models/
git commit -m "feat: add Alembic migrations with initial schema and notes field"
```

**Verify before continuing:**
- [ ] `alembic upgrade head` runs without errors
- [ ] `alembic downgrade -1` and `alembic upgrade head` both work (reversible migration)
- [ ] The Swagger UI still works with the new field

> **Stuck?** If autogenerate produces an empty migration, Alembic can't see your models. Check that `env.py` imports your models and sets `target_metadata` correctly.

---

## Step 2.6: Update CLAUDE.md

Your project now has backend conventions that have emerged naturally. Capture them:

```
Review the backend code we've built and suggest 2-3 additions to the Conventions section of CLAUDE.md that would help maintain consistency going forward.
```

Common additions:
- "All routers follow the pattern: `routers/{resource}.py` with a prefix of `/api/{resource}`"
- "Pydantic schemas go in `schemas/{resource}.py` with `Create`, `Update`, and `Response` variants"
- "Database sessions use dependency injection via `get_db()`"

Add the useful ones to CLAUDE.md. This is your file (P9).

Now let Claude update the tracking files:

```
Review what we built in Phase 2. Then:
1. Add a Phase 2 entry to CHANGELOG.md summarizing models, endpoints, and migrations
2. Check off completed items in REQUIREMENTS.md for Phase 2
```

Review what Claude wrote — make sure it's accurate.

**Commit + push:**
```bash
git add CLAUDE.md CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update CLAUDE.md with backend conventions, update tracking files for Phase 2"
git push
```

---

## Phase 2 Complete

You now have:
- Database models with proper constraints and field names
- Alembic migrations tracking every schema change
- Full CRUD endpoints for two resources with Pydantic validation
- A Swagger UI for instant verification
- CLAUDE.md updated with backend conventions

**Why this phase matters:** Schema decisions (P6) and diff review discipline (P12) prevent the kind of subtle bugs that compound over time. If you let Claude use `created_at` for `transaction_date` now, it'll do it everywhere — and you won't catch it until it matters.

**Next:** [Phase 3 — Frontend Core](phase-3-frontend.md)
