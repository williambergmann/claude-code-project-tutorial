---
name: add-endpoint
description: Scaffold a new FastAPI endpoint following project conventions
disable-model-invocation: true
argument-hint: "[resource-name]"
allowed-tools: Read, Edit, Write
---

Create a new FastAPI endpoint for $ARGUMENTS following the existing patterns in the codebase.

Current project structure:
!`find backend/app -type f -name "*.py" | head -20`

Current routers:
!`ls backend/app/routers/ 2>/dev/null`

Requirements:
- Follow the same file organization as existing endpoints
- Create a Pydantic schema file in `schemas/` for request/response models
- Create a router file in `routers/` with CRUD operations
- Add input validation on all request bodies
- Register the router in `main.py`
- Use proper HTTP status codes (201 for created, 404 for not found)
- Do NOT create tests (we handle those separately)
- Do NOT add comments to obvious code
