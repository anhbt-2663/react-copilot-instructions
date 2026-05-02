---
description: "Performance patterns and optimization rules for React + TypeScript frontend. Rendering, bundle size, and anti-pattern avoidance."
applyTo: "src/**/*.{ts,tsx,js,jsx}"
---

# Performance Instructions

_Last updated: May 2026_

## 1. Rendering Best Practices

- Use `React.memo` for components with expensive renders or pure functional props.
- Use `useMemo`, `useCallback` for expensive calculations inside functional components.
- Avoid unnecessary re-renders by lifting state or using selectors.

## 2. Code Splitting & Optimization

- Use `React.lazy` and `Suspense` for large or route-level components.
- Keep bundle size small by importing only from module entry points (never deep import large libs).

## 3. Patterns & Examples

### 3.1. Memoization

```tsx
const MemoizedList = React.memo(function List({ items }) {
  // will only rerender if items array reference changes
  return (
    <ul>
      {items.map((i) => (
        <li key={i}>{i}</li>
      ))}
    </ul>
  );
});
```

### 3.2. useMemo Example

```tsx
const expensiveResult = useMemo(() => computeExpensive(data), [data]);
```

## 4. Anti-patterns

- 🚫 No large inline objects/arrays in props—will break memoization.
- 🚫 No unnecessary state at the root—keep local when possible.
- 🚫 Don’t fetch or process heavy data in render body.

_For bundle analysis or advanced optimization, see the team’s performance guide or contact your frontend lead._
