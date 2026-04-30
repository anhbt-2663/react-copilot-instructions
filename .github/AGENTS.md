# Agent Operating Guide

Use `.github/copilot-instructions.md` as the always-on baseline for this repository.

## Scope

This customization pack is for React + TypeScript frontend code only. Apply it to `src/` frontend implementation tasks, especially components, hooks, feature modules, pages, and styling.

For non-React tasks (backend services, infrastructure, CI/CD, database, non-frontend tooling), do not force these React-specific patterns.

When the task matches a scoped area, load the matching instruction file from `.github/instructions/`:

- `architecture.instructions.md` for component boundaries, feature structure, and cross-feature imports.
- `state-management.instructions.md` for SWR, Axios, Zustand, and mutation flows.
- `styling.instructions.md` for Tailwind-first styling and SCSS exceptions.
- `typescript.instructions.md` for strict TypeScript, interfaces, and Zod-backed API validation.

Use the workspace prompts in `.github/prompts/` for focused one-shot tasks such as feature scaffolding, SWR data-layer setup, senior review, atomic UI work, and Zod type synchronization.

Use the workspace skills in `.github/skills/` for repeatable multi-step workflows such as scaffolding, data-layer integration, dependency graph review, performance auditing, and UI consistency review.

If guidance conflicts, prefer the most specific instruction that applies to the file or task. Only treat files with supported customization names and layouts as active Copilot customizations.
