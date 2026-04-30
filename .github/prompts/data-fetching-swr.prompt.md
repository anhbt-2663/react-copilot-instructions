---
description: "Use when implementing or refactoring an SWR-based data layer with Axios, including fetch hooks, loading states, and cache revalidation."
agent: true
inputs:
  - id: featureName
    description: "Target feature module in kebab-case (e.g. article-list, user-profile)"
  - id: hookBaseName
    description: "Hook base name in PascalCase (e.g. Articles, UserProfile)"
  - id: endpoint
    description: "API endpoint path to integrate (e.g. /api/articles, /api/users/:id)"
---

# Implement SWR Data Layer

Wire up server-state management for **`${input:featureName}`** using SWR and Axios against endpoint **`${input:endpoint}`**.

## Requirements

- Add Axios request functions inside `src/features/${input:featureName}/api/`.
- Expose a `use${input:hookBaseName}` hook (or clear variant such as `use${input:hookBaseName}List`) inside `src/features/${input:featureName}/hooks/` wrapping `useSWR`.
- Use the shared Axios instance and fetcher from `src/api/` instead of bare `fetch` or a new Axios instance.
- After any POST, PUT, PATCH, or DELETE, call `mutate()` with the matching SWR cache key.
- Ensure consuming Organism handles `isLoading` and `error` states in the UI.

## Constraints

- Do not store the SWR response in Zustand.
- Do not call `useSWR` directly in Pages or molecules.
