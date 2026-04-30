---
name: data-layer-integrator
description: "Use when connecting backend endpoints to frontend features with Axios, SWR hooks, cache revalidation, and UI loading or error handling."
---

# Data Layer Integrator

This skill implements the repository's standard pattern for API calls and server-state management.

## Use This Skill For

- Writing Axios-backed feature API functions.
- Creating feature hooks that wrap `useSWR`.
- Wiring cache revalidation after data mutations.
- Making sure consuming UI covers loading and error states.

## Workflow

1. Check whether the feature already has API and hook patterns to extend.
2. Add or update request functions in the feature `api/` layer.
3. Expose server state through feature hooks and keep SWR as the source of truth.
4. Revalidate the affected cache path after writes.
