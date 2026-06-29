# Frontend Web Rebuild v2 — Iso Mobile Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild from scratch `/Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-web/` to reach functional and design parity with `frontend-mobile/`, kill all legacy API fallbacks, and ship a production container that replaces the current CRA build.

**Architecture:** Vite SPA + TypeScript strict + pnpm 10 + shadcn/ui (Tailwind v4 + Radix) + TanStack Query 5 + Zustand 5 + react-router v7 + apisauce + framer-motion. Services/types/stores/hooks/design tokens are copied 1:1 from `frontend-mobile/src/`. Components keep the same name/API as mobile but render with shadcn/Tailwind. Auth = Bearer JWT + localStorage + refresh queue interceptor.

**Tech Stack:** Vite 6, React 19, TypeScript 5.9, Tailwind v4, shadcn/ui, TanStack Query 5, Zustand 5, react-router 7, framer-motion, apisauce 3.1, @react-oauth/google, i18next, Vitest + RTL, ESLint + Prettier, pnpm 10, nginx alpine.

**Source of truth:** `/Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile/` — when in doubt about a behavior, the mobile implementation wins.

**Backend contract:** `/Users/ilhan.neuville/Desktop/eip/Syntheza/backend/src/app.ts` mounts all routes under `/api/*`. The API base URL is `https://api.syntheza.ovh/` (trailing slash kept). No legacy `/user/*` (no `/api/`) endpoint exists — only `/api/user/*`.

---

## Phases overview

| Phase | Name | Depends on | Outcome |
|---|---|---|---|
| 0 | Branch & wipe | — | `feat/rebuild-v2` checked out, old `src/` removed, lockfile removed |
| 1 | Bootstrap Vite + TS + pnpm | 0 | `pnpm dev` boots an empty React+TS app |
| 2 | Tailwind v4 + design tokens | 1 | CSS vars match mobile palette, dark class works |
| 3 | shadcn/ui init + base primitives | 2 | `Button`, `Input`, `Dialog`, `Sheet`, `Tabs`, `Switch`, `Avatar` installed |
| 4 | ESLint + Prettier + Vitest | 1 | `pnpm lint` / `pnpm test` exit 0 |
| 5 | Storage util + Config + path alias | 1 | `storage.ts`, `config/index.ts`, `@/*` alias resolved |
| 6 | API client + auth services | 5 | `api`, `tokenService`, `authService` with refresh queue |
| 7 | Data services (12) | 6 | `feedService`, `publisherService`, etc. all copied |
| 8 | QueryClient + AuthContext + preferencesStore | 6, 7 | App-wide state plumbed |
| 9 | i18n FR setup | 1 | `t("...")` works, FR translations loaded |
| 10 | Router skeleton + ProtectedRoute | 8 | `/login` and `/home` reachable |
| 11 | Component library port (22 components) | 3, 7 | All UI atoms ready |
| 12 | Auth screens (4) | 10, 11 | Login/Register/Forgot/Reset functional |
| 13 | App layout + sidebar/tabs + dark toggle | 11, 8 | Authenticated layout shell |
| 14 | Home (Feed) | 13, 11 | Infinite feed with likes/bookmarks |
| 15 | Discover | 13, 11 | 3 tabs + Publisher cards + subscribe |
| 16 | Search | 13, 11 | Debounced query → results |
| 17 | Notifications | 13, 11 | List + mark read + delete |
| 18 | Profile (perso + public) | 13, 11 | Stats + follow + bio edit |
| 19 | Article detail + Comments + Trust sheet | 13, 11 | Full article view |
| 20 | Settings (6 sub-routes) | 13, 11 | Account/Privacy/Appearance/Notif/Security/Data |
| 21 | Tests Vitest (15+ tests) | 14–20 | `pnpm test` green |
| 22 | Docker + CI/CD update | all | Container builds, deploy workflow uses pnpm |
| 23 | Smoke + merge | 22 | User-validated build, ff-merge on main |

---

## Phase 0 — Branch & wipe

**Files:**
- Modify: working tree of `/Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-web/`

- [ ] **Step 0.1: Create and checkout branch**

```bash
cd /Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-web
git status
git checkout -b feat/rebuild-v2
git status
```

Expected: clean working tree, branch `feat/rebuild-v2` active.

- [ ] **Step 0.2: Tag the pre-rebuild commit for safety**

```bash
git tag -a pre-rebuild-v2 -m "Pre-rebuild v2 snapshot of old CRA frontend-web"
git tag --list pre-rebuild-v2
```

Expected: tag listed.

- [ ] **Step 0.3: Wipe old source tree (keep .github/, Dockerfile, docker-compose.prod.yml, nginx.conf, README.md, .gitignore, .env until later)**

```bash
rm -rf src/ public/ package-lock.json package.json
ls -la
```

Expected: `src/` and `public/` gone, `package*.json` gone, `Dockerfile` `docker-compose.prod.yml` `nginx.conf` `README.md` still present.

- [ ] **Step 0.4: Commit the wipe**

```bash
git add -A
git commit -m "chore: wipe old CRA scaffold before rebuild-v2"
```

Expected: commit created, working tree clean.

---

## Phase 1 — Bootstrap Vite + TS + pnpm

**Files:**
- Create: `package.json`, `pnpm-lock.yaml`, `vite.config.ts`, `tsconfig.json`, `tsconfig.node.json`, `index.html`, `src/main.tsx`, `src/App.tsx`, `.npmrc`, `.gitignore`

- [ ] **Step 1.1: Init pnpm and base package.json**

```bash
corepack enable
corepack prepare pnpm@10.11.0 --activate
pnpm --version
```

Expected: pnpm version 10.11.x printed.

- [ ] **Step 1.2: Create `.npmrc`**

