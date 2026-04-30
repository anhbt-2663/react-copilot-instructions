---
name: dependency-graph-manager
description: "Use when reviewing or fixing cross-feature imports, circular dependencies, or misplaced shared types in the React frontend."
---

# Dependency Graph Manager

This skill protects feature encapsulation and import hygiene across the frontend codebase.

## Use This Skill For

- Detecting deep imports that bypass a feature's public API.
- Reviewing potential circular dependencies between features.
- Identifying types that should move to a shared location because they are reused broadly.

## Workflow

1. Trace imports between the affected features.
2. Replace deep imports with public exports where possible.
3. Extract truly shared contracts only when reuse is real and stable.
