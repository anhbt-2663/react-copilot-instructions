---
name: setup-project
description: 'Scaffold and configure a new React + TypeScript + Vite project with full tooling: folder structure, ESLint, Prettier, Husky, lint-staged, path aliases, and environment setup. Use when asked to "setup project", "init project", "bootstrap app", "create new React app", "initialize frontend project", or when starting from scratch.'
---

# Setup React + TypeScript + Vite Project

Initializes a production-ready React project with full tooling setup following project conventions. Covers folder structure, code quality tools, git hooks, and environment configuration.

## When to Use This Skill

- User asks to "setup project", "init project", "bootstrap a React app"
- Starting a new frontend project from scratch
- Need a consistent, convention-compliant project base for the team

## Prerequisites

- Node.js >= 18.x
- Package manager: `npm` / `yarn` / `pnpm`
- Project name in `kebab-case` (e.g. `my-app`)
- Git repository initialized (`git init`)

## Step-by-Step Workflows

### Step 1: Bootstrap Vite + React + TypeScript

```bash
npm create vite@latest <project-name> -- --template react-ts
cd <project-name>
npm install
```

### Step 2: Install core dependencies

```bash
# Data fetching & validation
npm install swr axios zod

# State management
npm install zustand

# Routing
npm install react-router-dom

# Styling
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Step 3: Install dev tooling

```bash
npm install -D \
  eslint \
  @typescript-eslint/eslint-plugin \
  @typescript-eslint/parser \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-plugin-import \
  eslint-config-prettier \
  prettier \
  husky \
  lint-staged
```

### Step 4: Install testing tools

```bash
npm install -D \
  jest \
  @testing-library/react \
  @testing-library/jest-dom \
  @testing-library/user-event \
  @types/jest \
  jest-environment-jsdom \
  ts-jest
```

### Step 5: Setup folder structure

```
src/
  components/
    atoms/
    molecules/
  features/
  layouts/
  pages/
  store/
  constants/
    index.ts
  utils/
  types/
  api/
    core/
      ApiService.ts       ← base class: generic REST methods + interceptors
    types/
      api.types.ts        ← shared API types (ApiResponse, ApiServiceOptions...)
    index.ts              ← export ApiService + fetcher
  assets/
  App.tsx
  main.tsx
```

### Step 6: Configure path aliases

Update `vite.config.ts`:

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      src: path.resolve(__dirname, "./src"),
    },
  },
});
```

Update `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "src/*": ["src/*"]
    },
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### Step 7: Setup API types

Generate `src/api/types/api.types.ts`:

```ts
import type { AxiosRequestConfig } from "axios";

export interface ApiResponse<T> {
  data: T;
  message: string;
  status: number;
}

export interface PaginatedResponse<T> {
  data: T[];
  total: number;
  page: number;
  limit: number;
}

export interface PaginationParams {
  page?: number;
  limit?: number;
  sort?: string;
  order?: "asc" | "desc";
}

export interface AxiosRequestConfigWithToast extends AxiosRequestConfig {
  toast?: boolean;
  toastMessage?: string;
}

export interface TPromiseParams {
  resolve: (value: string) => void;
  reject: (reason?: unknown) => void;
}

export interface ApiServiceOptions {
  withCredentials?: boolean;
  timeout?: number;
}
```

### Step 8: Setup ApiService class

Generate `src/api/core/ApiService.ts`:

```ts
import axios from "axios";
import type { AxiosInstance } from "axios";
import type {
  ApiResponse,
  ApiServiceOptions,
  AxiosRequestConfigWithToast,
  TPromiseParams,
} from "../types/api.types";

export class ApiService {
  private axiosInstance: AxiosInstance;
  private isRefreshing = false;
  private failedRequestQueue: TPromiseParams[] = [];

  constructor(baseURL: string, options?: ApiServiceOptions) {
    this.axiosInstance = axios.create({
      baseURL,
      timeout: options?.timeout ?? 60_000,
      withCredentials: options?.withCredentials ?? false,
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json",
      },
    });

