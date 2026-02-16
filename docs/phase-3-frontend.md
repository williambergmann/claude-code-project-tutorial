# Phase 3: Frontend Core

**Time:** ~10 minutes | **Tokens:** Moderate

**Principles in action:**
- P3: AI is a force multiplier in whatever direction you're going
- P4: The 1-shot prompt test
- P9: Own your prompts, own your workflow

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 3.1: Create the Main List Page

```
Create a page component that displays a list/table of [your main resource].
Put it in frontend/src/pages/[ResourceName]Page.jsx.

For now, use hardcoded dummy data (we'll connect to the API in Phase 4).
Include:
- A table or list showing the key fields
- A header with the page title
- An "Add New" button (doesn't need to work yet)

Follow the component pattern established in the existing pages.
```

**Review the diff:**
- [ ] File is in `pages/` directory with PascalCase naming
- [ ] Component uses the same export pattern as existing pages
- [ ] Layout is clean — no over-styled first attempt
- [ ] Dummy data structure matches your backend schema

This is where P3 kicks in. Because Phase 1 established clean patterns, Claude should replicate them without being told. If the new page looks different from existing pages, the foundation needs work.

**Commit:**
```bash
git add frontend/src/pages/
git commit -m "feat: add main resource list page with dummy data"
```

**Verify before continuing:**
- [ ] `npm run dev` shows the page
- [ ] Navigation to the page works
- [ ] The table/list renders with dummy data

> **Stuck?** Compare with `reference/expense-tracker/frontend/src/pages/`

---

## Step 3.2: Create the Add/Edit Form

```
Create a form component for adding and editing [your main resource].
Put it in frontend/src/components/[ResourceName]Form.jsx.

Include:
- Input fields for each editable field in the schema
- Basic HTML validation (required fields, number type for amounts)
- Submit and Cancel buttons
- The form should accept an optional existing item for edit mode

Use the same component patterns as the rest of the project.
Don't connect to the API yet — just console.log the form data on submit.
```

**Review the diff:**
- [ ] Component is in `components/` (reusable) not `pages/`
- [ ] Follows the same style patterns as other components
- [ ] Form fields match the Pydantic schema from Phase 2
- [ ] No unnecessary state management libraries added

**Commit:**
```bash
git add frontend/src/components/
git commit -m "feat: add form component for [main resource]"
```

**Verify before continuing:**
- [ ] Form renders in the browser
- [ ] Required fields show validation
- [ ] Submit logs data to console

---

## Step 3.3: Create the API Client

```
Create an API client module at frontend/src/api/client.js.

It should:
- Export a base fetch wrapper that handles JSON parsing and error responses
- Export functions for each API endpoint: list, get, create, update, delete
- Use http://localhost:8000 as the base URL (we'll make this configurable later)
- Throw meaningful errors on non-2xx responses

Keep it simple — plain fetch, no axios. One file.
```

**Review the diff:**
- [ ] Single file in `api/` directory
- [ ] Functions match the CRUD endpoints from Phase 2
- [ ] Error handling is present (not just `fetch` with no error check)
- [ ] No unnecessary abstraction layers

**Commit:**
```bash
git add frontend/src/api/
git commit -m "feat: add API client module"
```

**Verify before continuing:**
- [ ] File exists and exports the expected functions
- [ ] No TypeScript errors or import issues

---

## Step 3.4: Health Check — The 1-Shot Test

Time to test project health again (P4).

Ask Claude to add a feature that should follow established patterns:

```
Add a delete button to each row in the [resource] list page. When clicked, show a confirmation dialog before deleting. Use the API client's delete function.
```

**What to look for:**
- Did Claude put the delete logic in the right place?
- Did it import from the API client correctly?
- Did it follow the same component and event handling patterns?
- Did it do this in **one shot**?

If yes → patterns are holding up well.
If no → figure out what's unclear. Is CLAUDE.md missing a convention? Is the code structure ambiguous?

**Commit:**
```bash
git add frontend/src/pages/ frontend/src/components/
git commit -m "feat: add delete with confirmation to resource list"
```

---

## Step 3.5: Build Your Second Custom Skill

You've now created multiple components following the same pattern. Time to capture it.

```bash
mkdir -p .claude/skills/add-component
cp ../claude-code-project-tutorial/skills/add-component/SKILL.md .claude/skills/add-component/SKILL.md
```

Review the SKILL.md and adjust it to match YOUR project's actual patterns. The template is a starting point — own it (P9).

Test it:

```
/add-component StatsCard
```

Claude should create a component that matches your existing style perfectly.

**Commit:**
```bash
git add .claude/skills/add-component/
git commit -m "feat: add /add-component custom skill"
```

**Verify before continuing:**
- [ ] `/add-component` creates files matching your project's conventions
- [ ] You now have two skills: `/add-endpoint` (backend) and `/add-component` (frontend)

---

## Step 3.6: Let Claude Update the Tracking Files

```
Review what we built in Phase 3. Then:
1. Add a Phase 3 entry to CHANGELOG.md summarizing the components, API client, and skill we created
2. Check off completed items in REQUIREMENTS.md for Phase 3
3. If anything we built doesn't appear in REQUIREMENTS.md, add it
```

**Review what Claude wrote** — same discipline as code review. Did it accurately capture what was built? Did it check off items that are actually done?

**Commit + push:**
```bash
git add CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: update tracking files for Phase 3"
git push
```

---

## Phase 3 Complete

You now have:
- A frontend with pages, components, and an API client
- Two custom skills that match your project's actual conventions
- The "continue" workflow down: prompt → review → adjust → commit

**Why this phase matters:** Principle 3 is now visible. The clean patterns from Phase 1 made every component in Phase 3 predictable. Claude didn't need extra instructions — it followed the established conventions. That's the force multiplier.

**Next:** [Phase 4 — Integration](phase-4-integration.md)
