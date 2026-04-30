---
applyTo: "src/**/*.{ts,tsx}"
description: "Use when defining TypeScript types, component props, API contracts, or validating external data in React features."
---

# TypeScript Rules

## Type Safety

- Keep strict typing intact and avoid `any`.
- Use `unknown` at external boundaries when runtime data is not yet validated.
- Define a `Props` interface for component props and explicit interfaces for shared data models.

## API Contracts

- Keep API-facing types in `src/features/<feature>/types.ts`.
- Use Zod schemas to validate external API payloads at the feature boundary.
- After validation, map parsed transport data into the interface-based shapes used by the rest of the feature when that improves readability or stability.

## Naming

- Use descriptive type names that match the domain.
- Prefer readonly data from SWR and avoid mutating cached response objects directly.