    this.setupRequestInterceptor();
    this.setupResponseInterceptor();
  }

  private setupRequestInterceptor(): void {
    this.axiosInstance.interceptors.request.use(
      (config) => {
        const token = localStorage.getItem("access_token");
        if (token && config.headers) {
          config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
      },
      (error) => Promise.reject(error),
    );
  }

  private setupResponseInterceptor(): void {
    this.axiosInstance.interceptors.response.use(
      (response) => response,
      async (error) => {
        const originalRequest = error.config;

        if (error.response?.status === 401 && !originalRequest._retry) {
          if (this.isRefreshing) {
            return new Promise<string>((resolve, reject) => {
              this.failedRequestQueue.push({ resolve, reject });
            })
              .then((token) => {
                originalRequest.headers.Authorization = `Bearer ${token}`;
                return this.axiosInstance(originalRequest);
              })
              .catch((err) => Promise.reject(err));
          }

          originalRequest._retry = true;
          this.isRefreshing = true;

          try {
            const refreshToken = localStorage.getItem("refresh_token");
            const { data } = await axios.post<
              ApiResponse<{ access_token: string }>
            >(`${this.axiosInstance.defaults.baseURL}/auth/refresh`, {
              refresh_token: refreshToken,
            });
            const newToken = data.data.access_token;

            localStorage.setItem("access_token", newToken);
            this.axiosInstance.defaults.headers.common.Authorization = `Bearer ${newToken}`;

            this.failedRequestQueue.forEach(({ resolve }) => resolve(newToken));
            this.failedRequestQueue = [];

            return this.axiosInstance(originalRequest);
          } catch (refreshError) {
            this.failedRequestQueue.forEach(({ reject }) =>
              reject(refreshError),
            );
            this.failedRequestQueue = [];
            localStorage.removeItem("access_token");
            localStorage.removeItem("refresh_token");
            window.location.href = "/login";
            return Promise.reject(refreshError);
          } finally {
            this.isRefreshing = false;
          }
        }

        switch (error.response?.status) {
          case 403:
            window.location.href = "/403";
            break;
          case 404:
            console.warn("[API] Resource not found:", error.config?.url);
            break;
          case 500:
            console.error("[API] Internal server error:", error.config?.url);
            break;
          default:
            console.error("[API] Unexpected error:", error.message);
        }

        return Promise.reject(error);
      },
    );
  }

  public get<TResponse>(
    url: string,
    config?: AxiosRequestConfigWithToast,
  ): Promise<TResponse> {
    return this.axiosInstance
      .get<ApiResponse<TResponse>>(url, config)
      .then((res) => res.data.data);
  }

  public post<TResponse, TBody = Record<string, unknown>>(
    url: string,
    data?: TBody,
    config?: AxiosRequestConfigWithToast,
  ): Promise<TResponse> {
    return this.axiosInstance
      .post<ApiResponse<TResponse>>(url, data, config)
      .then((res) => res.data.data);
  }

  public put<TResponse, TBody = Record<string, unknown>>(
    url: string,
    data?: TBody,
    config?: AxiosRequestConfigWithToast,
  ): Promise<TResponse> {
    return this.axiosInstance
      .put<ApiResponse<TResponse>>(url, data, config)
      .then((res) => res.data.data);
  }

  public patch<TResponse, TBody = Record<string, unknown>>(
    url: string,
    data?: TBody,
    config?: AxiosRequestConfigWithToast,
  ): Promise<TResponse> {
    return this.axiosInstance
      .patch<ApiResponse<TResponse>>(url, data, config)
      .then((res) => res.data.data);
  }

  public delete<TResponse>(
    url: string,
    config?: AxiosRequestConfigWithToast,
  ): Promise<TResponse> {
    return this.axiosInstance
      .delete<ApiResponse<TResponse>>(url, config)
      .then((res) => res.data.data);
  }
}
```

### Step 9: Export ApiService + fetcher

Generate `src/api/index.ts`:

```ts
import { ApiService } from "./core/ApiService";

export { ApiService } from "./core/ApiService";
export type {
  ApiResponse,
  PaginatedResponse,
  PaginationParams,
  AxiosRequestConfigWithToast,
} from "./types/api.types";

const apiService = new ApiService(import.meta.env.VITE_API_BASE_URL);

