---
name: performance-auditor
description: "Use when auditing React rendering performance, state placement, expensive computations, or code-splitting opportunities in the frontend."
---

# Performance Auditor

This skill reviews React code for avoidable re-renders, misplaced state, and overly heavy initial bundles.

## Use This Skill For

- Checking whether state should stay local, move to Zustand, or remain in SWR.
- Auditing expensive computations and render churn.
- Evaluating whether code-splitting or lazy loading is justified.

## Constraints

- Do not add memoization by default without evidence from the surrounding code.
- Prefer minimal, architecture-consistent optimizations over speculative tuning.
