# React Frontend Workspace Instructions

This workspace is a React 19 + TypeScript frontend. Prefer existing project patterns over introducing new abstractions, and keep changes minimal and aligned with the owning feature.

## Core Stack

- Use functional components and hooks only.
- Keep TypeScript strict. Do not introduce `any`. Use `interface` for component props and shared data models.
- Use `useSWR` for server state, `axios` from `src/api/` for HTTP, and `zustand` only for UI or workflow state.
- Use Tailwind CSS for most styling. Use SCSS only when Tailwind is not a good fit for complex styling logic.

## Architecture Rules

- `src/components/atoms` and `src/components/molecules` are presentational only. No API calls, no store access, and no feature business logic.
- `src/features/<feature>` owns its `api/`, `hooks/`, `components/`, `store/`, `types.ts`, and `index.ts`.
- Import across features only through the target feature's `index.ts` public API.
- `src/pages` and `src/layouts` assemble features and application shell code. Avoid direct API calls or heavy business logic there.

## Data Rules

- Wrap SWR usage in feature-level hooks.
- Validate external API responses with Zod at the feature boundary when parsing transport data.
- Do not mirror SWR data into Zustand.
- After POST, PUT, PATCH, or DELETE flows, trigger the relevant SWR `mutate()` path.

## Delivery Rules

- Check for an existing Atom or Molecule before creating a new component.
- Prefer extending an existing feature module over creating parallel patterns.
- Keep responses concise and focused on the required diff.

## Scope Guard

- This instruction set applies only to React + TypeScript frontend work in this repository.
- Do not apply these rules to backend, infrastructure, database, CI/CD, or other non-frontend tasks.
- If a task is outside React frontend scope, switch to general project reasoning instead of forcing feature-based React patterns.