```
auto-install-peers=true
strict-peer-dependencies=false
```

Path: `.npmrc`

- [ ] **Step 1.3: Create `package.json`**

```json
{
  "name": "syntheza-frontend-web",
  "version": "2.0.0",
  "private": true,
  "type": "module",
  "engines": {
    "node": ">=20.0.0"
  },
  "packageManager": "pnpm@10.11.0",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview --port 4173",
    "typecheck": "tsc --noEmit -p .",
    "lint": "eslint . --max-warnings 0",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx,css,md}\"",
    "format:check": "prettier --check \"src/**/*.{ts,tsx,css,md}\"",
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```

- [ ] **Step 1.4: Install Vite + React + TS deps**

```bash
pnpm add react@19.2.0 react-dom@19.2.0
pnpm add -D vite@^6.0.0 @vitejs/plugin-react@^4.3.4 typescript@5.9.3 @types/react@^19 @types/react-dom@^19 @types/node@^20
```

Expected: `package.json` updated, `pnpm-lock.yaml` created.

- [ ] **Step 1.5: Create `tsconfig.json`**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "jsx": "react-jsx",
    "strict": true,
    "noImplicitAny": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "isolatedModules": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },
    "types": ["vite/client", "node", "vitest/globals"]
  },
  "include": ["src", "vite.config.ts", "vitest.config.ts"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

- [ ] **Step 1.6: Create `tsconfig.node.json`**

```json
{
  "compilerOptions": {
    "composite": true,
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts", "vitest.config.ts"]
}
```

- [ ] **Step 1.7: Create `vite.config.ts`**

```ts
import { defineConfig, loadEnv } from "vite"
import react from "@vitejs/plugin-react"
import path from "node:path"

export default defineConfig(({ mode }) => {
  const env = loadEnv(mode, process.cwd(), "")
  return {
    plugins: [react()],
    resolve: {
      alias: {
        "@": path.resolve(__dirname, "./src"),
      },
    },
    server: {
      port: 3000,
      strictPort: true,
    },
    preview: {
      port: 4173,
    },
    define: {
      // expose VITE_* envs is automatic; explicit ones go here if needed
    },
    build: {
      sourcemap: true,
      target: "es2022",
      chunkSizeWarningLimit: 800,
    },
  }
})
```

- [ ] **Step 1.8: Create `index.html`**

```html
<!doctype html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="theme-color" content="#000000" />
    <title>Syntheza</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&display=swap"
      rel="stylesheet"
    />
  </head>
  <body class="bg-[var(--bg)] text-[var(--color)] antialiased">
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

- [ ] **Step 1.9: Create `src/main.tsx`**

```tsx
import { StrictMode } from "react"
import { createRoot } from "react-dom/client"
import App from "./App"
import "./globals.css"

const rootEl = document.getElementById("root")
if (!rootEl) throw new Error("Root element #root not found in index.html")

createRoot(rootEl).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

- [ ] **Step 1.10: Create `src/App.tsx` (placeholder, will be replaced in Phase 10)**

```tsx
export default function App() {
  return (
    <main className="flex min-h-screen items-center justify-center">
      <h1 className="text-2xl font-bold">Syntheza v2 — bootstrap OK</h1>
    </main>
  )
}
```

- [ ] **Step 1.11: Create `src/globals.css` (placeholder, Phase 2 fills it)**

```css
/* placeholder — Phase 2 will replace with Tailwind directives and CSS vars */
:root {
  --bg: #000;
  --color: #fff;
}
```

- [ ] **Step 1.12: Create `.gitignore`**

```
node_modules/
dist/
.env.local
.env.*.local
*.log
.DS_Store
.vite/
coverage/
.eslintcache
```

- [ ] **Step 1.13: Verify dev server boots**

```bash
pnpm dev
```

Expected: Vite logs `Local: http://localhost:3000/` within 2 seconds. Stop with Ctrl-C.

- [ ] **Step 1.14: Verify production build works**

```bash
pnpm build
ls -la dist/
```

Expected: `dist/index.html` and `dist/assets/*.js` exist.

- [ ] **Step 1.15: Commit Phase 1**

```bash
git add -A
git commit -m "feat(rebuild-v2): bootstrap Vite + React 19 + TS strict + pnpm"
```

---

## Phase 2 — Tailwind v4 + design tokens

**Files:**
- Create: `src/globals.css` (real), `tailwind.config.ts`, `postcss.config.js`, `src/design/colors.ts`, `src/design/typography.ts`
- Modify: `index.html` (already linked SpaceGrotesk in Phase 1)

- [ ] **Step 2.1: Install Tailwind v4 + PostCSS plugin**

```bash
pnpm add -D tailwindcss@^4 @tailwindcss/postcss@^4 postcss@^8 autoprefixer@^10
```

- [ ] **Step 2.2: Create `postcss.config.js`**

```js
export default {
  plugins: {
    "@tailwindcss/postcss": {},
    autoprefixer: {},
  },
}
```

- [ ] **Step 2.3: Create `src/design/colors.ts` (copied from mobile, web variant)**

```ts
export const darkColors = {
  bg: "#000000",
  bg2: "#121212",
  surface: "#1a1a1a",
  surface2: "#262626",
  hover: "#2a2a2a",
  border: "#363636",
  borderMuted: "#2a2a2a",
  color: "#f5f5f5",
  color2: "#a8a8a8",
  color3: "#737373",
  color4: "#6b6b6b",
  placeholder: "#525252",
  link: "#a8a8a8",
  linkDark: "#c0c0c0",
  primary: "#3a3a3a",
  primaryHover: "#4a4a4a",
  primaryText: "#ffffff",
  danger: "#ed4956",
  dangerLight: "#ff3040",
  success: "#22c55e",
  warning: "#f59e0b",
  gradientStart: "#667eea",
  gradientEnd: "#764ba2",
} as const

export const lightColors = {
  bg: "#ffffff",
  bg2: "#fafafa",
  surface: "#f8f8f8",
  surface2: "#f8f9fa",
  hover: "#f2f2f2",
  border: "#dbdbdb",
  borderMuted: "#efefef",
  color: "#262626",
  color2: "#8e8e8e",
  color3: "#757575",
  color4: "#8a8a8a",
  placeholder: "#a8a8a8",
  link: "#737373",
  linkDark: "#525252",
  primary: "#e8e8e8",
  primaryHover: "#d4d4d4",
  primaryText: "#262626",
  danger: "#ed4956",
  dangerLight: "#ff3040",
  success: "#22c55e",
  warning: "#f59e0b",
  gradientStart: "#667eea",
  gradientEnd: "#764ba2",
} as const

export type AppColors = { [K in keyof typeof darkColors]: string }
```

- [ ] **Step 2.4: Create `src/design/typography.ts`**

```ts
export const typography = {
  fontFamily: {
    sans: ["Space Grotesk", "system-ui", "-apple-system", "Segoe UI", "sans-serif"].join(", "),
  },
  size: {
    xs: "0.75rem",
    sm: "0.875rem",
    base: "1rem",
    lg: "1.125rem",
    xl: "1.25rem",
    "2xl": "1.5rem",
    "3xl": "1.75rem",
    "4xl": "2.125rem",
    "5xl": "2.5rem",
  },
} as const
```

- [ ] **Step 2.5: Overwrite `src/globals.css` with Tailwind v4 directives + CSS vars**

```css
@import "tailwindcss";

@theme {
  --font-sans: "Space Grotesk", system-ui, -apple-system, "Segoe UI", sans-serif;

  --color-bg: #ffffff;
  --color-bg2: #fafafa;
  --color-surface: #f8f8f8;
  --color-surface2: #f8f9fa;
  --color-hover: #f2f2f2;
  --color-border: #dbdbdb;
  --color-border-muted: #efefef;
  --color-color: #262626;
  --color-color2: #8e8e8e;
  --color-color3: #757575;
  --color-color4: #8a8a8a;
  --color-placeholder: #a8a8a8;
  --color-link: #737373;
  --color-link-dark: #525252;
  --color-primary: #e8e8e8;
  --color-primary-hover: #d4d4d4;
  --color-primary-text: #262626;
  --color-danger: #ed4956;
  --color-danger-light: #ff3040;
  --color-success: #22c55e;
  --color-warning: #f59e0b;

  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 14px;
  --radius-xl: 18px;
  --radius-2xl: 22px;
}

@layer base {
  :root {
    --bg: #ffffff;
    --bg2: #fafafa;
    --surface: #f8f8f8;
    --surface2: #f8f9fa;
    --hover: #f2f2f2;
    --border: #dbdbdb;
    --border-muted: #efefef;
    --color: #262626;
    --color2: #8e8e8e;
    --color3: #757575;
    --color4: #8a8a8a;
    --placeholder: #a8a8a8;
    --link: #737373;
    --link-dark: #525252;
    --primary: #e8e8e8;
    --primary-hover: #d4d4d4;
    --primary-text: #262626;
    --danger: #ed4956;
    --danger-light: #ff3040;
    --success: #22c55e;
    --warning: #f59e0b;
  }

  .dark {
    --bg: #000000;
    --bg2: #121212;
    --surface: #1a1a1a;
    --surface2: #262626;
    --hover: #2a2a2a;
    --border: #363636;
    --border-muted: #2a2a2a;
    --color: #f5f5f5;
    --color2: #a8a8a8;
    --color3: #737373;
    --color4: #6b6b6b;
    --placeholder: #525252;
    --link: #a8a8a8;
    --link-dark: #c0c0c0;
    --primary: #3a3a3a;
    --primary-hover: #4a4a4a;
    --primary-text: #ffffff;
    --danger: #ed4956;
    --danger-light: #ff3040;
    --success: #22c55e;
    --warning: #f59e0b;
  }

  html,
  body,
  #root {
    height: 100%;
  }

  body {
    background-color: var(--bg);
    color: var(--color);
    font-family: var(--font-sans);
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  * {
    box-sizing: border-box;
  }
}
```

- [ ] **Step 2.6: Create `tailwind.config.ts` (kept for editor + IntelliSense; v4 also reads `@theme` from CSS)**

```ts
import type { Config } from "tailwindcss"

export default {
  darkMode: "class",
  content: ["./index.html", "./src/**/*.{ts,tsx}"],
  theme: {
    extend: {
      colors: {
        bg: "var(--bg)",
        bg2: "var(--bg2)",
        surface: "var(--surface)",
        surface2: "var(--surface2)",
        hover: "var(--hover)",
        border: "var(--border)",
        "border-muted": "var(--border-muted)",
        color: "var(--color)",
        color2: "var(--color2)",
        color3: "var(--color3)",
        color4: "var(--color4)",
        placeholder: "var(--placeholder)",
        link: "var(--link)",
        primary: "var(--primary)",
        "primary-hover": "var(--primary-hover)",
        "primary-text": "var(--primary-text)",
        danger: "var(--danger)",
        "danger-light": "var(--danger-light)",
        success: "var(--success)",
        warning: "var(--warning)",
      },
      fontFamily: {
        sans: ["Space Grotesk", "system-ui", "sans-serif"],
      },
    },
  },
  plugins: [],
} satisfies Config
```

- [ ] **Step 2.7: Update `src/App.tsx` to validate Tailwind + dark mode**

```tsx
import { useState } from "react"

export default function App() {
  const [dark, setDark] = useState(false)

  return (
    <main
      className={`${dark ? "dark" : ""} bg-bg text-color min-h-screen flex flex-col items-center justify-center gap-6`}
    >
      <h1 className="text-3xl font-bold tracking-tight">Syntheza v2 — design tokens</h1>
      <button
        onClick={() => setDark((v) => !v)}
        className="rounded-lg border border-border bg-surface px-4 py-2 text-color2 hover:bg-hover"
      >
        Toggle dark ({dark ? "on" : "off"})
      </button>
      <p className="text-color3">Si tu vois ce texte changer de couleur en cliquant, c'est OK.</p>
    </main>
  )
}
```

- [ ] **Step 2.8: Verify dark mode in browser**

```bash
pnpm dev
```

Expected: click toggle → background switches white ↔ black, text switches dark ↔ light, SpaceGrotesk font loaded. Stop with Ctrl-C.

- [ ] **Step 2.9: Verify build still works**

```bash
pnpm build
```

Expected: build OK, no Tailwind errors.

- [ ] **Step 2.10: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): Tailwind v4 + design tokens (iso mobile palette)"
```

---

## Phase 3 — shadcn/ui init + base primitives

**Files:**
- Create: `components.json`, `src/lib/utils.ts`, `src/components/ui/*.tsx`

- [ ] **Step 3.1: Install class-variance-authority, clsx, tailwind-merge, and Radix primitives**

```bash
pnpm add class-variance-authority@^0.7.1 clsx@^2.1.1 tailwind-merge@^2.5.5 lucide-react@^0.460.0
pnpm add @radix-ui/react-slot @radix-ui/react-dialog @radix-ui/react-dropdown-menu @radix-ui/react-tabs @radix-ui/react-switch @radix-ui/react-avatar @radix-ui/react-label @radix-ui/react-separator @radix-ui/react-toast @radix-ui/react-tooltip
```

- [ ] **Step 3.2: Create `src/lib/utils.ts`**

```ts
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

- [ ] **Step 3.3: Create `components.json` (shadcn config, used by `npx shadcn` CLI but we'll author manually)**

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "new-york",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.ts",
    "css": "src/globals.css",
    "baseColor": "neutral",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  }
}
```

- [ ] **Step 3.4: Create `src/components/ui/button.tsx`**

```tsx
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const buttonVariants = cva(
  "inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-color3 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-color text-bg hover:bg-color/90",
        outline: "border border-border bg-bg text-color hover:bg-surface",
        ghost: "text-color hover:bg-hover",
        destructive: "bg-danger text-white hover:bg-danger-light",
        secondary: "bg-surface text-color hover:bg-hover",
        link: "text-link underline-offset-4 hover:underline",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: { variant: "default", size: "default" },
  },
)

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : "button"
    return <Comp className={cn(buttonVariants({ variant, size }), className)} ref={ref} {...props} />
  },
)
Button.displayName = "Button"

export { buttonVariants }
```

- [ ] **Step 3.5: Create `src/components/ui/input.tsx`**

```tsx
import * as React from "react"
import { cn } from "@/lib/utils"

export const Input = React.forwardRef<HTMLInputElement, React.InputHTMLAttributes<HTMLInputElement>>(
  ({ className, type, ...props }, ref) => (
    <input
      type={type}
      ref={ref}
      className={cn(
        "flex h-11 w-full rounded-md border border-border bg-bg px-3 py-2 text-sm text-color placeholder:text-placeholder focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-color3 disabled:cursor-not-allowed disabled:opacity-50",
        className,
      )}
      {...props}
    />
  ),
)
Input.displayName = "Input"
```

- [ ] **Step 3.6: Create `src/components/ui/label.tsx`**

```tsx
import * as LabelPrimitive from "@radix-ui/react-label"
import * as React from "react"
import { cn } from "@/lib/utils"

export const Label = React.forwardRef<
  React.ElementRef<typeof LabelPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof LabelPrimitive.Root>
>(({ className, ...props }, ref) => (
  <LabelPrimitive.Root
    ref={ref}
    className={cn("text-sm font-medium text-color leading-none", className)}
    {...props}
  />
))
Label.displayName = LabelPrimitive.Root.displayName
```

- [ ] **Step 3.7: Create `src/components/ui/dialog.tsx`**

```tsx
import * as DialogPrimitive from "@radix-ui/react-dialog"
import { X } from "lucide-react"
import * as React from "react"
import { cn } from "@/lib/utils"

export const Dialog = DialogPrimitive.Root
export const DialogTrigger = DialogPrimitive.Trigger
export const DialogPortal = DialogPrimitive.Portal
export const DialogClose = DialogPrimitive.Close

export const DialogOverlay = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Overlay>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Overlay>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Overlay
    ref={ref}
    className={cn(
      "fixed inset-0 z-50 bg-black/70 backdrop-blur-sm data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0",
      className,
    )}
    {...props}
  />
))
DialogOverlay.displayName = DialogPrimitive.Overlay.displayName

export const DialogContent = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Content>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Content>
>(({ className, children, ...props }, ref) => (
  <DialogPortal>
    <DialogOverlay />
    <DialogPrimitive.Content
      ref={ref}
      className={cn(
        "fixed left-[50%] top-[50%] z-50 grid w-full max-w-lg translate-x-[-50%] translate-y-[-50%] gap-4 border border-border bg-surface p-6 shadow-xl rounded-lg",
        className,
      )}
      {...props}
    >
      {children}
      <DialogPrimitive.Close className="absolute right-4 top-4 rounded-sm opacity-70 ring-offset-bg transition-opacity hover:opacity-100">
        <X className="h-4 w-4" />
        <span className="sr-only">Fermer</span>
      </DialogPrimitive.Close>
    </DialogPrimitive.Content>
  </DialogPortal>
))
DialogContent.displayName = DialogPrimitive.Content.displayName

export const DialogHeader = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div className={cn("flex flex-col space-y-1.5 text-left", className)} {...props} />
)
DialogHeader.displayName = "DialogHeader"

export const DialogFooter = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div className={cn("flex justify-end gap-2", className)} {...props} />
)
DialogFooter.displayName = "DialogFooter"

export const DialogTitle = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Title>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Title>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Title
    ref={ref}
    className={cn("text-lg font-semibold text-color", className)}
    {...props}
  />
))
DialogTitle.displayName = DialogPrimitive.Title.displayName

export const DialogDescription = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Description>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Description>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Description
    ref={ref}
    className={cn("text-sm text-color3", className)}
    {...props}
  />
))
DialogDescription.displayName = DialogPrimitive.Description.displayName
```

- [ ] **Step 3.8: Create `src/components/ui/tabs.tsx`**

```tsx
import * as TabsPrimitive from "@radix-ui/react-tabs"
import * as React from "react"
import { cn } from "@/lib/utils"

export const Tabs = TabsPrimitive.Root

export const TabsList = React.forwardRef<
  React.ElementRef<typeof TabsPrimitive.List>,
  React.ComponentPropsWithoutRef<typeof TabsPrimitive.List>
>(({ className, ...props }, ref) => (
  <TabsPrimitive.List
    ref={ref}
    className={cn("inline-flex items-center gap-1 rounded-lg bg-surface p-1", className)}
    {...props}
  />
))
TabsList.displayName = TabsPrimitive.List.displayName

export const TabsTrigger = React.forwardRef<
  React.ElementRef<typeof TabsPrimitive.Trigger>,
  React.ComponentPropsWithoutRef<typeof TabsPrimitive.Trigger>
>(({ className, ...props }, ref) => (
  <TabsPrimitive.Trigger
    ref={ref}
    className={cn(
      "inline-flex items-center justify-center whitespace-nowrap rounded-md px-3 py-1.5 text-sm font-medium text-color2 transition-all data-[state=active]:bg-bg data-[state=active]:text-color data-[state=active]:shadow",
      className,
    )}
    {...props}
  />
))
TabsTrigger.displayName = TabsPrimitive.Trigger.displayName

export const TabsContent = React.forwardRef<
  React.ElementRef<typeof TabsPrimitive.Content>,
  React.ComponentPropsWithoutRef<typeof TabsPrimitive.Content>
>(({ className, ...props }, ref) => (
  <TabsPrimitive.Content ref={ref} className={cn("mt-4", className)} {...props} />
))
TabsContent.displayName = TabsPrimitive.Content.displayName
```

- [ ] **Step 3.9: Create `src/components/ui/switch.tsx`**

```tsx
import * as SwitchPrimitive from "@radix-ui/react-switch"
import * as React from "react"
import { cn } from "@/lib/utils"

export const Switch = React.forwardRef<
  React.ElementRef<typeof SwitchPrimitive.Root>,
  React.ComponentPropsWithoutRef<typeof SwitchPrimitive.Root>
>(({ className, ...props }, ref) => (
  <SwitchPrimitive.Root
    ref={ref}
    className={cn(
      "peer inline-flex h-6 w-11 shrink-0 cursor-pointer items-center rounded-full border-2 border-transparent transition-colors data-[state=checked]:bg-color data-[state=unchecked]:bg-border",
      className,
    )}
    {...props}
  >
    <SwitchPrimitive.Thumb className="pointer-events-none block h-5 w-5 rounded-full bg-bg shadow-lg ring-0 transition-transform data-[state=checked]:translate-x-5 data-[state=unchecked]:translate-x-0" />
  </SwitchPrimitive.Root>
))
Switch.displayName = SwitchPrimitive.Root.displayName
```

- [ ] **Step 3.10: Verify shadcn primitives compile**

```bash
pnpm typecheck
```

Expected: no errors.

- [ ] **Step 3.11: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): shadcn primitives (button, input, label, dialog, tabs, switch)"
```

---

## Phase 4 — ESLint + Prettier + Vitest

**Files:**
- Create: `.eslintrc.cjs`, `.eslintignore`, `.prettierrc`, `.prettierignore`, `vitest.config.ts`, `src/test/setup.ts`

- [ ] **Step 4.1: Install ESLint + Prettier + Vitest deps**

```bash
pnpm add -D eslint@^8.57.0 @typescript-eslint/parser@^8 @typescript-eslint/eslint-plugin@^8 eslint-plugin-react@^7.36 eslint-plugin-react-hooks@^5 eslint-plugin-jsx-a11y@^6 eslint-config-prettier@^9 eslint-plugin-prettier@^5 prettier@^3
pnpm add -D vitest@^2 @vitest/ui@^2 jsdom@^25 @testing-library/react@^16 @testing-library/jest-dom@^6 @testing-library/user-event@^14
```

- [ ] **Step 4.2: Create `.eslintrc.cjs`**

```js
module.exports = {
  root: true,
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaVersion: 2022,
    sourceType: "module",
    ecmaFeatures: { jsx: true },
  },
  settings: {
    react: { version: "detect" },
  },
  env: { browser: true, es2022: true, node: true },
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react/jsx-runtime",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:prettier/recommended",
  ],
  rules: {
    "prettier/prettier": "error",
    "react/prop-types": "off",
    "react/react-in-jsx-scope": "off",
    "@typescript-eslint/no-unused-vars": ["error", { argsIgnorePattern: "^_", varsIgnorePattern: "^_" }],
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/consistent-type-imports": ["error", { prefer: "type-imports" }],
  },
}
```

- [ ] **Step 4.3: Create `.eslintignore`**

```
node_modules
dist
coverage
*.config.js
*.config.ts
public
```

- [ ] **Step 4.4: Create `.prettierrc`**

```json
{
  "semi": false,
  "singleQuote": false,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

- [ ] **Step 4.5: Create `.prettierignore`**

```
node_modules
dist
coverage
pnpm-lock.yaml
package-lock.json
```

- [ ] **Step 4.6: Create `vitest.config.ts`**

```ts
import { defineConfig } from "vitest/config"
import react from "@vitejs/plugin-react"
import path from "node:path"

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: ["./src/test/setup.ts"],
    css: false,
    coverage: {
      provider: "v8",
      reporter: ["text", "html"],
      exclude: ["src/test/**", "src/main.tsx", "src/**/*.d.ts"],
    },
  },
})
```

- [ ] **Step 4.7: Create `src/test/setup.ts`**

```ts
import "@testing-library/jest-dom/vitest"
import { afterEach } from "vitest"
import { cleanup } from "@testing-library/react"

