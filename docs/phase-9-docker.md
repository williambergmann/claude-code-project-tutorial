# Phase 9: Docker & Ship

**Time:** ~5 minutes | **Tokens:** Low

**Principles in action:**
- P8: Agent experience is a priority — retrospective on the whole process

> **Start by running `/clear`** to reset context. Claude will re-read your CLAUDE.md.

---

## Step 9.1: Create the Backend Dockerfile

```
Create a Dockerfile for the FastAPI backend at backend/Dockerfile.
- Use python:3.11-slim as the base image
- Install dependencies from requirements.txt
- Run with uvicorn on port 8000
- Don't run as root — create a non-root user
Keep it simple. No multi-stage build needed for this project.
```

**Review the diff:**
- [ ] Uses a slim base image (not the full `python:3.11`)
- [ ] Copies requirements.txt and installs dependencies before copying code (layer caching)
- [ ] Runs as a non-root user
- [ ] Exposes port 8000

**Commit:**
```bash
git add backend/Dockerfile
git commit -m "feat: add backend Dockerfile"
```

---

## Step 9.2: Create the Frontend Dockerfile

```
Create a Dockerfile for the React frontend at frontend/Dockerfile.
- Build stage: use node:18-slim, install deps, run npm run build
- Serve stage: use nginx:alpine to serve the built files
- Copy a simple nginx.conf that serves the SPA (all routes → index.html)
- Serve on port 80
```

**Review the diff:**
- [ ] Multi-stage build (node for building, nginx for serving)
- [ ] `.dockerignore` or explicit COPY avoids `node_modules` in the image
- [ ] nginx config handles SPA routing (all paths → index.html)

**Commit:**
```bash
git add frontend/Dockerfile frontend/nginx.conf
git commit -m "feat: add frontend Dockerfile with nginx"
```

---

## Step 9.3: Create docker-compose.yml

```
Create a docker-compose.yml at the project root that:
- Runs the backend service on port 8000
- Runs the frontend service on port 3000
- Frontend proxies API requests to the backend (or update CORS for Docker network)
- Both services build from their respective Dockerfiles
Keep it simple. No Postgres yet — SQLite is fine for this demo.
```

**Review the diff:**
- [ ] Both services defined with `build` pointing to their directories
- [ ] Ports mapped correctly (8000 for API, 3000 for frontend)
- [ ] Backend is accessible from frontend container (Docker network or environment variable)

**Commit:**
```bash
git add docker-compose.yml
git commit -m "feat: add docker-compose.yml for full stack"
```

---

## Step 9.4: Launch It

Make sure Docker Desktop is running, then:

```bash
docker compose up --build
```

Wait for both services to build and start. Then open your browser:

- **Frontend:** `http://localhost:3000`
- **Backend API:** `http://localhost:8000/docs`

**Verify:**
- [ ] Frontend loads in the browser
- [ ] You can create, edit, and delete items
- [ ] The Swagger UI is accessible
- [ ] Data persists within the session (SQLite inside the container)

If something doesn't work, check the logs in the terminal. Common issues:
- CORS needs to allow `http://localhost:3000` (not just `5173`)
- Frontend API client needs to use the correct backend URL in Docker
- Port conflicts with other running services

Stop with `Ctrl+C` when you're done testing.

> **Stuck?** Check `docker compose logs` for specific errors. Common fixes: update CORS to allow port 3000, set the API URL as an environment variable in the frontend build.

---

## Step 9.5: Browser Testing (Optional)

If you're using an AI IDE with browser preview capabilities (like Antigravity's Chrome plugin), this is a great time to test the app visually:

1. Launch the Docker containers (`docker compose up --build`)
2. Open the browser preview pointing to `http://localhost:3000`
3. Ask your AI to visually inspect the DOM and verify:
   - All elements are rendering correctly
   - Forms submit and update the page
   - Navigation works between pages
   - The layout is responsive at different widths

