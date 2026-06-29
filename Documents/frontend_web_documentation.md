# Syntheza Frontend Web — Documentation Technique

**Version** : 2.0.0
**Stack** : React 19.2 / TypeScript 5.9.3 / Vite 6.4 / Tailwind CSS 4 / React Query 5
**Date** : Juin 2026

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Stack technique](#2-stack-technique)
3. [Architecture des dossiers](#3-architecture-des-dossiers)
4. [Routing & pages](#4-routing--pages)
5. [Client API](#5-client-api)
6. [Services (couche données)](#6-services-couche-données)
7. [Gestion d'état & auth](#7-gestion-détat--auth)
8. [Panel Admin](#8-panel-admin)
9. [Design system & i18n](#9-design-system--i18n)
10. [Tests](#10-tests)
11. [Build & déploiement](#11-build--déploiement)
12. [Lancer en local](#12-lancer-en-local)

---

## 1. Vue d'ensemble

Syntheza Frontend Web est la version navigateur de la plateforme de veille stratégique Syntheza. Elle permet aux utilisateurs de :

- S'authentifier (email/password + Google OAuth)
- Parcourir un feed d'articles personnalisé avec scroll infini
- Rechercher des articles et des utilisateurs
- Découvrir des articles tendance et des éditeurs (publishers)
- Gérer son profil (avatar, bio, abonnements, bookmarks)
- Interagir avec les articles : likes, commentaires, bookmarks, follow
- Consulter les scores de confiance (Trust Factor) des articles
- Personnaliser l'apparence (thème clair/sombre/système)
- Gérer ses paramètres (compte, confidentialité, notifications, sécurité)
- Administrer la plateforme via un panel ADMIN réservé aux rôles élevés

### Stack technique

| Couche | Technologie |
|--------|------------|
| Framework UI | React 19.2.0 |
| Langage | TypeScript 5.9.3 (strict) |
| Bundler | Vite 6.4.2 |
| Routing | React Router DOM 7.15.1 |
| State (serveur) | TanStack React Query 5.100.11 |
| State (client) | Zustand 5.0.13 |
| HTTP | Apisauce 3.1.1 (wrapper axios) |
| UI primitives | Radix UI (avatar, dialog, dropdown, switch, tabs, toast, tooltip) |
| Icônes | Lucide React 0.460.0 |
| Charts | Recharts 2.15.4 |
| Animations | Framer Motion 12.39.0 |
| CSS | Tailwind CSS 4.3.0 + CSS Variables |
| Fonts | Space Grotesk (@fontsource/space-grotesk 5.2.10) |
| i18n | i18next 23.16.8 + react-i18next 15.7.4 |
| Auth Google | @react-oauth/google 0.13.5 |
| Tests | Vitest 3.2.4 + Testing Library React 16.3.2 |
| Stockage local | localStorage (abstraction via `utils/storage.ts`) |
| Gestionnaire de paquets | pnpm 10.11.0 |

---

## 2. Stack technique

### Variables d'environnement

| Variable | Valeur par défaut | Description |
|----------|-------------------|-------------|
| `VITE_API_URL` | `https://api.syntheza.ovh/` | URL de l'API backend |
| `VITE_GOOGLE_WEB_CLIENT_ID` | — | Client ID Google OAuth (optionnel) |

Le fichier `src/config/index.ts` centralise la lecture de ces variables et expose un objet `Config` typé (`API_URL`, `ENV`). Si `VITE_API_URL` est absent, l'URL de production est utilisée par défaut.

### Configuration Vite

- Alias `@` → `./src` pour tous les imports
- Proxy `/uploads` → `VITE_API_URL` en développement (évite les problèmes CORS pour les avatars)
- Port dev : 3000 (strictPort)
- Build : target `es2022`, sourcemaps activées, seuil d'avertissement bundle à 800 KB

---

## 3. Architecture des dossiers

```
frontend-web/
├── Dockerfile                        # Build multi-stage (builder → nginx)
├── docker-compose.prod.yml           # Production (Traefik + frontend)
├── nginx.conf                        # Config nginx (SPA fallback, gzip, cache headers)
├── .github/workflows/deploy.yml      # CI/CD GitHub Actions
├── package.json                      # Dependencies & scripts
├── vite.config.ts                    # Config Vite (alias, proxy, build)
├── vitest.config.ts                  # Config tests (jsdom, globals, coverage v8)
├── tailwind.config.ts                # Tailwind (darkMode class, CSS variables)
├── tsconfig.json                     # TypeScript strict
├── postcss.config.js                 # PostCSS (autoprefixer, @tailwindcss/postcss)
├── public/
│   ├── favicon.png
│   └── logo.png
└── src/
    ├── main.tsx                      # Entry point (React DOM, i18n import)
    ├── App.tsx                       # Composant racine (render Root)
    ├── globals.css                   # CSS variables design tokens + Tailwind import
    ├── config/                       # Lecture VITE_* env vars
    ├── contexts/
    │   └── AuthContext.tsx           # Auth state global (React Context)
    ├── stores/
    │   └── preferencesStore.ts       # Zustand store des préférences utilisateur
    ├── hooks/
    │   ├── queryKeys.ts              # Clés React Query centralisées
    │   ├── useDebounce.ts            # Debounce générique
    │   ├── useInView.ts              # Intersection Observer (scroll infini)
    │   ├── useIsAdmin.ts             # Dérive isAdmin depuis AuthContext
    │   └── useTheme.ts              # Gestion thème clair/sombre/système
    ├── services/                     # Couche données (19 modules)
    │   ├── api/                      # Client Apisauce + intercepteur refresh
    │   ├── auth/                     # authService + tokenService + types
    │   ├── admin/                    # adminService + adminPublisherService + systemService
    │   ├── avatar/                   # avatarService
    │   ├── bookmark/                 # bookmarkService
    │   ├── comment/                  # commentService
    │   ├── feed/                     # feedService
    │   ├── follow/                   # followService
    │   ├── like/                     # likeService
    │   ├── notification/             # notificationService
    │   ├── preferences/              # preferencesService
    │   ├── publisher/                # publisherService
    │   ├── publisherSubscription/    # publisherSubscriptionService
    │   ├── search/                   # searchService
    │   ├── stats/                    # statsService
    │   ├── trust/                    # trustService
    │   └── user/                     # userService
    ├── routes/                       # Pages (file-based par convention)
    │   ├── root.tsx                  # Arbre de routes React Router
    │   ├── (auth)/                   # Layout auth + 4 pages (non protégées)
    │   └── (app)/                    # Layout app + pages protégées
    │       ├── admin/                # 6 pages panel admin (ADMIN only)
    │       └── settings/             # 6 pages paramètres
    ├── components/                   # 27 composants partagés
    │   ├── admin/                    # 25 composants spécifiques admin
    │   └── index.ts                  # Barrel export
    ├── design/                       # Tokens TS (colors.ts, typography.ts)
    ├── i18n/                         # Traductions (fr.ts, index.ts, types.ts)
    ├── lib/
    │   ├── queryClient.ts            # Instance QueryClient configurée
    │   └── utils.ts                  # Helpers CSS (cn = clsx + tailwind-merge)
    ├── utils/
    │   ├── avatarUrl.ts              # Résolution URL avatar (relative → absolue)
    │   ├── formatDate.ts             # Formatage dates
    │   └── storage.ts                # Abstraction localStorage (prefixe "syntheza:")
    └── test/
        └── setup.ts                  # Setup Vitest (@testing-library/jest-dom)
```

---

## 4. Routing & pages

### Structure React Router

Le routing est déclaré dans `src/routes/root.tsx` avec React Router DOM 7. Deux groupes distincts séparent les flux :

- **`GuestRoute`** : redirige vers `/home` si déjà authentifié
- **`ProtectedRoute`** : redirige vers `/login` si non authentifié
- **`AdminRoute`** : en plus de `ProtectedRoute`, vérifie `user.role === "ADMIN"`

La racine `/` redirige vers `/home`. Toute route inconnue (`*`) fait de même.

### Pages (auth)

| Chemin | Page | Description |
|--------|------|-------------|
| `/login` | LoginPage | Formulaire email/password + bouton Google OAuth |
| `/register` | RegisterPage | Création de compte (nom, email, password) |
| `/forgot-password` | ForgotPasswordPage | Demande de reset par email |
| `/reset-password` | ResetPasswordPage | Saisie du nouveau password (token URL) |

### Pages (app — protégées)

| Chemin | Page | Description |
|--------|------|-------------|
| `/home` | HomePage | Feed infini d'articles personnalisés |
| `/discover` | DiscoverPage | Articles tendance/récents + liste des publishers |
| `/search` | SearchPage | Recherche d'articles et d'utilisateurs |
| `/notifications` | NotificationsPage | Liste des notifications avec marquage lu |
| `/profile` | ProfilePage | Profil de l'utilisateur connecté |
| `/user/:id` | UserProfilePage | Profil d'un autre utilisateur |
| `/article/:id` | ArticleDetailPage | Détail article (contenu, trust, likes, commentaires) |
| `/settings` | redirect → `/settings/account` | |
| `/settings/account` | AccountSettingsPage | Nom, email, bio, avatar |
| `/settings/privacy` | PrivacySettingsPage | Visibilité profil, messages |
| `/settings/appearance` | AppearanceSettingsPage | Thème, langue, taille texte |
| `/settings/notifications` | NotificationsSettingsPage | Notifications email/push |
| `/settings/security` | SecuritySettingsPage | Mot de passe, 2FA |
| `/settings/data-privacy` | DataPrivacySettingsPage | Données et vie privée |

### Pages (admin — ADMIN only)

| Chemin | Page | Description |
|--------|------|-------------|
| `/admin` | AdminDashboardPage | KPIs, sparklines, donut rôles, tops, activité |
| `/admin/users` | AdminUsersPage | Liste et recherche des utilisateurs |
| `/admin/users/:id` | AdminUserDetailPage | Détail utilisateur, actions CRUD |
| `/admin/publishers` | AdminPublishersPage | Liste des publishers |
| `/admin/publishers/:id` | AdminPublisherDetailPage | Détail publisher, canaux RSS |
| `/admin/system` | AdminSystemPage | Toggle crons ON/OFF, historique exécutions, jauge conso IA |

### Layout applicatif

Le `AppLayout` affiche une sidebar gauche fixe (245px sur `lg`, 260px sur `xl`) avec :
- Logo Syntheza
- 5 liens de navigation (icône Lucide + label)
- Badge de notifications non lues (polling 60s via React Query)
- Lien admin conditionnel si `isAdmin`
- Toggle thème clair/sombre
- Avatar utilisateur + logout

Un `RightSidebar` est rendu à droite sur les grands écrans.

---

## 5. Client API

### Instance Apisauce

Le fichier `src/services/api/index.ts` expose un singleton `api` (classe `Api`) construit sur Apisauce (wrapper axios).

```
baseURL   : VITE_API_URL (défaut : https://api.syntheza.ovh/)
timeout   : 10 000 ms
headers   : Accept: application/json, Content-Type: application/json
```

### Intercepteur de refresh token

L'intercepteur axios gère automatiquement les 401 :

```
1. Requête reçoit un 401
   └── Si l'URL est /api/user/refresh → reject immédiat (évite boucle infinie)
   └── Si isRefreshing = true → mise en file d'attente (refreshQueue)

2. Tentative de refresh
   └── Lit le refreshToken depuis localStorage (via tokenService)
   └── POST /api/user/refresh avec { refreshToken }
   └── Récupère le nouveau token
   └── Sauvegarde le nouveau token + met à jour l'en-tête Authorization

3. En cas d'échec du refresh
   └── Vide la refreshQueue (reject toutes les requêtes en attente)
   └── Supprime les tokens (tokenService.clearAll)
   └── Appelle onAuthFailure → redirige vers /login
```

### Format de réponse API

Tous les services attendent la structure uniforme du backend :

```typescript
// Succès
{ success: true, data: { ... } }

// Erreur
{ success: false, error: { message: "..." } }
```

Les services retournent un discriminated union `{ kind: "ok", data: ... } | { kind: "error", message: string }`.

### Gestion des erreurs réseau

`apiProblem.ts` mappe les codes HTTP et les problèmes réseau en types sémantiques : `timeout`, `cannot-connect`, `server`, `unauthorized`, `forbidden`, `not-found`, `rejected`, `unknown`.

---

## 6. Services (couche données)

Chaque domaine fonctionnel possède son répertoire `src/services/<domaine>/` contenant au minimum un fichier `*Service.ts` et un fichier `*Types.ts`. Les services n'ont aucune dépendance vers les composants UI — ils communiquent exclusivement via le singleton `api`.

### Services disponibles

| Service | Endpoints backend consommés |
|---------|----------------------------|
| `authService` | POST /api/user/login, /signup, /google, /forgot-password, /reset-password, DELETE /logout |
| `tokenService` | (localStorage uniquement — gestion tokens/user) |
| `userService` | GET /api/user/me, /api/user/:id, PUT /api/user/me, POST /api/user/me/avatar |
| `feedService` | GET /api/feed, /api/feed/item/:id, /api/feed/digest |
| `searchService` | GET /api/search, /api/user/search |
| `likeService` | POST /api/likes/:id/toggle, GET /api/likes/:id/count |
| `commentService` | GET/POST /api/comments/item/:id, PUT/DELETE /api/comments/:id |
| `bookmarkService` | POST /api/bookmarks/:id/toggle, GET /api/bookmarks |
| `notificationService` | GET /api/notifications, /unread-count, PATCH read/read-all, DELETE |
| `followService` | POST /api/follow/:id/toggle, GET followers/following/counts/is-following |
| `publisherService` | GET /api/publishers, /api/publishers/:id |
| `publisherSubscriptionService` | POST /api/publisher-subscriptions/:id/toggle, GET / |
| `preferencesService` | GET/PATCH /api/preferences |
| `trustService` | GET /api/trust/:id, POST /api/trust/:id/analyze |
| `avatarService` | (résolution URL avatar) |
| `statsService` | GET /api/admin/stats/overview, /api/admin/stats/analytics |
| `adminService` | CRUD /api/admin/users/* (list, get, update, role, reset-pwd, set-pwd, delete) |
| `adminPublisherService` | CRUD /api/admin via publishers endpoint |
| `systemService` | GET/PATCH /api/admin/crons, POST /crons/:name/run, GET /crons/:name/runs, GET /api/admin/ai-usage, POST /test-notification |

---

## 7. Gestion d'état & auth

### Auth (React Context)

`AuthContext` est le seul provider d'état d'authentification. Il est monté à la racine de l'arbre de routes (sous `BrowserRouter`) et expose :

```typescript
{
  user: AuthUser | null       // { id, email, name, role, avatar?, bio? }
  isAuthenticated: boolean
  isLoading: boolean          // true pendant la restauration de session au démarrage
  login(credentials): Promise<{ error? }>
  register(data): Promise<{ error? }>
  forgotPassword(data): Promise<{ error?, success? }>
  loginWithGoogle(idToken): Promise<{ error? }>
  logout(): Promise<void>
}
```

#### Restauration de session au démarrage

Au montage du provider :
1. Lit `user` et `token` depuis localStorage (via `tokenService`)
2. Si les deux existent, configure le header `Authorization: Bearer <token>`
3. Appelle `GET /api/user/me` pour valider le token et obtenir les données à jour
4. Si l'appel échoue, vide les tokens et passe à l'état déconnecté

#### Persistance des tokens

`tokenService` utilise `localStorage` avec le préfixe `syntheza:` (via `utils/storage.ts`) pour trois clés :

| Clé stockée | Contenu |
|-------------|---------|
| `syntheza:auth.token` | JWT access token |
| `syntheza:auth.refreshToken` | JWT refresh token |
| `syntheza:auth.user` | Objet `AuthUser` sérialisé |

### Préférences (Zustand)

`preferencesStore` (Zustand) gère les préférences utilisateur :

- **`fetchPreferences()`** : charge depuis `GET /api/preferences`
- **`updatePreference(key, value)`** : optimistic update local + `PATCH /api/preferences`. En cas d'erreur, rollback automatique vers la valeur précédente.

Le store est alimenté par `AppLayout` au montage si l'utilisateur est authentifié.

### Thème (hook `useTheme`)

`useTheme` coordonne le thème entre trois sources de vérité :
1. **localStorage** (`syntheza:ui.theme`) — persistance locale
2. **Préférences serveur** — synchronisées si différentes du local
3. **`prefers-color-scheme`** — écouté en temps réel si mode `system`

La résolution finale toggle la classe `dark` sur `document.documentElement` (Tailwind dark mode basé sur classe).

### React Query (TanStack)

`queryClient` est configuré avec :
- `staleTime` : 5 minutes
- `gcTime` : 10 minutes
- `refetchOnWindowFocus` : désactivé
- `retry` (queries) : 1 / `retry` (mutations) : 0

Toutes les query keys sont centralisées dans `src/hooks/queryKeys.ts` pour éviter les collisions.

---

## 8. Panel Admin

Le panel admin est accessible uniquement aux utilisateurs avec `role === "ADMIN"`. Il est protégé par `AdminRoute` (vérifie `useIsAdmin()`) en plus de `ProtectedRoute`.

### Dashboard (`/admin`)

Données chargées via `statsService` :
- **`getOverview()`** → KPIs (total users, items, publishers, likes), tops publishers/articles
- **`getAnalytics(period)`** → métriques par période (`24h`, `7d`, `30d`, `90d`)

Composants dashboard :
- `KpiCard` + `KpiCardSkeleton` — métrique chiffrée avec tendance
- `Sparkline` + `SparklineCardSkeleton` — graphique mini (Recharts)
- `PeriodFilter` — sélecteur de période
- `RoleDistributionChart` + `RoleDistributionSkeleton` — donut chart répartition des rôles
- `TopList` + `TopListSkeleton` — top articles ou publishers
- `ActivityFeed` + `ActivityFeedSkeleton` — activité récente

Polling automatique toutes les 60 secondes pour les données overview.

### Gestion des utilisateurs (`/admin/users`, `/admin/users/:id`)

Via `adminService` : liste, détail, modification des infos, changement de rôle, reset password par email, forçage de password, suppression.

Dialogs Radix UI : `ChangeRoleDialog`, `SetPasswordDialog`, `DeleteUserDialog`.

Composants : `UsersTable` + `UsersTableSkeleton`, `RoleBadge`, `StatusBadge`, `UserDetailSkeleton`.

### Gestion des publishers (`/admin/publishers`, `/admin/publishers/:id`)

Via `adminPublisherService` : liste, détail, création, modification, suppression.

Dialogs : `AddPublisherDialog`, `EditPublisherDialog`, `DeletePublisherDialog`.

Composant : `PublishersTable`.

### Système (`/admin/system`)

Via `systemService` :
- **Crons** : liste tous les jobs (`GET /api/admin/crons`), toggle ON/OFF (`PATCH /api/admin/crons/:name`), lancement manuel (`POST /api/admin/crons/:name/run`), historique des exécutions (`GET /api/admin/crons/:name/runs`), édition de fréquence (schedule).
- **Conso IA** : jauge d'utilisation quotidienne (`GET /api/admin/ai-usage`), alerte visuelle à 80%.
- **Notification de test** : `POST /api/admin/test-notification`.

Polling toutes les 30 secondes pour crons et ai-usage.

---

## 9. Design system & i18n

### Tokens de design (CSS Variables)

Le design system repose sur des CSS custom properties déclarées dans `src/globals.css` et consommées par Tailwind via `tailwind.config.ts`. Deux palettes complètes sont définies : `:root` (thème clair) et `.dark` (thème sombre).

| Token | Clair | Sombre | Usage |
|-------|-------|--------|-------|
| `--bg` | `#ffffff` | `#000000` | Fond principal |
| `--bg2` | `#fafafa` | `#121212` | Fond secondaire (body) |
| `--surface` | `#f8f8f8` | `#1a1a1a` | Cartes, panels |
| `--surface2` | `#f8f9fa` | `#262626` | Surfaces imbriquées |
| `--border` | `#dbdbdb` | `#363636` | Bordures principales |
| `--color` | `#262626` | `#f5f5f5` | Texte principal |
| `--color2` | `#8e8e8e` | `#a8a8a8` | Texte secondaire |
| `--primary` | `#262626` | `#f5f5f5` | Boutons principaux |
| `--primary-text` | `#ffffff` | `#000000` | Texte sur boutons primaires |
| `--danger` | `#ed4956` | `#ed4956` | Erreurs, suppressions |
| `--success` | `#22c55e` | `#22c55e` | Confirmations |
| `--gradient-start` | `#667eea` | `#667eea` | Gradient de marque |
| `--gradient-end` | `#764ba2` | `#764ba2` | Gradient de marque |

Radii : `--radius-sm` (6px), `--radius-md` (8px), `--radius-lg` (12px), `--radius-xl` (16px), `--radius-2xl` (22px).

### Typographie

- **Police** : Space Grotesk (300→700, latine + étendue + vietnamienne), chargée via `@fontsource/space-grotesk`
- **Wordmark** : classe utilitaire `wordmark` (font-weight 800, letter-spacing -0.04em)
- Rendu : `-webkit-font-smoothing: antialiased`, `text-rendering: optimizeLegibility`

### Composants partagés (src/components/)

27 composants dans le dossier racine de `components/` :

`ArticleCard`, `AutoImage`, `Avatar`, `Badge`, `CommentInput`, `CommentItem`, `EmptyState`, `IconButton`, `Logo`, `NotificationItem`, `ProfileHeader`, `PublisherCard`, `RightSidebar`, `SearchBar`, `SectionHeader`, `SettingsRow`, `SettingsToggle`, `Skeleton`, `TabSelector`, `ThemeCard`, `Toast`, `TrustBadge`, `TrustDetailSheet`, `UserRow`, `AdminRoute`, `ProtectedRoute` + `GuestRoute`.

`ArticleCard` comprend un squelette de chargement (`ArticleCardSkeleton`) colocalisé.

### Internationalisation (i18n)

i18next configuré uniquement avec le français (`lng: "fr"`, `fallbackLng: "fr"`). Un seul fichier de traductions : `src/i18n/fr.ts`.

Namespaces couverts : `common`, `auth`, `feed`, `discover`, `notifications`, `settings`, `trust`, `profile`.

Le `t()` hook de `react-i18next` est utilisé dans les composants. La langue n'est pas commutable dynamiquement depuis l'UI web (contrairement au mobile).

---

## 10. Tests

### Configuration

- **Framework** : Vitest 3.2.4
- **Environnement** : jsdom
- **Setup** : `src/test/setup.ts` (import `@testing-library/jest-dom`)
- **Globals** : activés (`describe`, `it`, `expect`, `vi` disponibles sans import)
- **Coverage** : provider v8, reporters text + html

### Résultat

**48 tests passants, 0 échec** (vérifié en exécution réelle).

### Fichiers de test (14 fichiers)

| Fichier | Domaine testé |
|---------|---------------|
| `components/ArticleCard.test.tsx` | Rendu du composant ArticleCard |
| `components/admin/KpiCard.test.tsx` | Rendu du composant KpiCard |
| `components/admin/UsersTable.test.tsx` | Rendu et interactions UsersTable |
| `services/admin/adminService.test.ts` | adminService (mocks axios) |
| `services/admin/adminPublisherService.test.ts` | adminPublisherService |
| `services/admin/systemService.test.ts` | systemService (crons, ai-usage) |
| `services/auth/authService.test.ts` | authService (login, register, google, logout) |
| `services/feed/feedService.test.ts` | feedService (getFeed, getItem, getDigest) |
| `services/like/likeService.test.ts` | likeService (toggle, count) |
| `services/publisherSubscription/publisherSubscriptionService.test.ts` | subscriptions |
| `services/stats/statsService.test.ts` | statsService (overview, analytics) |
| `stores/preferencesStore.test.ts` | Zustand store (fetch, update, rollback) |
| `utils/formatDate.test.ts` | Formatage dates |
| `utils/storage.test.ts` | localStorage abstraction |

### Scripts

```bash
pnpm test          # Vitest en mode run (--passWithNoTests)
pnpm test:watch    # Vitest en mode watch
```

---

## 11. Build & déploiement

### Dockerfile (multi-stage)

```
Stage 1 — builder (node:20-alpine)
  └── pnpm install --frozen-lockfile
  └── pnpm build (tsc --noEmit + vite build)
      Les variables VITE_API_URL et VITE_GOOGLE_WEB_CLIENT_ID
      sont injectées comme ARG Docker → ENV → disponibles à la compilation.

Stage 2 — runner (nginx:alpine)
  └── Copie dist/ vers /usr/share/nginx/html
  └── Applique nginx.conf
  └── Expose port 80
  └── Healthcheck wget sur http://localhost/
```

### Configuration nginx

- `try_files $uri $uri/ /index.html` — SPA fallback (React Router côté client)
- Gzip activé sur JS, CSS, JSON, XML
- Assets statiques (JS, CSS, fonts, images) : cache 1 an (`Cache-Control: public, immutable`)
- HTML principal : `no-cache, no-store, must-revalidate`
- En-têtes sécurité : `X-Frame-Options: SAMEORIGIN`, `X-Content-Type-Options: nosniff`, `X-XSS-Protection`

### CI/CD GitHub Actions (`.github/workflows/deploy.yml`)

Le pipeline se déclenche sur chaque push sur `main` en deux jobs séquentiels :

**Job `quality`** :
1. Setup Node 20 + pnpm 10.11.0
2. `pnpm install --frozen-lockfile`
3. `pnpm lint` (ESLint, zero warnings)
4. `pnpm typecheck` (tsc --noEmit)
5. `pnpm test` (Vitest)

**Job `build_and_deploy`** (conditionné à `quality`) :
1. Build de l'image Docker avec `VITE_API_URL` et `VITE_GOOGLE_WEB_CLIENT_ID` depuis les secrets GitHub
2. Push vers GitHub Container Registry (ghcr.io)
3. Copie de `docker-compose.prod.yml` sur le serveur via SCP
4. Connexion SSH → `docker pull` + `docker compose up -d`

### Infrastructure production

Le serveur frontend est `57.128.55.171`. Le `docker-compose.prod.yml` orchestre :

- **`traefik`** : reverse proxy Traefik v2.11, TLS Let's Encrypt automatique (`letsencrypt@syntheza.ovh`), routes sur `syntheza.ovh`
- **`frontend`** : image depuis ghcr.io, réseau `syntheza-network` (externe — partagé avec le serveur backend via Cloudflare)

---

## 12. Lancer en local

### Prérequis

- Node.js >= 20.0.0
- pnpm 10.11.0 (`corepack enable && corepack prepare pnpm@10.11.0 --activate`)

### Installation

```bash
cd frontend-web
pnpm install
```

### Variables d'environnement

Créer un fichier `.env` à la racine (voir `.env.example`) :

```env
VITE_API_URL=http://localhost:3001/
# Optionnel pour Google OAuth
VITE_GOOGLE_WEB_CLIENT_ID=<votre_client_id>
```

Sans `VITE_API_URL`, l'application pointe sur `https://api.syntheza.ovh/` par défaut.

### Démarrage

```bash
pnpm dev         # Vite dev server sur http://localhost:3000
pnpm build       # Build de production (typecheck + vite build)
pnpm preview     # Prévisualiser le build sur http://localhost:4173
pnpm typecheck   # Vérification TypeScript sans build
pnpm lint        # ESLint (zero warnings)
pnpm lint:fix    # ESLint avec auto-fix
pnpm format      # Prettier sur src/**
pnpm test        # Tests Vitest
pnpm test:watch  # Tests en mode watch
```

### Comptes de test (seed backend)

| Email | Mot de passe | Rôle |
|-------|-------------|------|
| `dev@syntheza.app` | `password123` | USER |
| `admin@syntheza.app` | `password123` | ADMIN |
| `test@syntheza.app` | `password123` | USER |
