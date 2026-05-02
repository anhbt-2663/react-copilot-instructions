---
description: "Architecture and Atomic Design Principles with practical examples for React + TypeScript frontend: folder structure, component roles, anti-patterns."
applyTo: "src/**/*.{ts,tsx,js,jsx}"
---

# Architecture & Atomic Design Instructions

_Last updated: May 2026_

## 1. Project Structure

- All code in `src/`.
- Key folders:
  - `src/components/atoms/`, `molecules/`: Stateless, presentational UI only (**no business logic**).
  - `src/features/<feature>/`: Self-contained business logic; owns `api/`, `components/` (organisms), `hooks/`, `store/`, `types.ts`, `index.ts` (public API).
  - `src/constants/`: Shared constants, always imported from `index.ts`.
  - `src/layouts/`, `src/pages/`: Routing, shell layouts.
  - `src/store/`: Zustand global UI state.
  - `src/utils/`, `src/types/`: Helpers & types.

**Example tree:**

```
src/
  features/
    user/
      api/
      components/
      hooks/
      types.ts
      index.ts
  components/
    atoms/
      Button.tsx
    molecules/
      ModalHeader.tsx
  constants/
    api.ts
    index.ts
  utils/
  types/
```

---

## 2. Atomic Design Examples

### 2.1. Atoms

- **Do:** Pure UI, no business logic.

  ```tsx
  // src/components/atoms/Button.tsx
  export function Button({
    children,
    ...props
  }: React.ButtonHTMLAttributes<HTMLButtonElement>) {
    return <button {...props}>{children}</button>;
  }
  ```

- **Don’t:** No store/API/data logic!

  ```tsx
  // ❌ BAD: Atoms should not access store or make API calls
  import { useAuthStore } from "src/store/use-auth-store";
  export function UserAvatar() {
    const user = useAuthStore();
    return <img src={user.avatar} />;
  }
  ```

### 2.2. Molecules

- **Do:** Compose atoms, still stateless/presentational.

  ```tsx
  // src/components/molecules/FormGroup.tsx
  import { Label } from "../atoms/Label";
  import { Input } from "../atoms/Input";
  export function FormGroup({ label, ...inputProps }) {
    return (
      <div>
        <Label>{label}</Label>
        <Input {...inputProps} />
      </div>
    );
  }
  ```

---

## 3. Feature Boundaries (with Examples)

- **Do:** Only import another feature via its `index.ts`.

  ```ts
  // Good: Import via feature's public API
  import { login } from "src/features/auth";
  ```

- **Don’t:** Never deep import another feature’s private files.

  ```ts
  // ❌ BAD: Do not import private file across features
  import { login } from "src/features/auth/api/login";
  ```

- **Keep all business logic and APIs in feature folders:**

  ```ts
  // src/features/user/store/useUserStore.ts (OK)
  ```

---

## 4. Anti-patterns (with Counter-Examples)

- 🚫 **No business logic, store, or API in atoms/molecules.**
  - _Counter-example:_

    ```tsx
    // ❌ BAD in atoms/molecules
    import useSWR from "swr";
    export function FancyBadge() {
      const { data } = useSWR("/api/user");
      return <span>{data.name}</span>;
    }
    ```

- 🚫 **No deep cross-feature import.**
- 🚫 **No duplicate helpers/types: use `utils/` or `types/`.**
- 🚫 **Don't nest >3 levels deep without strong reason.**
- 🚫 **Tests/styles should be next to their component.**

---

_For more advanced diagrams and rationale, see the architecture wiki or contact the technical lead!_