afterEach(() => {
  cleanup()
})
```

- [ ] **Step 4.8: Run lint to confirm it works (will fail clean on empty App.tsx; fix any minor issues)**

```bash
pnpm lint
```

Expected: 0 errors. If errors appear, fix them in `src/App.tsx` (likely formatting).

- [ ] **Step 4.9: Run vitest dry**

```bash
pnpm test
```

Expected: `No test files found` (or 0 tests) — exit 0.

- [ ] **Step 4.10: Commit**

```bash
git add -A
git commit -m "chore(rebuild-v2): ESLint + Prettier + Vitest configured"
```

---

## Phase 5 — Storage util + Config + .env

**Files:**
- Create: `src/utils/storage.ts`, `src/utils/storage.test.ts`, `src/config/index.ts`, `src/config/google.ts`, `.env`, `.env.example`

- [ ] **Step 5.1: Write the failing storage test**

Path: `src/utils/storage.test.ts`

```ts
import { describe, it, expect, beforeEach } from "vitest"
import { load, loadString, save, saveString, clear, remove } from "./storage"

describe("storage", () => {
  beforeEach(() => {
    clear()
  })

  it("saves and loads a string", () => {
    saveString("key", "value")
    expect(loadString("key")).toEqual("value")
  })

  it("saves and loads an object", () => {
    save("obj", { x: 1, y: "two" })
    expect(load<{ x: number; y: string }>("obj")).toEqual({ x: 1, y: "two" })
  })

  it("returns null on missing key", () => {
    expect(loadString("missing")).toBeNull()
    expect(load("missing")).toBeNull()
  })

  it("removes a key", () => {
    save("obj", { a: 1 })
    remove("obj")
    expect(load("obj")).toBeNull()
  })

  it("clear() empties everything", () => {
    save("a", 1)
    save("b", 2)
    clear()
    expect(load("a")).toBeNull()
    expect(load("b")).toBeNull()
  })
})
```

- [ ] **Step 5.2: Run the test to verify failure**

```bash
pnpm test src/utils/storage.test.ts
```

Expected: FAIL — `Cannot find module './storage'`.

- [ ] **Step 5.3: Implement `src/utils/storage.ts`**

```ts
const PREFIX = "syntheza:"

