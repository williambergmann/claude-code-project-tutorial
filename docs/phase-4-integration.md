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

**This is the planted bug checkpoint.** Claude almost always sets CORS to `*` unless you explicitly tell it not to. If it did, you just caught your first real bug — let Claude log it:

```
You just set CORS allow_origins to ["*"]. That's a security issue — it should be restricted to http://localhost:5173.
1. Fix the CORS configuration
2. Log this as BUG-001 in BUGS.md with: what was wrong, why (AI code is not optimized for security by default), and how it was fixed
```

Review the BUGS.md entry Claude writes. This is how BUGS.md stays alive — the agent populates it as bugs are found, not as a chore at the end.

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

## Step 4.3: Add Integration Tests

Now that frontend and backend are connected, test the integration:

```
Write tests for the integration we just built:
1. Frontend: test the main list page — mock the API, verify items render, verify loading state
2. Frontend: test the form component — verify submit calls the API with correct data
3. Frontend: test the delete flow — verify confirmation dialog and API call

Use Vitest + React Testing Library for frontend tests.
Install vitest, @testing-library/react, and @testing-library/jest-dom as dev dependencies if not already installed.
```

Run both test suites:
```bash
cd backend && python -m pytest -v
cd frontend && npx vitest run
```

**Verify before continuing:**
- [ ] All backend tests still pass (no regressions from CORS or integration changes)
- [ ] New frontend tests pass
- [ ] Frontend tests mock the API properly (no real network calls in tests)

**Commit:**
```bash
git add backend/tests/ frontend/src/ frontend/package.json frontend/vite.config.*
git commit -m "test: add frontend component tests for CRUD flow"
```

> **Why here?** You just connected two systems. Integration points are where bugs hide. A test that worked in Phase 2 (isolated backend) might fail now that CORS and real API calls are involved.

---

## Step 4.4: Health Check — The 1-Shot Test (Again)

Your most important test yet (P4):

```
Add a filter dropdown that lets users filter [resources] by [category/status/type]. The filter should call the API with a query parameter.
```

**Evaluation criteria:**
- Did Claude add the query parameter to the backend endpoint?
- Did it update the frontend to send the filter?
- Did it do it in ONE SHOT without breaking existing functionality?

**Verify before continuing:**
- [ ] Claude modified both backend AND frontend in one shot
- [ ] The filter works in the browser (select a category/status → list updates)
- [ ] Existing CRUD operations still work (no regressions)
- [ ] All tests still pass

If the 1-shot test failed, investigate. Common causes:
- CLAUDE.md is missing conventions Claude needs
- The code has gotten tangled (too much in one file, unclear module boundaries)
- The prompt was too vague — try being more specific

**Commit:**
```bash
git add backend/ frontend/
git commit -m "feat: add filter by [category/status] with query parameter"
```

---

## Step 4.5: Review and Log Any Bugs

Let Claude do a sweep and update all tracking files at once:

```
Review the code we built in Phase 4. Then:
1. Check for any issues: hardcoded values, inconsistent error handling, leftover console.logs, broken filter reset. Log anything you find in BUGS.md.
2. Add a Phase 4 entry to CHANGELOG.md summarizing the integration work
3. Check off completed items in REQUIREMENTS.md for Phase 4
```

**Review what Claude wrote:**
- [ ] BUGS.md entries are real issues (not padding)
- [ ] CHANGELOG.md accurately summarizes what was built
- [ ] REQUIREMENTS.md has the correct items checked off

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

