# Phase 6: Testing

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P11: AI code is not optimized by default — tests verify it works correctly
- P4: The 1-shot prompt test — good tests are a sign of clean architecture

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 6.1: Add Backend Tests (pytest)

Test generation is AI's sweet spot — boring but necessary work that Claude does well.

```
Add pytest tests for the [main resource] CRUD endpoints.
Create backend/tests/test_[resource].py with tests for:
- POST /api/[resource] — creates an item and returns 201
- GET /api/[resource] — returns a list
- GET /api/[resource]/{id} — returns one item
- GET /api/[resource]/999 — returns 404 for missing item
- DELETE /api/[resource]/{id} — deletes and returns 200

Use a test database (SQLite in-memory) so tests don't affect dev data.
Use FastAPI's TestClient.
```

**Review the diff:**
- [ ] Tests use an in-memory database, not your dev database
- [ ] Each test is independent (no shared state between tests)
- [ ] Assertions check both status codes AND response body content
- [ ] No excessive mocking — these are integration tests, not unit tests

Run the tests:
```bash
cd backend && python -m pytest -v
```

**Commit:**
```bash
git add backend/tests/
git commit -m "test: add CRUD endpoint tests for [main resource]"
```

**Verify before continuing:**
- [ ] All tests pass
- [ ] Tests run in under 5 seconds

> **Stuck?** If tests fail with database errors, make sure the test file creates its own in-memory SQLite database — don't share the dev database.

---

## Step 6.2: Add Frontend Tests (Vitest + React Testing Library)

```
Set up Vitest and React Testing Library for the frontend.
Install the necessary dev dependencies, then create tests for:

1. The main list page component:
   - Renders a list of items when data is provided
   - Shows a loading state while fetching
   - Shows "No items" message when the list is empty

2. The form component:
   - Renders all form fields
   - Validates required fields before submission
   - Calls the submit handler with form data

Put tests next to their components as [ComponentName].test.jsx files.
```

**Review the diff:**
- [ ] Tests test behavior (what the user sees), not implementation details (internal state)
- [ ] No testing of CSS classes or DOM structure that could change
- [ ] Mock API calls properly — don't hit the real backend
- [ ] Tests are readable — someone unfamiliar with the code can understand what's being tested

Run the tests:
```bash
cd frontend && npx vitest run
```

**Commit:**
```bash
git add frontend/src/ frontend/package.json frontend/vite.config.*
git commit -m "test: add frontend component tests with Vitest and RTL"
```

**Verify before continuing:**
- [ ] All frontend tests pass
- [ ] Tests run in under 10 seconds

---

## Step 6.3: Test Coverage

Now let's see what we're missing:

```bash
# Backend coverage
cd backend && python -m pytest --cov=app --cov-report=term-missing

# Frontend coverage
cd frontend && npx vitest run --coverage
```

Review the coverage report. You're not aiming for 100% — focus on critical paths:

```
Ask Claude to fill test coverage gaps on the most critical paths.
Look at the coverage report and add tests for any untested:
- Error handling paths (what happens when the API returns 500?)
- Edge cases (empty strings, very long inputs, special characters)
- The secondary resource CRUD endpoints
Don't test boilerplate or configuration files.
```

**Commit + push:**
```bash
git add backend/tests/ frontend/src/
git commit -m "test: add coverage for critical paths and edge cases"
```

**Verify before continuing:**
- [ ] Backend coverage is >70% on `app/routers/` and `app/models/`
- [ ] Frontend coverage is >60% on components
- [ ] All tests still pass

---

## Step 6.4: Let Claude Update the Tracking Files

```
Review what we built in Phase 6. Then:
1. Add a Phase 6 entry to CHANGELOG.md — include the actual coverage numbers from the test reports
2. Check off completed items in REQUIREMENTS.md for Phase 6
3. If any tests revealed bugs we haven't fixed, add them to BUGS.md
```

Review what Claude wrote — did it include the real coverage numbers, or make them up?

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md BUGS.md
git commit -m "docs: update tracking files for Phase 6"
git push
```

---

## Phase 6 Complete

You now have:
- Backend integration tests covering all CRUD endpoints
- Frontend component tests covering rendering, interactions, and error states
- Coverage reports showing where gaps remain
- All tests running in under 15 seconds total

**Why this phase matters:** AI-generated tests save massive time, but you still need to verify they test the right things (P11). Tests that check implementation details instead of behavior break every time you refactor. Tests that share state between runs produce flaky results. Review the test code with the same rigor as production code.

**Next:** [Phase 7 — Hardening](phase-7-hardening.md)
