---
name: create-page
description: 'Scaffold a page component inside src/pages/ that wires routing and composes feature-level UI. Use when asked to "create a page", "add a new page", "scaffold a route", "add a view", or when connecting a feature to the router.'
---

# Create Page Component

Generates a thin page component inside `src/pages/<Name>Page/`. Pages are routing entry points that delegate all logic to feature modules.

## When to Use This Skill

- User asks to "create a page", "add a new page", "scaffold a route"
- Connecting a feature to the application router
- Need a route-level entry point that composes feature UI

## Prerequisites

- Page name in `PascalCase` (e.g. `UserDetail`, `OrderList`)
- Related feature name in `kebab-case` (e.g. `user`, `order`)
- Route path (e.g. `/users/:id`)

## Step-by-Step Workflows

### Step 1: Create folder structure

```
src/pages/
  <Name>Page/
    <Name>Page.tsx
    <Name>Page.test.tsx
    index.ts
```

### Step 2: Generate `<Name>Page.tsx`

```tsx
import { useParams } from 'react-router-dom';
import { <Name>Form } from 'src/features/<feature>';

export function <Name>Page() {
  const { id } = useParams();

  return (
    <main>
      <h1><Name></h1>
      <<Name>Form id={id} />
    </main>
  );
}
```

### Step 3: Generate `<Name>Page.test.tsx`

```tsx
import { render, screen } from '@testing-library/react';
import { MemoryRouter } from 'react-router-dom';
import { <Name>Page } from './<Name>Page';

describe('<Name>Page', () => {
  it('renders page correctly', () => {
    render(
      <MemoryRouter>
        <<Name>Page />
      </MemoryRouter>
    );
    expect(screen.getByRole('main')).toBeInTheDocument();
  });
});
```

### Step 4: Generate `index.ts`

```ts
export { <Name>Page } from './<Name>Page';
```

## Troubleshooting

| Issue                            | Solution                                                      |
| -------------------------------- | ------------------------------------------------------------- |
| Business logic inside page       | Move to feature hooks or components                           |
| Importing feature internal files | Only import from feature's `index.ts`                         |
| Page growing too large           | Extract UI into feature-level organism components             |
| Missing MemoryRouter in tests    | Wrap with `MemoryRouter` when using `useParams`/`useNavigate` |

## References

- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
- [testing.instructions.md](../../../instructions/testing.instructions.md)
