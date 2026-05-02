---
name: agents
description: "Global agent instructions for this repository. Read this file first before starting any task."
---

# Project Agent Instructions

## General Rules

- Never use `any` TypeScript type.
- Never import across feature boundaries — use `index.ts` public API only.
- Always colocate test files next to source files.
- Always use `ApiService` class — never call `axios` directly in features.
- Never store server/fetched data in Zustand — SWR is source of truth.
- Always validate API responses with Zod in SWR hooks.
- Atoms and molecules are stateless UI only — no business logic, no data fetching.

## Load Instructions By Task

> ⚠️ Only read the instruction file relevant to your current task.
> Do NOT load all instruction files upfront — load on demand to minimize token usage.

| Task                                                   | Read this file                                                                       |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| Creating folders, features, components, pages, layouts | [architectures.instructions.md](../instructions/architectures.instructions.md)       |
| Working with SWR hooks, Zustand stores, data flow      | [state-management.instructions.md](../instructions/state-management.instructions.md) |
| Writing or updating any test file                      | [testing.instructions.md](../instructions/testing.instructions.md)                   |
| Optimizing renders, lazy loading, memoization, bundle  | [performance.instructions.md](../instructions/performance.instructions.md)           |
| Reviewing code or opening a PR                         | [code-review.instructions.md](../instructions/code-review.instructions.md)           |

## Use Skills For Repetitive Tasks

> ⚠️ Do NOT reinvent workflows — always check available skills before writing boilerplate.

| Skill                  | When to use                                   |
| ---------------------- | --------------------------------------------- |
| `setup-project`        | Init and configure a new project from scratch |
| `create-feature`       | Scaffold a complete feature module            |
| `create-component`     | Scaffold an atom or molecule component        |
| `create-api-hook`      | Create a SWR + Zod data-fetching hook         |
| `create-zustand-store` | Create a UI state Zustand store               |
| `create-page`          | Scaffold a page component with routing        |
| `create-layout`        | Scaffold a layout shell wrapper               |
| `write-unit-test`      | Generate a Jest + RTL colocated test file     |

## Before Finishing Any Task

Run all checks — do not ask for approval to run these:

```bash
npm run lint    # ESLint must pass with zero errors
npm run test    # All tests must pass
```
