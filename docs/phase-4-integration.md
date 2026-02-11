# Phase 4: Integration

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P4: The 1-shot prompt test
- P11: AI code is not optimized by default
- P12: Check git diff for critical logic

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 4.1: Connect Frontend to Backend — CORS

```
Add CORS middleware to the FastAPI backend.
Allow requests from http://localhost:5173 (the Vite dev server).
Do NOT use a wildcard "*" for origins.
```

**Review the diff — this is a security checkpoint (P11):**

| Check | Why |
|-------|-----|
| Is `allow_origins` set to `["http://localhost:5173"]`? | If it's `["*"]`, that's a security issue. Claude does this by default. |
| Are `allow_methods` restricted? | Should be `["GET", "POST", "PUT", "DELETE"]`, not `["*"]`. |
| Is `allow_headers` reasonable? | `["Content-Type"]` is usually enough. Not `["*"]`. |

**This is the planted bug checkpoint.** Claude almost always sets CORS to `*` unless you explicitly tell it not to. If it did, you just caught your first real bug — log it:

Open BUGS.md and add:

```markdown
## BUG-001: CORS set to wildcard
- **Found in:** Phase 4, Step 4.1
- **Severity:** Medium
- **Description:** Claude set CORS allow_origins to ["*"] instead of restricting to localhost:5173
- **Root cause:** AI code is not optimized for security by default (Principle 11)
- **Fix:** Changed to ["http://localhost:5173"]
- **Status:** Fixed
```

**Commit:**
```bash
git add backend/app/main.py BUGS.md
git commit -m "feat: add CORS middleware restricted to localhost:5173"
```

**Verify before continuing:**
- [ ] CORS origins are NOT `*`
- [ ] If Claude did use `*`, you caught it, logged it, and fixed it

---

## Step 4.2: Wire Up the Full Flow

Start both servers in separate terminals:

```bash
# Terminal 1
cd backend && python -m uvicorn app.main:app --reload

# Terminal 2
cd frontend && npm run dev
```

Now connect the frontend to the real API:

```
Update the frontend to use the API client instead of dummy data.
Wire up:
1. List page loads data from GET /api/[resource] on mount
2. Form submits to POST /api/[resource] (create) or PUT /api/[resource]/{id} (edit)
3. Delete button calls DELETE /api/[resource]/{id}
4. After create/delete, refresh the list

Handle loading states and API errors. Keep it simple — no global state management.
```

**Review the diff:**
- [ ] API calls use the client module from Phase 3 (not inline fetch)
- [ ] Error handling shows user-friendly messages
- [ ] Loading state prevents empty flickers
- [ ] No unnecessary libraries added (no Redux, no React Query)

**Commit:**
```bash
git add frontend/src/
git commit -m "feat: connect frontend to backend API with full CRUD flow"
```

**Verify before continuing:**
- [ ] Open `http://localhost:5173`
- [ ] Create a new item → it appears in the list
- [ ] Edit the item → changes are saved
- [ ] Delete the item → it disappears from the list
- [ ] Refresh the page → data persists (it's in SQLite)

> **Stuck?** Check browser console for CORS errors. Check the backend terminal for Python errors.

---

## Step 4.3: Health Check — The 1-Shot Test (Again)

Your most important test yet (P4):

```
Add a filter dropdown that lets users filter [resources] by [category/status/type]. The filter should call the API with a query parameter.
```

**Evaluation criteria:**
- Did Claude add the query parameter to the backend endpoint?
- Did it update the frontend to send the filter?
- Did it do it in ONE SHOT without breaking existing functionality?

If yes → architecture is solid through 4 phases. This is a great sign.
If no → investigate. Common causes:
- CLAUDE.md is missing conventions Claude needs
- The code has gotten tangled (too much in one file, unclear module boundaries)
- The prompt was too vague — try being more specific

**Commit:**
```bash
git add backend/ frontend/
git commit -m "feat: add filter by [category/status] with query parameter"
```

---

## Step 4.4: Review and Log Any Bugs

Look through the code Claude has generated in this phase. Common issues to check:

- Are there any hardcoded values that should be configurable?
- Is error handling consistent between endpoints?
- Are there any console.logs left in the frontend?
- Does the filter reset properly?

Log anything you find in BUGS.md.

**Commit + push:**
```bash
git add BUGS.md CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update tracking files for Phase 4"
git push
```

---

## Phase 4 Complete

You now have:
- Frontend and backend fully connected
- A working CRUD flow you can test in the browser
- Your first bug logged and fixed (CORS)
- A passing 1-shot test for adding a filter

**Why this phase matters:** This is where the rubber meets the road. Testing in the browser reveals issues that code review alone misses. And the CORS bug demonstrates P11 — AI code is not secure by default. You have to verify it yourself.

**Next:** [Phase 5 — Parallel Work](phase-5-parallel.md)

