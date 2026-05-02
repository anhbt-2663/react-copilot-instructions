---
name: create-feature
description: 'Scaffold a complete, self-contained React feature module with api, hooks, components, store, types and public index. Use when asked to "create a feature", "add a new feature", "scaffold feature folder", "start a new module", or when building a new business domain.'
---

# Create Feature Module

Scaffolds a complete feature module inside `src/features/<name>/` following project architecture conventions.

## When to Use This Skill

- User asks to "create a feature", "add a new feature", "scaffold a feature"
- Starting a new business domain (e.g. user management, order tracking)
- Need a consistent, convention-compliant feature structure from scratch

## Prerequisites

- Feature name in `kebab-case` (e.g. `user-management`, `order-tracking`)
- Resource name in `PascalCase` (e.g. `User`, `Order`)
- API endpoint for the resource (e.g. `/api/users`)

## Step-by-Step Workflows

### Step 1: Create folder structure

```
src/features/<name>/
  api/
    <name>-api.ts
  components/
    <Name>Form.tsx
    <Name>Form.test.tsx
  hooks/
    use-<name>.ts
    use-<name>.test.ts
  store/
    use-<name>-store.ts
  types.ts
  index.ts
```

### Step 2: Generate `types.ts`

```ts
export interface <Name> {
  id: string;
}

export interface Create<Name>Dto {
  // define fields here
}
```

### Step 3: Generate `api/<name>-api.ts`

```ts
import { ApiService } from 'src/api';
import type { <Name>, Create<Name>Dto } from '../types';

const service = new ApiService(import.meta.env.VITE_API_BASE_URL);

export const <name>Api = {
  getById: (id: string) => service.get<Name>(`/<name>s/${id}`),
  getAll: () => service.get<<Name>[]>(`/<name>s`),
  create: (dto: Create<Name>Dto) => service.post<Name, Create<Name>Dto>(`/<name>s`, dto),
  update: (id: string, dto: Partial<Create<Name>Dto>) =>
    service.patch<Name, Partial<Create<Name>Dto>>(`/<name>s/${id}`, dto),
  remove: (id: string) => service.delete<void>(`/<name>s/${id}`),
};
```

### Step 4: Generate `hooks/use-<name>.ts`

```ts
import useSWR from 'swr';
import { z } from 'zod';
import { fetcher } from 'src/api';

const <Name>Schema = z.object({
  id: z.string(),
});

export type <Name> = z.infer<typeof <Name>Schema>;

export function use<Name>(id: string) {
  const { data, error, isLoading, mutate } = useSWR(
    id ? `/<name>s/${id}` : null,
    fetcher
  );
  return {
    data: data ? <Name>Schema.parse(data) : undefined,
    error,
    isLoading,
    mutate,
  };
}
```

### Step 5: Generate `store/use-<name>-store.ts`

```ts
import { create } from 'zustand';

interface <Name>UIState {
  selectedId: string | null;
  setSelectedId: (id: string | null) => void;
  reset: () => void;
}

const initialState = { selectedId: null };

export const use<Name>Store = create<<Name>UIState>(set => ({
  ...initialState,
  setSelectedId: (id) => set({ selectedId: id }),
  reset: () => set(initialState),
}));
```

### Step 6: Generate `index.ts`

```ts
export type { <Name>, Create<Name>Dto } from './types';
export { use<Name> } from './hooks/use-<name>';
export { <Name>Form } from './components/<Name>Form';
```

## Troubleshooting

| Issue                               | Solution                                           |
| ----------------------------------- | -------------------------------------------------- |
| Cross-feature import error          | Only import from another feature's `index.ts`      |
| Business logic leaking to component | Move to hooks or api layer                         |
| Missing Zod validation              | Always validate API response in hook               |
| Store holding server data           | Use SWR for server data, Zustand for UI state only |

## References

- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
- [state-management.instructions.md](../../../instructions/state-management.instructions.md)
