---
description: "React frontend code review best practices: what to look for, anti-patterns, feedback style."
applyTo: "src/**/*.{ts,tsx,js,jsx}"
---

# Code Review Instructions

_Last updated: May 2026_

## 1. Review Goals

- Ensure code is correct, maintainable, and matches architectures.instructions.md & copilot-instructions.md.
- Confirm tests and documentation are present for new/changed features.

## 2. Checklist

- [ ] Conforms to project folder structure and atomic design rules.
- [ ] No business logic, store, or API in atomic components.
- [ ] Feature boundaries respected (no deep cross-feature import).
- [ ] State managed appropriately—no SWR-Zustand sync.
- [ ] Changes are concise; no unrelated "drive-by" cleanup.
- [ ] Well-written, colocated tests present for all new logic/UI.
- [ ] Public APIs and types are documented.
- [ ] No secrets or sensitive data in source.
- [ ] Performance/scalability considered.

## 3. Feedback Style

- Use clear, concise comments. Example:

  > "Please move this logic into the feature folder per architecture instructions."
  > "Consider splitting this component: it's too broad for an atom."

- Approve ONLY if all checklist items pass; otherwise, request changes and specify which rule was violated.

## 4. Anti-patterns

- 🚫 Approving code with architectural or security violations.
- 🚫 Large, unfocused PRs (request split/refocus).
- 🚫 Passing without required tests.

_PRs violating these rules should be considered blockers until fixed._
