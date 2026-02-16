# Phase 6: Test Coverage & Gaps

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P11: AI code is not optimized by default — tests verify it works correctly
- P4: The 1-shot prompt test — good tests are a sign of clean architecture

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Where You Should Be

If you've been writing tests alongside features (as recommended since Phase 1), you should already have:
- Backend CRUD tests from Phase 2
- Frontend component tests and integration tests from Phase 4
- Feature tests from Phase 5 (search and export)

If you skipped testing in earlier phases, go back and add tests now before continuing. Tests are not optional in agentic coding — they're how you verify the agent's work.

This phase is about **filling gaps and making your test suite comprehensive**, not starting from scratch.

---

## Step 6.1: Run Coverage Reports

See what you're missing:

```bash
# Backend coverage
cd backend && python -m pytest --cov=app --cov-report=term-missing

# Frontend coverage
cd frontend && npx vitest run --coverage
```

Review the output. Look for:
- Untested routers or endpoints
- Components with 0% coverage
- Error handling paths that are never exercised

---

## Step 6.2: Fill Backend Test Gaps

```
Look at the backend test coverage report. Add tests for:
- The secondary resource CRUD endpoints (if not already tested)
- Error handling paths: what happens when the API gets invalid input? (422 responses)
- Edge cases: empty strings, very long inputs, special characters in text fields
- Any endpoint filters or query parameters we added in Phase 4

Don't test boilerplate or configuration files. Focus on business logic and API behavior.
```

Run the tests:
```bash
cd backend && python -m pytest -v
```

**Review the diff:**
- [ ] New tests cover untested paths identified in the coverage report
- [ ] Tests check edge cases, not just happy paths
- [ ] No excessive mocking — these are integration tests

**Commit:**
```bash
git add backend/tests/
git commit -m "test: fill backend test coverage gaps"
```

---

## Step 6.3: Fill Frontend Test Gaps

```
Look at the frontend test coverage report. Add tests for any untested:
- Components that render but have no tests
- User interactions: click delete, confirm dialog, submit form with errors
- Loading and empty states
- The search and export features from Phase 5

Test behavior (what the user sees), not implementation details (internal state).
```

Run the tests:
```bash
cd frontend && npx vitest run
```

**Review the diff:**
- [ ] Tests test behavior, not implementation details
- [ ] Mock API calls properly — don't hit the real backend
- [ ] Tests are readable — someone unfamiliar with the code can understand what's being tested

**Commit:**
```bash
git add frontend/src/ frontend/package.json frontend/vite.config.*
git commit -m "test: fill frontend test coverage gaps"
```

---

## Step 6.4: Verify Full Suite

Run everything together:

```bash
# Backend
cd backend && python -m pytest --cov=app --cov-report=term-missing -v

# Frontend
cd frontend && npx vitest run --coverage
```

**Verify before continuing:**
- [ ] Backend coverage is >70% on `app/routers/` and `app/models/`
- [ ] Frontend coverage is >60% on components
- [ ] All tests pass
- [ ] Total test runtime is under 30 seconds

---

## Step 6.5: Let Claude Update the Tracking Files

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
- Comprehensive backend tests covering all CRUD endpoints and edge cases
- Frontend component tests covering rendering, interactions, and error states
- Coverage reports showing where gaps remain
- A test suite that runs in under 30 seconds

**Why this phase matters:** AI-generated tests save massive time, but you still need to verify they test the right things (P11). Tests that check implementation details instead of behavior break every time you refactor. Tests that share state between runs produce flaky results. Review the test code with the same rigor as production code.

**Next:** [Phase 7 — Hardening](phase-7-hardening.md)
