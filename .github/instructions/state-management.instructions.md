---
applyTo: "src/**/*.{ts,tsx}"
description: "Use when fetching data, writing feature hooks, handling mutations, or deciding between SWR and Zustand in the frontend."
---

# State Management Rules

## Server State

- Treat SWR as the source of truth for API-backed data.
- Access server state through feature hooks instead of calling `useSWR` inline across pages and components.
- Do not copy SWR response data into Zustand stores.

## Client State

- Use Zustand only for UI state or workflow state such as modals, sidebars, stepper progress, and shared filters.
- Use selectors when reading from Zustand stores to avoid broad subscriptions.

## Mutations

- Perform HTTP mutations through the Axios client configured in `src/api/` or feature API helpers built on top of it.
- Revalidate the affected SWR cache with `mutate()` after create, update, or delete operations.
