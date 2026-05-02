---
description: "State management practices with examples for React + TypeScript. Covers usage of Zustand, SWR, boundaries, and best practices for UI and server state."
applyTo: "src/**/*.{ts,tsx,js,jsx}"
---

# State Management Instructions

_Last updated: May 2026_

## 1. State Strategy

- Use **Zustand** for global UI state (theme, sidebar, dialog…).
- Use **SWR** for server data fetching/caching within features.
- **Never sync/mirror server state** from SWR to Zustand (source of truth violation).
- Local component state: use `useState` for strictly local UI state.

## 2. Patterns & Examples

### 2.1. Zustand for UI State

```ts
// src/store/use-ui-store.ts
import { create } from "zustand";
export const useUIStore = create((set) => ({
  sidebarOpen: false,
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
}));
```

### 2.2. SWR for Server Data

```ts
// src/features/user/hooks/useUser.ts
import useSWR from "swr";
export function useUser(id: string) {
  return useSWR(`/api/users/${id}`, fetcher);
}
```

## 3. Anti-patterns

- 🚫 **Never copy/“sync” SWR data into Zustand**
  - _Counter-example:_
    ```ts
    // ❌ BAD — Don’t mirror SWR into Zustand!
    const user = useUser(id); // from SWR
    useEffect(() => {
      setUserInStore(user.data); // avoid!
    }, [user]);
    ```
- 🚫 **Do not handle business/server data in global Zustand**; keep it in feature-specific SWR hooks.
- 🚫 **No cross-feature global state unless truly global (e.g., theme, auth)**

_For more advanced state scenarios, consult tech leads or the state-management wiki._
