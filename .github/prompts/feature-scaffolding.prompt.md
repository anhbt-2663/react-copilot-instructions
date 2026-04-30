---
description: "Use when creating a new frontend feature module with the project's feature-based React structure, API hooks, and public exports."
agent: true
inputs:
  - id: featureName
    description: "Feature module name in kebab-case (e.g. user-profile, article-editor)"
  - id: scope
    description: "Scaffold scope: structure-only | full-bootstrap"
---

# Create New Feature Module

Scaffold the feature module **`${input:featureName}`** under `src/features/${input:featureName}/`.

## Expected Structure

- `api/` — Axios request functions for this domain.
- `components/` — Feature-level Organisms only.
- `hooks/` — SWR hooks wrapping the `api/` functions.
- `store/` — Zustand slice, only if truly UI state is needed.
- `types.ts` — Zod schemas and TypeScript interfaces for the domain.
- `index.ts` — Public API surface; export only Organisms and hooks intended for external use.

## Scope Rules

- If `scope = structure-only`: create folder and file skeletons only, with minimal placeholders and no API/hook business implementation.
- If `scope = full-bootstrap`: scaffold structure and provide baseline implementation in `types.ts`, `api/`, `hooks/`, and `index.ts` following project conventions.

## Implementation Rules

- Do not add API calls or heavy logic to `src/pages/`.
- Do not import from another feature's private sub-folders.
- Prefer extending existing shared utilities in `src/api/` or `src/components/` before duplicating.
