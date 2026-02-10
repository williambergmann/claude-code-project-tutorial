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

> **Stuck?** Compare with `reference/expense-tracker/backend/app/models/`

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

> **Stuck?** Compare with `reference/expense-tracker/backend/app/routers/`

---

## Step 2.3: Add Secondary Resource

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

## Step 2.4: Update CLAUDE.md

Your project now has backend conventions that have emerged naturally. Capture them:

```
Review the backend code we've built and suggest 2-3 additions to the Conventions section of CLAUDE.md that would help maintain consistency going forward.
```

Common additions:
- "All routers follow the pattern: `routers/{resource}.py` with a prefix of `/api/{resource}`"
- "Pydantic schemas go in `schemas/{resource}.py` with `Create`, `Update`, and `Response` variants"
- "Database sessions use dependency injection via `get_db()`"

Add the useful ones to CLAUDE.md manually. This is your file (P9).

**Commit + push:**
```bash
git add CLAUDE.md REQUIREMENTS.md
git commit -m "docs: update CLAUDE.md with backend conventions, check off Phase 2 requirements"
git push
```

---

## Phase 2 Complete

You now have:
- Database models with proper constraints and field names
- Full CRUD endpoints for two resources with Pydantic validation
- A Swagger UI for instant verification
- CLAUDE.md updated with backend conventions

**Why this phase matters:** Schema decisions (P6) and diff review discipline (P12) prevent the kind of subtle bugs that compound over time. If you let Claude use `created_at` for `transaction_date` now, it'll do it everywhere — and you won't catch it until it matters.

**Next:** [Phase 3 — Frontend Core](phase-3-frontend.md)