This kind of visual testing catches issues that automated tests miss — broken layouts, invisible elements, z-index problems, or buttons that render but aren't clickable.

> **Note:** This step requires a tool with browser/DOM access. If you're using Claude Code CLI without a browser plugin, you can do this manually — open the app in your browser and test the flows yourself.

---

## Step 9.6: Deploy to the Web (Optional)

Want to share what you built? Deploy it for free on [Render](https://render.com):

```
Help me deploy this app to Render.com.
I need:
1. A render.yaml (Blueprint) file that defines both services
2. The backend as a Web Service (Python, using the existing Dockerfile)
3. The frontend as a Static Site (build command: npm run build, publish directory: dist)
4. Environment variables configured for production (CORS origins pointing to the Render frontend URL)
```

**Other free hosting options:**
- [Railway](https://railway.app) — similar to Render, generous free tier
- [Fly.io](https://fly.io) — deploy Docker containers directly
- [Vercel](https://vercel.com) (frontend only) + [Render](https://render.com) (backend)

**Review the diff:**
- [ ] No secrets or API keys in the config files
- [ ] CORS origins point to the production frontend URL, not localhost
- [ ] The database is configured for production (Render provides managed Postgres)

**Commit:**
```bash
git add render.yaml
git commit -m "feat: add Render deployment config"
```

> **Why optional?** Deployment is worth learning but the specifics depend on your hosting choice. The workflow is the same: prompt Claude, review the config, deploy.

---

## Step 9.7: Final Commit, Tag, and Push

```bash
# Update CLAUDE.md with Docker commands
# Add to "Key Commands" section:
# docker compose up --build
# docker compose down

git add CLAUDE.md CHANGELOG.md REQUIREMENTS.md
git commit -m "docs: final updates for Phase 9 — Docker and ship"

git push

# Tag the release
git tag v1.0
git push origin v1.0
```

**Verify before continuing:**
- [ ] `git log --oneline` shows a clean, readable history
- [ ] `v1.0` tag exists on GitHub
- [ ] All items in REQUIREMENTS.md are checked off

---

## Step 9.8: Retrospective

You're done building. Take 2 minutes to reflect on the process (P8).

**Questions to consider:**

**About CLAUDE.md:**
- What would you change if you started over?
- What conventions did you add mid-project that should have been there from the start?
- Is there anything in CLAUDE.md that Claude ignores?

**About the 1-shot test (P4):**
- Which prompts worked in one shot?
- Which required multiple tries? Why?
- Did the 1-shot success rate improve or decline as the project grew?

**About your skills:**
- Which of the 3 skills (`/add-endpoint`, `/add-component`, `/review-diff`) was most useful?
- Would you build any additional skills for your next project?
- Did the `!`command`` context injection work well?

**About the workflow:**
- Did `/clear` between phases keep token usage manageable?
- Was the commit rhythm natural or forced?
- Would you change the tracking files (CHANGELOG, REQUIREMENTS, BUGS)?

Write your answers in CHANGELOG.md or keep them for yourself. The point is to invest in the process, not just the output (P8).

---

## Tutorial Complete

**What you built:**
- A full-stack application with Python backend and React frontend
- Tests (backend + frontend) with CI running on every push
- Running in Docker with one command
- With a clean git history, meaningful commits, and phase tags

**What you learned:**
- How to establish patterns that scale (P1)
- How to run parallel agents without chaos (P2)
- How to review diffs for subtle bugs (P12)
- How to build custom skills from observed patterns (P9)
- How to set up permissions, hooks, and MCP for workflow efficiency
- How to let AI generate tests, CI config, and Docker setup (the boring stuff)
- When to push back on Claude's suggestions (P11, P13)

**What's next:**
- [What's Next section in the README](../README.md#whats-next-after-the-tutorial) — auth, Postgres, deployment
- [Appendix: What Goes Wrong](appendix-what-goes-wrong.md) — see what happens when you break the principles

---

You shipped. The workflow is yours now. Make it better (P9).