function key(k: string): string {
  return `${PREFIX}${k}`
}

export function loadString(k: string): string | null {
  try {
    return localStorage.getItem(key(k))
  } catch {
    return null
  }
}

export function saveString(k: string, value: string): boolean {
  try {
    localStorage.setItem(key(k), value)
    return true
  } catch {
    return false
  }
}

export function load<T>(k: string): T | null {
  let raw: string | null = null
  try {
    raw = loadString(k)
    return raw === null ? null : (JSON.parse(raw) as T)
  } catch {
    return (raw as unknown as T) ?? null
  }
}

export function save(k: string, value: unknown): boolean {
  try {
    return saveString(k, JSON.stringify(value))
  } catch {
    return false
  }
}

export function remove(k: string): void {
  try {
    localStorage.removeItem(key(k))
  } catch {}
}

export function clear(): void {
  try {
    const toRemove: string[] = []
    for (let i = 0; i < localStorage.length; i++) {
      const k = localStorage.key(i)
      if (k && k.startsWith(PREFIX)) toRemove.push(k)
    }
    toRemove.forEach((k) => localStorage.removeItem(k))
  } catch {}
}
```

- [ ] **Step 5.4: Run tests, expect green**

```bash
pnpm test src/utils/storage.test.ts
```

Expected: 5 passing.

- [ ] **Step 5.5: Create `src/config/index.ts`**

```ts
interface AppConfig {
  API_URL: string
  ENV: "development" | "production"
}

