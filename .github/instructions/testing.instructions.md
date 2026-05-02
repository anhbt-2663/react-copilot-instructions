---
description: "Testing best practices and file conventions for React + TypeScript. Placement, coverage, tools, and anti-patterns."
applyTo: "src/**/*.{ts,tsx,js,jsx}"
---

# Testing Instructions

_Last updated: May 2026_

## 1. Placement & Coverage

- Place test files directly next to the component/module (colocate): `<Component>.test.tsx`, `<feature>.test.ts`.
- Cover all critical business-logic, major UI states, custom hooks, and edge cases.
- Prefer actual render tests over snapshot tests.

## 2. Tools

- Use **Jest** as the test runner.
- Use **React Testing Library (RTL)** for component/UI tests.

## 3. Patterns & Examples

### 3.1. Component Test Example

```tsx
// src/components/atoms/Button.test.tsx
import { render, screen } from "@testing-library/react";
import { Button } from "./Button";

test("renders with correct children", () => {
  render(<Button>OK</Button>);
  expect(screen.getByText("OK")).toBeInTheDocument();
});
```

### 3.2. Hook Test Example

```ts
// src/features/user/hooks/useUser.test.ts
import { renderHook } from "@testing-library/react";
import { useUser } from "./useUser";

test("returns user data", async () => {
  const { result } = renderHook(() => useUser("id"));
  // ...mock SWR/fetch...
});
```

## 4. Anti-patterns

- 🚫 **Do not skip tests for new features or critical logic.**
- 🚫 **Do not place tests in a centralized /tests or /**tests** folder,** unless for integration or e2e tests.
- 🚫 **No snapshot-only tests for core components** (prefer behavior-based tests).

_For further testing strategy (e2e, coverage targets), see the testing wiki or ask a senior engineer._
