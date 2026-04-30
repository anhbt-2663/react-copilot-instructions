---
applyTo: "src/**/*.{ts,tsx}"
description: "Use when creating or refactoring React components, feature folders, layouts, pages, or imports between frontend modules."
---

# Architecture Rules

## Feature Modules

- Each directory in `src/features/` must be self-contained.
- Internal folders such as `api`, `hooks`, `components`, and `store` are private to the feature.
- Expose cross-feature usage through the feature's `index.ts` only.
- Do not import private files from another feature.

## Component Layers

- Atoms are the smallest reusable UI units. They must stay presentational and independent.
- Molecules may compose Atoms, but they still remain UI-only and side-effect free.
- Feature-level Organisms may compose Atoms, Molecules, and feature hooks or feature API helpers.

## Data Flow

- Pages compose feature-level UI and routing concerns.
- Layouts own persistent shells and route-level wrappers.
- Data flows down through props, and events flow up through callbacks.