export const fetcher = <T>(url: string): Promise<T> => apiService.get<T>(url);
```

Usage per feature:

```ts
// src/features/user/api/user-api.ts
import { ApiService } from "src/api";
import type { User, CreateUserDto } from "../types";

const service = new ApiService(import.meta.env.VITE_API_BASE_URL);

export const userApi = {
  getById: (id: string) => service.get<User>(`/users/${id}`),
  getAll: () => service.get<User[]>("/users"),
  create: (dto: CreateUserDto) =>
    service.post<User, CreateUserDto>("/users", dto),
  update: (id: string, dto: Partial<CreateUserDto>) =>
    service.patch<User, Partial<CreateUserDto>>(`/users/${id}`, dto),
  remove: (id: string) => service.delete<void>(`/users/${id}`),
};
```

### Step 10: Configure ESLint

Create `.eslintrc.cjs`:

```js
module.exports = {
  root: true,
  parser: "@typescript-eslint/parser",
  parserOptions: { ecmaVersion: "latest", sourceType: "module" },
  env: { browser: true, es2021: true },
  plugins: ["react", "react-hooks", "@typescript-eslint", "import"],
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier",
  ],
  rules: {
    "react/react-in-jsx-scope": "off",
    "@typescript-eslint/no-explicit-any": "error",
    "import/no-restricted-paths": [
      "error",
      {
        zones: [
          {
            target: "./src/features",
            from: "./src/features",
            except: ["./index.ts"],
          },
        ],
      },
    ],
  },
  settings: { react: { version: "detect" } },
};
```

### Step 11: Configure Prettier

Create `.prettierrc`:

```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2
}
```

### Step 12: Configure Jest

Create `jest.config.ts`:

```ts
export default {
  preset: "ts-jest",
  testEnvironment: "jsdom",
  setupFilesAfterFramework: ["@testing-library/jest-dom"],
  moduleNameMapper: {
    "^src/(.*)$": "<rootDir>/src/$1",
  },
};
```

### Step 13: Setup Husky + lint-staged

```bash
npx husky install
npx husky add .husky/pre-commit "npx lint-staged"
```

Update `package.json`:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext .ts,.tsx",
    "format": "prettier --write .",
    "test": "jest --watchAll=false",
    "test:watch": "jest --watch",
    "prepare": "husky install"
  },
  "lint-staged": {
    "**/*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "**/*.{json,css,md}": ["prettier --write"]
  }
}
```

### Step 14: Setup environment files

Create `.env.example`:

```env
VITE_API_BASE_URL=http://localhost:3000
```

Create `.env.local`:

```env
VITE_API_BASE_URL=http://localhost:3000
```

Update `.gitignore`:

```
.env.local
.env.*.local
```

### Step 15: Validate setup

```bash
npm run lint
npm run format
npm run test
npm run build
```

## Troubleshooting

| Issue                          | Solution                                                               |
| ------------------------------ | ---------------------------------------------------------------------- |
| Path alias `src/` not resolved | Check both `vite.config.ts` and `tsconfig.json` paths                  |
| Husky hook not running         | Run `npm run prepare` to re-initialize husky                           |
| ESLint & Prettier conflict     | Ensure `eslint-config-prettier` is last in `extends`                   |
| Jest can't resolve path alias  | Add `moduleNameMapper` in `jest.config.ts`                             |
| `any` type error from ESLint   | Use proper TypeScript types — never use `any`                          |
| Vite env variable undefined    | Prefix all env vars with `VITE_`                                       |
| CORS error in browser          | Configure CORS on server — never add CORS headers on client            |
| Token not attached to request  | Check `setupRequestInterceptor` reads correct storage key              |
| 401 redirect loop              | Ensure `/login` route does not call protected API                      |
| Refresh token fails silently   | Check `failedRequestQueue` is cleared in both success and catch        |
| Multiple instances conflict    | `isRefreshing` is instance-level — each `new ApiService()` is isolated |

## References

- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
- [state-management.instructions.md](../../../instructions/state-management.instructions.md)
- [copilot-instructions.md](../../copilot-instructions.md)
- [Vite docs](https://vitejs.dev/guide/)
- [Axios interceptors docs](https://axios-http.com/docs/interceptors)
- [Agent Skills Spec](https://agentskills.io/specification)
