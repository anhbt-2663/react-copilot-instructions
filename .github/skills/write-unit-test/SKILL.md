---
name: write-unit-test
description: 'Generate a colocated unit test file for a React component, custom hook, or utility function using Jest and React Testing Library. Use when asked to "write a test", "add unit tests", "create test file", "test this component/hook/util", or when a module is missing test coverage.'
---

# Write Unit Test

Generates a colocated unit test file next to the target module using **Jest** + **React Testing Library**.

## When to Use This Skill

- User asks to "write a test", "add unit tests", "create a test file"
- A component, hook, or utility is missing test coverage
- New logic was added and needs to be tested

## Prerequisites

- Target file path (e.g. `Button.tsx`, `use-user.ts`, `format-date.ts`)
- Target type: `component`, `hook`, or `util`
- Key behaviors and edge cases to cover

## Step-by-Step Workflows

### Step 1: Create test file next to source

```
src/.../
  <Name>.tsx        ← source
  <Name>.test.tsx   ← test (component)

  use-<name>.ts     ← source
  use-<name>.test.ts ← test (hook)

  <util>.ts         ← source
  <util>.test.ts    ← test (util)
```

### Step 2: Generate test — type=component

```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { <Name> } from './<Name>';

describe('<Name>', () => {
  it('renders correctly', () => {
    render(<<Name> />);
    expect(screen.getByRole('...')).toBeInTheDocument();
  });

  it('handles user interaction', () => {
    const onAction = jest.fn();
    render(<<Name> onAction={onAction} />);
    fireEvent.click(screen.getByRole('button'));
    expect(onAction).toHaveBeenCalledTimes(1);
  });
});
```

### Step 3: Generate test — type=hook

```ts
import { renderHook, act, waitFor } from '@testing-library/react';
import { use<Name> } from './use-<name>';

describe('use<Name>', () => {
  it('returns initial state', () => {
    const { result } = renderHook(() => use<Name>());
    expect(result.current.<value>).toBe(<expected>);
  });

  it('updates state on action', async () => {
    const { result } = renderHook(() => use<Name>());
    act(() => { result.current.<action>(); });
    await waitFor(() => expect(result.current.<value>).toBe(<expected>));
  });
});
```

### Step 4: Generate test — type=util

```ts
import { <utilName> } from './<util-name>';

describe('<utilName>', () => {
  it('returns correct result for valid input', () => {
    expect(<utilName>(<input>)).toBe(<expected>);
  });

  it('handles edge case', () => {
    expect(<utilName>(<edgeCase>)).toBe(<expected>);
  });

  it('handles invalid input gracefully', () => {
    expect(() => <utilName>(null)).not.toThrow();
  });
});
```

## Troubleshooting

| Issue                            | Solution                                           |
| -------------------------------- | -------------------------------------------------- |
| Test placed in `/tests` folder   | Always colocate next to source file                |
| Using `getByTestId` everywhere   | Prefer `getByRole` for accessibility-aware queries |
| Snapshot test for core component | Use behavior-based assertions instead              |
| Test depends on external API     | Mock with `jest.mock()` or `msw` to isolate        |
| Hook test missing `act()`        | Wrap state updates in `act()` to flush effects     |

## References

- [testing.instructions.md](../../../instructions/testing.instructions.md)
- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
