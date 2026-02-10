# Requirements

Features organized by phase. Check them off as you complete each one.

## Phase 1 — Foundation
- [ ] Backend project scaffold (FastAPI + SQLite)
- [ ] Health endpoint (`GET /api/health`)
- [ ] Frontend project scaffold (React + Vite)
- [ ] Frontend routing setup with placeholder pages
- [ ] `/add-endpoint` custom skill created

## Phase 2 — Backend Core
- [ ] Database models for main resource
- [ ] Database models for secondary resource (categories/analytics/statuses)
- [ ] CRUD endpoints: Create
- [ ] CRUD endpoints: Read (list + detail)
- [ ] CRUD endpoints: Update
- [ ] CRUD endpoints: Delete
- [ ] Input validation with Pydantic schemas
- [ ] CLAUDE.md updated with backend conventions

## Phase 3 — Frontend Core
- [ ] Main list/table page
- [ ] Add/edit form with validation
- [ ] API client module (fetch wrapper)
- [ ] Delete with confirmation
- [ ] `/add-component` custom skill created

## Phase 4 — Integration
- [ ] CORS configured (NOT wildcard `*`)
- [ ] Frontend connected to backend API
- [ ] Full create → list → delete flow working in browser
- [ ] Filter/search functionality (1-shot health check)
- [ ] Bugs logged in BUGS.md

## Phase 5 — Parallel Work
- [ ] Feature A branch (search/filter)
- [ ] Feature B branch (export/report)
- [ ] Both features built with parallel Claude instances
- [ ] Branches merged cleanly
- [ ] `.mcp.json` for GitHub MCP (optional)

## Phase 6 — Hardening
- [ ] API endpoint tests (pytest)
- [ ] Security review completed
- [ ] `/review-diff` custom skill created
- [ ] Lint hook set up (PostToolUse)
- [ ] Diff review exercise completed

## Phase 7 — Docker & Ship
- [ ] Backend Dockerfile
- [ ] Frontend Dockerfile
- [ ] docker-compose.yml
- [ ] App runs with `docker compose up --build`
- [ ] App accessible in browser
- [ ] Tagged `v1.0`
