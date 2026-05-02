---
name: create-api-hook
description: 'Generate a type-safe SWR data-fetching hook with Zod schema validation for a feature resource. Use when asked to "create an API hook", "add data fetching", "fetch resource from API", "create a SWR hook", or when a feature needs to consume a REST endpoint.'
---

# Create SWR API Hook

Generates a SWR hook with Zod validation inside `src/features/<feature>/hooks/`.

## When to Use This Skill

- User asks to "create an API hook", "add data fetching", "fetch from API"
- A feature needs to consume a REST API endpoint
- Need type-safe server data with automatic revalidation

## Prerequisites

- Feature name in `kebab-case` (e.g. `user`, `order`)
- Resource name in `PascalCase` (e.g. `User`, `Order`)
- API endpoint (e.g. `/api/users`)

## Step-by-Step Workflows

### Step 1: Create hook files

```
src/features/<feature>/hooks/
  use-<resource>.ts
  use-<resource>.test.ts
```

### Step 2: Generate `use-<resource>.ts`

```ts
import useSWR from 'swr';
import { z } from 'zod';
import { fetcher } from 'src/api';

const <Resource>Schema = z.object({
  id: z.string(),
  // add fields here
});

export type <Resource> = z.infer<typeof <Resource>Schema>;

export function use<Resource>(id: string) {
  const { data, error, isLoading, mutate } = useSWR(
    id ? `/api/<resource>s/${id}` : null,
    fetcher
  );
  return {
    data: data ? <Resource>Schema.parse(data) : undefined,
    error,
    isLoading,
    mutate,
  };
}

export function use<Resource>List() {
  const { data, error, isLoading, mutate } = useSWR('/api/<resource>s', fetcher);
  return {
    data: data ? z.array(<Resource>Schema).parse(data) : [],
    error,
    isLoading,
    mutate,
  };
}
```

### Step 3: Generate `use-<resource>.test.ts`

```ts
import { renderHook, waitFor } from '@testing-library/react';
import { use<Resource> } from './use-<resource>';

describe('use<Resource>', () => {
  it('returns data when fetch succeeds', async () => {
    const { result } = renderHook(() => use<Resource>('test-id'));
    await waitFor(() => expect(result.current.isLoading).toBe(false));
  });

  it('returns undefined when id is empty', () => {
    const { result } = renderHook(() => use<Resource>(''));
    expect(result.current.data).toBeUndefined();
  });
});
```

## Troubleshooting

| Issue                              | Solution                                           |
| ---------------------------------- | -------------------------------------------------- |
| Zod parse error                    | Verify schema matches actual API response shape    |
| Premature fetch with empty id      | Use `null` key: `id ? url : null`                  |
| Data not refreshing after mutation | Ensure `mutate()` is called after POST/PUT/DELETE  |
| Storing result in Zustand          | Never — use SWR as source of truth for server data |

## References

- [state-management.instructions.md](../../../instructions/state-management.instructions.md)
- [testing.instructions.md](../../../instructions/testing.instructions.md)