const API_URL = (import.meta.env.VITE_API_URL as string | undefined) ?? "https://api.syntheza.ovh/"

const Config: AppConfig = {
  API_URL: API_URL.endsWith("/") ? API_URL : `${API_URL}/`,
  ENV: import.meta.env.PROD ? "production" : "development",
}

export default Config
```

- [ ] **Step 5.6: Create `src/config/google.ts`**

```ts
export const GOOGLE_CONFIG = {
  webClientId: (import.meta.env.VITE_GOOGLE_WEB_CLIENT_ID as string | undefined) ?? "",
}
```

- [ ] **Step 5.7: Create `.env.example`**

```
VITE_API_URL=https://api.syntheza.ovh/
VITE_GOOGLE_WEB_CLIENT_ID=
```

- [ ] **Step 5.8: Create `.env` (gitignored) for local dev**

```
VITE_API_URL=https://api.syntheza.ovh/
VITE_GOOGLE_WEB_CLIENT_ID=
```

- [ ] **Step 5.9: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): storage util (localStorage), Config, Google env"
```

---

## Phase 6 — API client + auth services (apisauce + refresh queue)

**Files:**
- Install: `apisauce`
- Create: `src/services/api/types.ts`, `src/services/api/apiProblem.ts`, `src/services/api/index.ts`, `src/services/auth/tokenService.ts`, `src/services/auth/authTypes.ts`, `src/services/auth/authService.ts`

