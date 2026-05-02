---
name: create-component
description: 'Scaffold a stateless, presentational UI component following atomic design principles. Use when asked to "create a component", "add an atom", "create a molecule", "make a UI component", or when building reusable interface elements with no business logic.'
---

# Create Atomic UI Component

Generates a stateless UI component inside `src/components/atoms/` or `src/components/molecules/` depending on the type specified.

## When to Use This Skill

- User asks to "create a component", "add an atom", "create a molecule"
- Building a reusable, stateless UI element (Button, Input, Label, FormGroup...)
- Need a presentational component with no business logic or data fetching

## Prerequisites

- Component name in `PascalCase` (e.g. `Button`, `FormGroup`)
- Component type: `atom` or `molecule`
- For molecules: know which atoms it will compose

## Step-by-Step Workflows

### Step 1: Determine folder by type

```
# type=atom
src/components/atoms/<Name>/
  <Name>.tsx
  <Name>.test.tsx
  index.ts

# type=molecule
src/components/molecules/<Name>/
  <Name>.tsx
  <Name>.test.tsx
  index.ts
```

### Step 2: Generate component file

**type=atom**

```tsx
interface <Name>Props extends React.HTMLAttributes<HTMLElement> {
  children?: React.ReactNode;
}

export function <Name>({ children, ...props }: <Name>Props) {
  return <element {...props}>{children}</element>;
}
```

**type=molecule**

```tsx
import { <Atom> } from 'src/components/atoms/<Atom>';

interface <Name>Props {
  // define props here
}

export function <Name>({ ...props }: <Name>Props) {
  return (
    <div>
      <Atom />
    </div>
  );
}
```

### Step 3: Generate test file

```tsx
import { render, screen } from '@testing-library/react';
import { <Name> } from './<Name>';

describe('<Name>', () => {
  it('renders correctly', () => {
    render(<<Name> />);
    expect(screen.getByRole('...')).toBeInTheDocument();
  });
});
```

### Step 4: Generate index.ts

```ts
export { <Name> } from './<Name>';
```

## Troubleshooting

| Issue                                  | Solution                                        |
| -------------------------------------- | ----------------------------------------------- |
| Tempted to add `useEffect` or `useSWR` | Move logic to a feature-level hook instead      |
| Molecule imports from `features/`      | Only import atoms from `src/components/atoms/`  |
| Component grows too complex            | Consider promoting to organism inside a feature |

## References

- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
- [testing.instructions.md](../../../instructions/testing.instructions.md)
