---
description: "Best practices, folder conventions, and coding standards for React 19 + TypeScript frontend development in this repository. Optimized for Copilot and consistent team collaboration."
applyTo: "**/*.tsx, **/*.ts, **/*.jsx, **/*.js, **/*.css"
---

# React Frontend Workspace Instructions

_Last updated: May 2026 — applies to React 19 frontend code only._

This document gathers key patterns, code conventions, directory structure, and rules for developing, reviewing, and onboarding code in this repository. For detailed architectural rationale and anti-patterns, see [instructions/architectures.instructions.md](instructions/architectures.instructions.md).

---

## 1. Core Stack & Coding Conventions

- **Functional components & React hooks only** (no class components).
- **TypeScript:** Always use strict mode.
  - Do not use `any`. Define component props/models with `interface`.
    - _Example:_
      ```tsx
      interface UserCardProps {
        name: string;
        age?: number;
      }
      ```
- **Data fetching:** Use `useSWR` for server state; always wrap it in feature-level hooks.
  - _Example:_
    ```tsx
    import useSWR from "swr";
    const { data } = useSWR("/api/profile", fetcher);
    ```
- **HTTP requests:** Always use `axios` from `src/api/`.
- **State management:** Use Zustand for UI/workflow/global state only (not server data).
- **Styling:** Use Tailwind CSS for most UI; SCSS only for advanced/edge-case styling.
  - _Example:_
    ```tsx
    <button className="px-4 py-2 bg-primary text-white rounded">Submit</button>
    ```

---

## 2. Folder & Architecture Rules

- **Use the `src/` directory** as the root for all source code.
- **Top-level folders:**
  - `public/` — Static assets (favicon, robots.txt, images, fonts, ...)
  - `src/components/` — Atomic Design: `atoms/` and `molecules/` only for stateless UI components (no business logic)
  - `src/features/` — Business-domain (feature-based) folders; each feature manages its own logic, API, components (organisms if required), hooks, store, types, and public exports via `index.ts`
  - `src/constants/` — Application-wide constants & enums (always group and import via `index.ts`)
  - `src/pages/` — Routing entry points (React Router/Next.js/other)
  - `src/layouts/` — Layout wrappers/boilerplate for application shells
  - `src/store/` — Global UI state using Zustand (theme, sidebar, session, ...)
  - `src/utils/` — Helpers, formatters, pure utilities
  - `src/types/` — TypeScript types/interfaces shared across features
- **Colocation:** Place test files, styles, types beside their component/hook if reasonable; avoid deep nesting.
- **Atomic Design:**
  - `atoms/` and `molecules/` are for stateless, pure UI—never include business logic, API calls, or state management.
  - Business-specific UI (organisms, containers) belong in each feature, not in global components.
- **Feature Ownership:**
  - Each feature folder self-manages its code; only export publicly via `index.ts`.
  - Never directly import private files from other features—use their public API exports only.
- **Constants:**
  - All shared constants/enums are in `src/constants/`, split by group (e.g., `api.ts`, `app.ts`). Always import via `index.ts`.
    - _Example:_
      ```ts
      // src/constants/api.ts
      export const USERS_ENDPOINT = "/api/users";
      // index.ts
      export * from "./api";
      ```
      ```ts
      import { USERS_ENDPOINT } from "src/constants";
      ```
- **Import Rules:**
  - Do not import deep/private files from other features; always follow explicit boundaries.
  - Only import global helpers, types, and constants directly.
- **For full details, rationale, examples, and anti-patterns:**  
  Refer to [instructions/architectures.instructions.md](instructions/architectures.instructions.md).

---

## 3. Naming Conventions

- **Folders:** `kebab-case`
- **Component files:** `PascalCase` (e.g., `UserCard.tsx`)
- **Hooks/utilities:** `camelCase` (e.g., `useProfile.ts`)
- **Assets:** `kebab-case` or `snake_case` (e.g., `logo_dark.svg`)
- **Variables/functions:** `camelCase`
- **Types/interfaces:** `PascalCase`
- **Constants:** `UPPER_SNAKE_CASE` for primitives, `PascalCase` for enums/types
- **Test files:** `<Component>.test.tsx`
- **Import constants** only from `src/constants` root (via `index.ts`).

---

## 4. Data & API Usage

- **Wrap all SWR usage** in feature-specific hooks.
  - _Example:_
    ```ts
    // src/features/profile/hooks/useUserProfile.ts
    import useSWR from "swr";
    export function useUserProfile(id: string) {
      return useSWR(`/api/user/${id}`, fetcher);
    }
    ```
- **Validate all API responses** with Zod at the feature boundary.
  - _Example:_
    ```ts
    import { z } from "zod";
    const ProfileSchema = z.object({ name: z.string(), age: z.number() });
    ProfileSchema.parse(responseData);
    ```
- **Do not mirror server data** to Zustand; never mix SWR result state with state stores.
- **After any mutation** (POST, PUT, PATCH, DELETE), always trigger SWR `mutate()` to refresh state for related endpoints.
  - _Example:_
    ```ts
    await axios.post("/api/tasks", payload);
    mutate("/api/tasks");
    ```

---

## 5. Testing

- Use Jest + React Testing Library for all unit/component/module tests.
- Test files must live next to the module/component they cover (e.g., `UserCard.test.tsx`).
- _Example:_

  ```tsx
  import { render, screen } from "@testing-library/react";
  import UserCard from "./UserCard";

  test("renders user name", () => {
    render(<UserCard name="Ken" />);
    expect(screen.getByText("Ken")).toBeInTheDocument();
  });
  ```

---

## 6. Delivery & Code Review

- **Before adding a new component:** Always check if an existing atom or molecule can be used or extended; only create new modules if necessary.
- **Review/contribution:** Keep feedback concise and focused on the required diff only.
- **Do not push out-of-scope refactor/cleanup** unless specifically requested (no "drive-by" code cleanup).

---

## 7. Security & Performance

- **Never store secrets** (tokens, API keys) outside local state or private environment variables (e.g., `.env.local`);
- **Validate all user input** using Zod or a similar schema at mutation/API boundaries;
- **Never commit secrets/tokens** to source control;
- **Clean up listeners/effects** in component unmount (e.g., `useEffect` cleanup);
- **Optimize rendering:** Use memoization and `React.memo` for complex components, and leverage lazy loading or Suspense where appropriate.

---

## 8. Linters & Tooling

- **ESLint (strict)** & **Prettier** are mandatory for lint/format enforcement. Never hand-edit code that the tools can auto-format/fix.
- **Secrets are only allowed in** `.env.local`. Never push secrets to the repository.
- **README/Docs:** Always maintain usage/build/test docs. All public components should have JSDoc/tsdoc on key input/output.

---

## 9. Scope Guard

- **All these instructions apply only to the React + TypeScript frontend.**
- **They do not apply to** backend, infrastructure, database, CI/CD (unless specifically noted).
- If a task is outside React frontend scope, follow general project guidelines—do not force React’s feature-based patterns.

---

You may extend this file with CI/CD rules or additional guidelines as the project or team evolves.  
If needed, create or update other `.md` instructions files for deeper architectural guidance or platform-specific rules.