- [ ] **Step 6.1: Install apisauce**

```bash
pnpm add apisauce@3.1.1
```

- [ ] **Step 6.2: Create `src/services/api/types.ts`**

```ts
export interface ApiConfig {
  url: string
  timeout: number
}
```

- [ ] **Step 6.3: Create `src/services/api/apiProblem.ts` (copied verbatim from mobile)**

```ts
import type { ApiResponse } from "apisauce"

export type GeneralApiProblem =
  | { kind: "timeout"; temporary: true }
  | { kind: "cannot-connect"; temporary: true }
  | { kind: "server" }
  | { kind: "unauthorized" }
  | { kind: "forbidden" }
  | { kind: "not-found" }
  | { kind: "rejected" }
  | { kind: "unknown"; temporary: true }
  | { kind: "bad-data" }

export function getGeneralApiProblem(response: ApiResponse<unknown>): GeneralApiProblem | null {
  switch (response.problem) {
    case "CONNECTION_ERROR":
      return { kind: "cannot-connect", temporary: true }
    case "NETWORK_ERROR":
      return { kind: "cannot-connect", temporary: true }
    case "TIMEOUT_ERROR":
      return { kind: "timeout", temporary: true }
    case "SERVER_ERROR":
      return { kind: "server" }
    case "UNKNOWN_ERROR":
      return { kind: "unknown", temporary: true }
    case "CLIENT_ERROR":
      switch (response.status) {
        case 401:
          return { kind: "unauthorized" }
        case 403:
          return { kind: "forbidden" }
        case 404:
          return { kind: "not-found" }
        default:
          return { kind: "rejected" }
      }
    case "CANCEL_ERROR":
      return null
  }
  return null
}
```

