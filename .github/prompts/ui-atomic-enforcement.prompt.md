---
description: "Use when creating or refactoring atoms, molecules, or other presentational React UI to match the project's atomic design rules."
agent: true
inputs:
  - id: componentName
    description: "PascalCase name of the component to create or refactor (e.g. AvatarCard, TagBadge)"
  - id: layer
    description: "Target atomic layer: atom | molecule"
  - id: intent
    description: "What this component does or renders (e.g. displays a user avatar with fallback initials)"
---

# Atomic UI — `${input:componentName}`

Create or refactor **`${input:componentName}`** as a **${input:layer}** inside `src/components/${input:layer}s/${input:componentName}/`.

**Intent:** ${input:intent}

## Rules

- Scan `src/components/atoms/` and `src/components/molecules/` first — reuse or extend an existing component if one already covers this need.
- Keep **Atoms** dependency-free and purely presentational.
- Keep **Molecules** limited to composing two or more Atoms; no side effects.
- Do not add API calls, SWR hooks, Zustand access, or feature business logic.
- Use Tailwind utilities for all standard styling; SCSS only for animations or pseudo-element logic that Tailwind cannot express.
- Define a strict `Props` interface; no `any`.

## Deliverables

- `${input:componentName}.tsx` — component implementation.
- `index.ts` — re-export for clean imports.
- SCSS file only if needed.
