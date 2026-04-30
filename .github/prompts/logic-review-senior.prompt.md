---
description: "Use when reviewing React frontend code for architecture drift, performance issues, deep imports, and misuse of Zustand or SWR."
agent: true
---

# Senior Frontend Review

Review the code in the active editor or in the files listed below. Apply the project's architectural rules as the evaluation baseline.

## Review Checklist

1. **Separation of concerns** — Is business logic leaking into Atoms, Molecules, or Pages?
2. **Encapsulation** — Are there deep feature imports that bypass `index.ts`?
3. **State ownership** — Is state placed at the right level: local → Zustand → SWR?
4. **SWR discipline** — Is API data being mirrored into Zustand without justification?
5. **Effect hygiene** — Are `useEffect` calls justified, or can they be replaced with SWR or derived values?
6. **Type safety** — Are there `any` casts, missing `unknown` guards, or unvalidated API responses?

## Output Format

- Group findings by checklist item.
- Order within each group by severity: blocking → warning → suggestion.
- For each finding, include an actionable file reference in `path:line` format.
- For each finding, provide a minimal corrected code snippet or a concrete action to take.
