---
name: component-scaffolder
description: "Use when scaffolding a new atom, molecule, or feature module that must follow the repository's React architecture and file layout."
---

# Component Scaffolder

This skill standardizes how reusable UI units and feature modules are created in this repository.

## Use This Skill For

- Creating a new Atom or Molecule with matching component and export files.
- Scaffolding a new feature module under `src/features/`.
- Generating baseline React 19 functional component structure with strict TypeScript interfaces.

## Workflow

1. Inspect existing components or features to avoid duplicate patterns.
2. Create the minimal file set required by the target layer.
3. Keep public exports explicit and aligned with the repository's feature boundaries.

## Constraints

- Do not add business logic to Atoms or Molecules.
- Do not create unused folders just to satisfy a template.
