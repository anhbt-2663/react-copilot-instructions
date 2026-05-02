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

- Package manager: `yarn` (preferred)
- `.github/` folder with skills, instructions, and copilot config ready
- Git repository initialized (`git init`)

## Step-by-Step Workflows

### Step 1: Verify Node.js version

> ⚠️ This project requires **Node.js >= 18.x**.
> Check current version before proceeding.

```bash
node -v
```

**If version is lower than 18.x — check for NVM first:**

```bash
# Check if nvm is available
command -v nvm
```

**If NVM is available → use it:**

```bash
# Install and use Node 18 LTS
nvm install 18
nvm use 18

# Optional: set as default for all future sessions
nvm alias default 18

# Verify
node -v  # should print v18.x.x or higher
```

**If NVM is NOT available → install Node.js directly:**

```bash
# macOS (Homebrew)
brew install node@18
brew link node@18 --force

# Ubuntu/Debian
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Windows (winget)
winget install OpenJS.NodeJS.LTS

# Verify after install
node -v
```

> 💡 Recommend installing NVM for easier Node version management in the future:
> https://github.com/nvm-sh/nvm

---

### Step 2: Verify Yarn is available

```bash
# Check if yarn is installed
yarn -v
```

**If yarn is NOT available → install it:**

```bash
npm install -g yarn

# Verify
yarn -v
```

---

### Step 3: Prepare project folder — preserve .github

> ⚠️ Never run `yarn create vite <project-name>` directly — it creates a new folder
> and will NOT carry over the `.github/` configuration.
> Always scaffold INTO the current folder using `.` as the target.

```bash
# Step 3a: Create and enter the project folder
mkdir <project-name>
cd <project-name>

# Step 3b: Copy .github from the template/source into this folder FIRST
# (skip if .github is already present)
cp -r /path/to/template/.github ./.github

# Step 3c: Init git
git init

# Step 3d: Scaffold Vite INTO current folder (dot = current dir)
yarn create vite . --template react-ts

# Step 3e: Install dependencies
yarn install
```

> ✅ After this step, your folder should look like:
>
> ```
> <project-name>/
>   .github/              ← preserved ✅
>     AGENTS.md
>     copilot-instructions.md
>     skills/
>   src/
>   index.html
>   package.json
>   vite.config.ts
>   ...
> ```

---

### Step 4: Install core dependencies

```bash
# Data fetching & validation
yarn add swr axios zod

# State management
yarn add zustand

# Routing
yarn add react-router-dom

# Styling
yarn add -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

---

### Step 5: Install dev tooling

```bash
yarn add -D \
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

---

### Step 6: Install testing tools

```bash
yarn add -D \
  jest \
  @testing-library/react \
  @testing-library/jest-dom \
  @testing-library/user-event \
  @types/jest \
  jest-environment-jsdom \
  ts-jest
```

---

### Step 7: Setup folder structure

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
      ApiService.ts
    types/
      api.types.ts
    index.ts
  assets/
  App.tsx
  main.tsx
```

---

### Step 8: Configure path aliases

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

---

### Step 9: Setup API types

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

---

### Step 10: Setup ApiService class

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

---

### Step 11: Export ApiService + fetcher

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

---

### Step 12: Configure ESLint

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

---

### Step 13: Configure Prettier

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

---

### Step 14: Configure Jest

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

---

### Step 15: Setup Husky + lint-staged

```bash
yarn husky install
yarn husky add .husky/pre-commit "yarn lint-staged"
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

---

### Step 16: Setup environment files

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

---

### Step 17: Validate setup

```bash
yarn lint
yarn format
yarn test
yarn build
```

## Troubleshooting

| Issue                                | Solution                                                        |
| ------------------------------------ | --------------------------------------------------------------- |
| `node -v` < 18 + NVM available       | `nvm install 18 && nvm use 18`                                  |
| `node -v` < 18 + no NVM              | Install Node 18 via Homebrew / apt / winget theo OS             |
| `yarn` command not found             | `npm install -g yarn` rồi verify `yarn -v`                      |
| `.github/` bị mất sau scaffold       | Dùng `yarn create vite .` thay vì `yarn create vite <name>`     |
| Vite hỏi "overwrite existing files?" | Chọn `ignore` — chỉ overwrite Vite files, không động `.github/` |
| Path alias `src/` not resolved       | Check cả `vite.config.ts` và `tsconfig.json`                    |
| Husky hook not running               | `yarn prepare` để re-initialize                                 |
| ESLint & Prettier conflict           | Đảm bảo `eslint-config-prettier` là cuối cùng trong `extends`   |
| Jest can't resolve path alias        | Thêm `moduleNameMapper` trong `jest.config.ts`                  |
| `any` type error                     | Dùng proper TypeScript types — không dùng `any`                 |
| Vite env variable undefined          | Prefix tất cả env vars với `VITE_`                              |
| Token not attached to request        | Check `setupRequestInterceptor` đọc đúng storage key            |
| 401 redirect loop                    | Đảm bảo `/login` không gọi protected API                        |

## References

- [architectures.instructions.md](../../../instructions/architectures.instructions.md)
- [state-management.instructions.md](../../../instructions/state-management.instructions.md)
- [copilot-instructions.md](../../copilot-instructions.md)
- [Vite docs](https://vitejs.dev/guide/)
- [Axios interceptors docs](https://axios-http.com/docs/interceptors)
- [NVM docs](https://github.com/nvm-sh/nvm)
- [Agent Skills Spec](https://agentskills.io/specification)