- [ ] **Step 6.4: Create `src/services/auth/authTypes.ts`**

```ts
export interface LoginRequest {
  email: string
  password: string
}

export interface RegisterRequest {
  name: string
  email: string
  password: string
}

export interface ForgotPasswordRequest {
  email: string
}

export interface ResetPasswordRequest {
  token: string
  password: string
}

export interface AuthUser {
  id: string
  email: string
  name: string
  avatar?: string
  bio?: string
}

export interface ApiErrorResponse {
  success: false
  error: { message: string }
}

export type LoginResult =
  | { kind: "ok"; token: string; refreshToken?: string; user: AuthUser }
  | { kind: "error"; message: string }

export type RegisterResult =
  | { kind: "ok"; token: string; refreshToken?: string; user: AuthUser }
  | { kind: "error"; message: string }

export type LogoutResult = { kind: "ok" } | { kind: "error"; message: string }

export type ForgotPasswordResult =
  | { kind: "ok"; message: string }
  | { kind: "error"; message: string }

export type ResetPasswordResult =
  | { kind: "ok"; message: string }
  | { kind: "error"; message: string }
```

- [ ] **Step 6.5: Create `src/services/auth/tokenService.ts` (web variant — localStorage instead of SecureStore)**

```ts
import { load, save, remove } from "@/utils/storage"
import type { AuthUser } from "./authTypes"

const KEYS = {
  TOKEN: "auth.token",
  REFRESH_TOKEN: "auth.refreshToken",
  USER: "auth.user",
} as const

export const tokenService = {
  saveToken: async (token: string): Promise<void> => {
    save(KEYS.TOKEN, token)
  },

  getToken: async (): Promise<string | null> => load<string>(KEYS.TOKEN),

  saveRefreshToken: async (token: string): Promise<void> => {
    save(KEYS.REFRESH_TOKEN, token)
  },

  getRefreshToken: async (): Promise<string | null> => load<string>(KEYS.REFRESH_TOKEN),

  saveUser: (user: AuthUser): void => {
    save(KEYS.USER, user)
  },

  getUser: (): AuthUser | null => load<AuthUser>(KEYS.USER),

  isAuthenticated: async (): Promise<boolean> => {
    const token = load<string>(KEYS.TOKEN)
    return !!token
  },

  clearAll: async (): Promise<void> => {
    remove(KEYS.TOKEN)
    remove(KEYS.REFRESH_TOKEN)
    remove(KEYS.USER)
  },
}
```

- [ ] **Step 6.6: Create `src/services/api/index.ts` (refresh queue interceptor, web variant)**

