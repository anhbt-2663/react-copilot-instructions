---
description: "Use when adding or updating Zod schemas and TypeScript types for API responses at a frontend feature boundary."
agent: true
inputs:
  - id: featureName
    description: "Feature module to update in kebab-case (e.g. user-profile, article-editor)"
  - id: apiResponseShape
    description: "Paste or describe the raw API response shape to validate (e.g. { id: number, title: string })"
---

# Sync API Types With Zod

Add or update Zod-backed type definitions for **`${input:featureName}`** based on the following API shape:

```
${input:apiResponseShape}
```

## Instructions

- Define the Zod schema in `src/features/${input:featureName}/types.ts`.
- Infer the TypeScript type from the schema using `z.infer<typeof schema>` so compile-time types stay in sync with runtime validation.
- Validate API payloads at the feature boundary — inside the `api/` or `hooks/` layer — before they reach Organisms or Atoms.
- Mark genuinely optional fields with `.optional()` on the schema rather than `?` on a hand-written interface.
- Reuse the inferred type across `api/`, `hooks/`, and any component props that depend on the response shape.
