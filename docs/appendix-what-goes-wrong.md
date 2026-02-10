# Appendix: What Goes Wrong

What happens when you break the principles? This appendix walks through three common failures — not to scare you, but to make the principles tangible.

---

## Failure 1: The Vague Mega-Prompt

**Principle violated:** P1 (first lines matter), P4 (1-shot test)

### What someone does:
```
Build me a full-stack expense tracker with React and FastAPI.
Include categories, monthly reports, budget limits, authentication,
and a dashboard with charts. Use SQLite for now.
```

### What happens:
- Claude generates 20+ files in one shot
- File organization is inconsistent — some logic in `utils.py`, some inline
- No clear pattern for routers, schemas, or components
- Authentication is half-baked (JWT tokens but no refresh flow)
- The "dashboard" is a placeholder with random chart library
- Tests? None.

### Why it fails:
The first thousand lines are a mess because no conventions were established first. Claude invented its own patterns on the fly. Every file follows a slightly different style. Now every future prompt produces more inconsistency because there's no clean pattern to follow.

### How the tutorial prevents this:
- Phase 0 establishes CLAUDE.md before any code
- Phase 1 scaffolds the skeleton first, reviews it carefully
- Each feature is added incrementally with diff review
- The 1-shot health check catches degradation early

---

## Failure 2: The Skipped Diff Review

**Principle violated:** P11 (not optimized by default), P12 (check git diff)

### What someone does:
They're moving fast. Claude generates code, it runs, they commit without reviewing the diff.

### What happens over time:

**Commit 1:** Claude sets CORS to `*`. Works fine locally.

**Commit 3:** Claude uses `Float` for money amounts. $10.99 becomes $10.989999999999998.

**Commit 5:** Claude uses `created_at` where it should be `transaction_date`. Monthly reports now show expenses on the day they were entered, not the day they occurred.

**Commit 8:** Claude adds a `utils.py` with a function that duplicates logic already in a model method. Two sources of truth.

**Commit 12:** Claude imports `openai` to "classify expenses into categories." You now have an API key dependency for something a switch statement handles (P13).

### Why it compounds:
Each of these issues is small. Each one "works." But:
- CORS `*` is a security hole in production
- Float math breaks financial calculations in edge cases
- Wrong date field means reports are wrong — and you won't notice until a user complains
- Duplicated logic means bugs only get fixed in one place
- An LLM call for classification adds latency, cost, and a dependency for a deterministic operation

By commit 20, the codebase has 5+ subtle issues that all "work" but are fundamentally wrong.

### How the tutorial prevents this:
- Phase 2 explicitly calls out the `created_at` trap
- Phase 4 plants a CORS bug so you practice catching it
- `/review-diff` skill catches these issues before they're committed
- The lint hook catches style issues automatically

---

## Failure 3: The Context Bloat

**Principle violated:** P7 (complex setups suck), P4 (1-shot test)

### What someone does:
They never run `/clear`. They keep adding context, explaining history, pasting error messages. The conversation grows to 80%+ of the context window.

### What happens:
- Claude's responses get slower
- Claude starts "forgetting" earlier instructions
- Claude contradicts its own previous suggestions
- Code quality drops — more generic, less tailored to the project
- The user compensates by adding MORE context, making it worse

### Symptoms:
- "Didn't we already fix this?" — Claude re-introduces a bug it fixed earlier
- "Why did you change the file structure?" — Claude loses track of conventions
- Prompts that worked in 1 shot now take 2-3 tries
- Claude starts generating boilerplate-heavy code instead of following project patterns

### Why it fails:
Context windows are finite. As the conversation grows, Claude's attention is spread across more text. Older instructions (including CLAUDE.md conventions) get less weight. The signal-to-noise ratio drops.

### How the tutorial prevents this:
- `/clear` at the start of every phase
- CLAUDE.md provides continuity — Claude reads it fresh every time
- Small, focused prompts instead of long explanations
- Skills encapsulate repeated patterns so you don't re-explain them
- The token budget guidance helps users recognize when they're burning tokens on retries

---

## The Common Thread

All three failures share a root cause: **skipping the process to go faster.**

- Mega-prompts skip the planning and review steps
- Skipped diffs skip the verification step
- Context bloat skips the cleanup step

The irony is that skipping these steps feels fast but costs more time later. You ship bugs, accumulate tech debt, and spend tokens on retries. The tutorial's process — plan, prompt, review, commit, clear — takes slightly longer per step but eliminates the compounding cost of shortcuts.

That's Principle 3: AI is a force multiplier in whatever direction you're already going. If your process is disciplined, AI makes you faster. If your process is sloppy, AI makes you sloppier faster.

---

## Try It Yourself

Want to experience this firsthand? After completing the tutorial, try this experiment:

1. Start a new project with NO CLAUDE.md
2. Give Claude a mega-prompt to build the whole thing
3. Compare the output to what you built step-by-step

The difference is striking. And now you know why.