```ts
import { create, type ApisauceInstance } from "apisauce"
import Config from "@/config"
import type { ApiConfig } from "./types"

export const DEFAULT_API_CONFIG: ApiConfig = {
  url: Config.API_URL,
  timeout: 10000,
}

type AuthFailureHandler = () => void | Promise<void>

export class Api {
  apisauce: ApisauceInstance
  config: ApiConfig
  private isRefreshing = false
  private refreshQueue: Array<{ resolve: (token: string) => void; reject: (err: Error) => void }> =
    []
  private onAuthFailure: AuthFailureHandler | null = null

  constructor(config: ApiConfig = DEFAULT_API_CONFIG) {
    this.config = config
    this.apisauce = create({
      baseURL: this.config.url,
      timeout: this.config.timeout,
      headers: {
        Accept: "application/json",
        "Content-Type": "application/json",
      },
    })
    this.setupInterceptor()
  }

  setAuthToken(token: string): void {
    this.apisauce.setHeader("Authorization", `Bearer ${token}`)
  }

  clearAuthToken(): void {
    this.apisauce.deleteHeader("Authorization")
  }

  setAuthFailureHandler(handler: AuthFailureHandler | null): void {
    this.onAuthFailure = handler
  }

  private async triggerAuthFailure(): Promise<void> {
    if (this.onAuthFailure) {
      try {
        await this.onAuthFailure()
      } catch {}
    }
  }

  private setupInterceptor(): void {
    this.apisauce.axiosInstance.interceptors.response.use(
      (response) => response,
      async (error) => {
        const originalRequest = error.config
        if (error.response?.status !== 401 || originalRequest._retry) {
          return Promise.reject(error)
        }

        const url: string | undefined = originalRequest?.url
        if (url && url.includes("/api/user/refresh")) {
          return Promise.reject(error)
        }

        if (this.isRefreshing) {
          return new Promise((resolve, reject) => {
            this.refreshQueue.push({
              resolve: (token: string) => {
                originalRequest.headers.Authorization = `Bearer ${token}`
                resolve(this.apisauce.axiosInstance(originalRequest))
              },
              reject,
            })
          })
        }

        originalRequest._retry = true
        this.isRefreshing = true

        try {
          const { tokenService } = await import("@/services/auth/tokenService")
          const refreshToken = await tokenService.getRefreshToken()
          if (!refreshToken) throw new Error("No refresh token")

          const base = this.config.url.endsWith("/") ? this.config.url : `${this.config.url}/`
          const response = await this.apisauce.axiosInstance.post(
            `${base}api/user/refresh`,
            { refreshToken },
            { headers: { "X-Refresh-Token": refreshToken } },
          )

          const newToken = response.data?.data?.token
          const newRefreshToken = response.data?.data?.refreshToken
          if (!newToken) throw new Error("Refresh failed")

          await tokenService.saveToken(newToken)
          if (newRefreshToken && newRefreshToken !== refreshToken) {
            await tokenService.saveRefreshToken(newRefreshToken)
          }
          this.setAuthToken(newToken)

          this.refreshQueue.forEach((req) => req.resolve(newToken))
          this.refreshQueue = []

          originalRequest.headers.Authorization = `Bearer ${newToken}`
          return this.apisauce.axiosInstance(originalRequest)
        } catch (refreshError) {
          this.refreshQueue.forEach((req) => req.reject(refreshError as Error))
          this.refreshQueue = []
          const { tokenService } = await import("@/services/auth/tokenService")
          await tokenService.clearAll()
          this.clearAuthToken()
          await this.triggerAuthFailure()
          return Promise.reject(refreshError)
        } finally {
          this.isRefreshing = false
        }
      },
    )
  }
}

export const api = new Api()
```

- [ ] **Step 6.7: Create `src/services/auth/authService.ts` (copied from mobile)**

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  AuthUser,
  LoginRequest,
  RegisterRequest,
  ForgotPasswordRequest,
  ResetPasswordRequest,
  ApiErrorResponse,
  LoginResult,
  RegisterResult,
  LogoutResult,
  ForgotPasswordResult,
  ResetPasswordResult,
} from "./authTypes"

interface AuthApiResponse {
  success: boolean
  data: { token: string; refreshToken?: string; user: AuthUser & { avatarUrl?: string } }
}

function normalizeUser(raw: AuthApiResponse["data"]["user"]): AuthUser {
  return {
    id: String(raw.id),
    email: raw.email,
    name: raw.name,
    avatar: raw.avatar || raw.avatarUrl,
    bio: raw.bio,
  }
}

export const authService = {
  login: async (credentials: LoginRequest): Promise<LoginResult> => {
    const response = await api.apisauce.post<AuthApiResponse | ApiErrorResponse>(
      "/api/user/login",
      credentials,
    )
    if (response.ok && response.data && "data" in response.data && response.data.data?.token) {
      return {
        kind: "ok",
        token: response.data.data.token,
        refreshToken: response.data.data.refreshToken,
        user: normalizeUser(response.data.data.user),
      }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  register: async (data: RegisterRequest): Promise<RegisterResult> => {
    const response = await api.apisauce.post<AuthApiResponse | ApiErrorResponse>(
      "/api/user/signup",
      data,
    )
    if (response.ok && response.data && "data" in response.data && response.data.data?.token) {
      return {
        kind: "ok",
        token: response.data.data.token,
        refreshToken: response.data.data.refreshToken,
        user: normalizeUser(response.data.data.user),
      }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  logout: async (): Promise<LogoutResult> => {
    const response = await api.apisauce.delete("/api/user/logout")
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  forgotPassword: async (data: ForgotPasswordRequest): Promise<ForgotPasswordResult> => {
    const response = await api.apisauce.post<{ success: boolean; message: string }>(
      "/api/user/forgot-password",
      data,
    )
    if (response.ok && response.data) {
      return { kind: "ok", message: response.data.message ?? "Reset email sent" }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  resetPassword: async (data: ResetPasswordRequest): Promise<ResetPasswordResult> => {
    const response = await api.apisauce.post<{ success: boolean; message: string }>(
      "/api/user/reset-password",
      data,
    )
    if (response.ok && response.data) {
      return { kind: "ok", message: response.data.message ?? "Password reset" }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  googleAuth: async (idToken: string): Promise<LoginResult> => {
    const response = await api.apisauce.post<AuthApiResponse | ApiErrorResponse>(
      "/api/user/google",
      { idToken },
    )
    if (response.ok && response.data && "data" in response.data && response.data.data?.token) {
      return {
        kind: "ok",
        token: response.data.data.token,
        refreshToken: response.data.data.refreshToken,
        user: normalizeUser(response.data.data.user),
      }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 6.8: Run typecheck**

```bash
pnpm typecheck
```

Expected: 0 errors.

- [ ] **Step 6.9: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): apisauce client + refresh queue + auth service"
```

---

(continued in next file — phases 7 through 23 in Part 2 of this plan)

See: `2026-05-20-frontend-web-rebuild-v2-part2.md`

