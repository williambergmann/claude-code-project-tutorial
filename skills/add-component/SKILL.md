---
name: add-component
description: Scaffold a React component following project conventions
disable-model-invocation: true
argument-hint: "[ComponentName]"
allowed-tools: Read, Edit, Write
---

Create a new React component called $ARGUMENTS following the existing patterns.

Current components:
!`find frontend/src -type f \( -name "*.jsx" -o -name "*.tsx" \) | head -20`

Current pages:
!`ls frontend/src/pages/ 2>/dev/null`

Requirements:
- Match the file naming and export pattern of existing components
- Place in `components/` for reusable pieces, `pages/` for route-level components
- Include prop types or TypeScript interfaces if the project uses them
- Follow the same styling approach used by existing components
- Import and use the API client from `api/` if the component needs data
- Do NOT add comments to obvious code
- Do NOT create test files (we handle those separately)
