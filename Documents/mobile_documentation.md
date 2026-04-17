# Syntheza Mobile — Documentation Technique

**Version** : MVP Mobile
**Stack** : Expo 54 / React Native 0.81.5 / React 19.1.0 / TypeScript / Tamagui
**Date** : Avril 2026

---

## Table des matieres

1. [Vue d'ensemble](#1-vue-densemble)
2. [Architecture & Structure](#2-architecture--structure)
3. [Navigation & Routing](#3-navigation--routing)
4. [Authentification](#4-authentification)
5. [Client API](#5-client-api)
6. [Services (Couche Donnees)](#6-services-couche-données)
7. [State Management](#7-state-management)
8. [Ecrans](#8-écrans)
9. [Composants Reutilisables](#9-composants-réutilisables)
10. [Design System](#10-design-system)
11. [Internationalisation](#11-internationalisation)
12. [Configuration & Environnement](#12-configuration--environnement)
13. [Testing](#13-testing)
14. [Build & Deploiement](#14-build--déploiement)
15. [Performance](#15-performance)
16. [Securite](#16-sécurité)

---

## 1. Vue d'ensemble

Syntheza Mobile est une application de veille informationnelle sociale permettant aux utilisateurs de :

- S'authentifier (email/password + Google OAuth)
- Parcourir un feed d'articles personnalise avec scroll infini
- Rechercher des articles et des utilisateurs
- Decouvrir des articles tendance/recents et des sources
- Recevoir des notifications avec compteur de non lues
- Gerer son profil (avatar, bio, articles, bookmarks, abonnements)
- Interagir : likes, commentaires, bookmarks, follow
- S'abonner a des sources
- Consulter les scores de confiance (Trust Factor) des articles
- Personnaliser l'apparence (theme sombre/clair, langue)
- Gerer ses parametres (compte, confidentialite, notifications, securite)

### Stack technique

| Couche | Technologie |
|--------|------------|
| Framework | Expo SDK 54, React Native 0.81.5 |
| Langage | TypeScript (strict) |
| Navigation | Expo Router (file-based routing) |
| UI | Tamagui v2.0.0-rc.17 + React Native natif |
| State (serveur) | TanStack React Query |
| State (client) | Zustand |
| HTTP | Apisauce (wrapper axios) |
| Stockage securise | expo-secure-store |
| Stockage local | react-native-mmkv |
| Fonts | SpaceGrotesk |
| i18n | i18next + react-i18next |
| Auth Google | @react-native-google-signin/google-signin |
| Animations | react-native-reanimated |
| Gestures | react-native-gesture-handler |
| JS Engine | Hermes |
| Architecture | React Native New Architecture (Fabric + TurboModules) |

---

## 2. Architecture & Structure

```
frontend-mobile/
├── app.json                          # Config Expo
├── package.json                      # Dependencies (pnpm 10.33.0, node >= 20)
├── tamagui.config.ts                 # Config theme Tamagui
├── babel.config.js                   # Babel
├── metro.config.js                   # Metro bundler
├── eas.json                          # EAS Build profiles
├── src/
│   ├── app/                          # Expo Router (file-based routes)
│   │   ├── _layout.tsx               # Root layout (providers)
│   │   ├── index.tsx                 # Splash/redirect auth
│   │   ├── (auth)/                   # Groupe auth (non protege)
│   │   │   ├── _layout.tsx
│   │   │   ├── login.tsx
│   │   │   ├── register.tsx
│   │   │   └── forgot-password.tsx
│   │   └── (app)/                    # Groupe app (protege)
│   │       ├── _layout.tsx
│   │       ├── (tabs)/               # Navigation par onglets
│   │       │   ├── _layout.tsx
│   │       │   ├── index.tsx         # Feed
│   │       │   ├── search.tsx        # Recherche
│   │       │   ├── discover.tsx      # Decouvrir
│   │       │   ├── notifications.tsx # Notifications
│   │       │   └── profile.tsx       # Profil
│   │       ├── article/[id].tsx      # Detail article
│   │       ├── user/[id].tsx         # Profil utilisateur
│   │       ├── user/[id]/followers.tsx
│   │       ├── user/[id]/following.tsx
│   │       └── settings/             # Parametres
│   │           ├── _layout.tsx
│   │           ├── index.tsx
│   │           ├── account.tsx
│   │           ├── privacy.tsx
│   │           ├── notifications.tsx
│   │           ├── appearance.tsx
│   │           ├── security.tsx
│   │           └── data-privacy.tsx
│   ├── components/                   # Composants UI reutilisables
│   ├── contexts/                     # React Context (Auth)
│   ├── services/                     # Couche API (14 services)
│   ├── stores/                       # Zustand stores
│   ├── hooks/                        # Custom hooks
│   ├── design/                       # Design tokens & theme
│   ├── config/                       # Configuration app
│   ├── i18n/                         # Traductions (7 langues)
│   ├── utils/                        # Utilitaires
│   ├── mocks/                        # Donnees mock
│   ├── devtools/                     # Reactotron (dev only)
│   └── tests/                        # Tests services
├── test/                             # Setup Jest
└── .maestro/                         # Tests UI Maestro
```

---

## 3. Navigation & Routing

### Expo Router (file-based routing)

L'application utilise Expo Router avec des typed routes.

#### Root Layout (`_layout.tsx`)

Initialise dans l'ordre :
1. Chargement des fonts (SpaceGrotesk)
2. Setup i18n + date-fns localization
3. Providers : `QueryClientProvider` → `SafeAreaProvider` → `TamaguiProvider` → `AuthProvider` → `KeyboardProvider`

#### Point d'entree (`index.tsx`)

- Utilisateur authentifie → redirect `/(app)`
- Utilisateur non authentifie → redirect `/(auth)/login`

#### Groupe Auth (`(auth)/`)

Stack navigator avec animation `slide_from_right` :
- `login` — Connexion email/password + Google OAuth
- `register` — Inscription avec validation
- `forgot-password` — Reset password par email

#### Groupe App (`(app)/`)

Protege par `AuthProvider` (redirect vers login si non authentifie).

**Tab Navigation (`(tabs)/`)** — 5 onglets :

| Onglet | Ecran | Icone | Description |
|--------|-------|-------|-------------|
| Accueil | `index.tsx` | home | Feed avec scroll infini |
| Recherche | `search.tsx` | search | Articles & utilisateurs |
| Decouvrir | `discover.tsx` | compass | Tendances, recents, sources |
| Notifications | `notifications.tsx` | bell + badge | Liste des notifications |
| Profil | `profile.tsx` | user | Mon profil |

**Ecrans supplementaires :**
- `article/[id]` — Detail article avec commentaires
- `user/[id]` — Profil d'un autre utilisateur
- `user/[id]/followers` — Liste des followers
- `user/[id]/following` — Liste des suivis
- `settings/*` — 7 ecrans de parametres

---

## 4. Authentification

### AuthContext (`contexts/AuthContext.tsx`)

**State :**
- `user` : objet utilisateur courant
- `isAuthenticated` : boolean
- `isLoading` : true pendant la restauration du token

**Methodes :**
- `login(email, password)` → LoginResult
- `register(name, email, password)` → RegisterResult
- `forgotPassword(email)` → ForgotPasswordResult
- `loginWithGoogle(idToken)` → LoginResult
- `logout()` → Clear token + user, redirect login

### Gestion des tokens (`services/auth/tokenService.ts`)

| Donnee | Stockage | Cle |
|--------|----------|-----|
| Token JWT | `expo-secure-store` (chiffre) | `auth.token` |
| User | MMKV (localStorage) | `auth.user` |

Au demarrage de l'app : restauration automatique du token + user depuis le stockage.

### Intercepteur de token

L'API client intercepte les reponses 401 :
1. Tente un refresh via `POST /api/user/refresh`
2. Si succes → met a jour le token, rejoue la requete
3. Si echec → logout + redirect login
4. File d'attente pour eviter les refreshs multiples simultanement

### Google OAuth (`hooks/useGoogleAuth.ts`)

- Utilise `@react-native-google-signin/google-signin`
- Gere gracieusement l'absence du module natif (fallback)
- Retourne : `isAvailable`, `isLoading`, `signIn()`

### Ecrans d'authentification

**Login :**
- Email + password + validation
- Bouton Google OAuth (si disponible)
- Lien "Mot de passe oublie ?"
- ActivityIndicator pendant le chargement

**Register :**
- Name, email, password, confirm password
- Validation : name requis, format email, password >= 8 chars, confirmation identique
- Feedback visuel (bordure rouge) sur mismatch password

**Forgot Password :**
- Input email + bouton envoi
- Etat de succes avec checkmark
- Bouton retour

---

## 5. Client API

### Configuration (`services/api/index.ts`)

- **Library** : Apisauce (wrapper axios)
- **Base URL** : depuis config (dev/prod)
- **Timeout** : 10 secondes
- **Headers** : `Accept: application/json`, `Content-Type: application/json`

### Fonctions utilitaires

```typescript
setAuthToken(token)    // Set Authorization: Bearer
clearAuthToken()       // Remove header
```

### Format de reponse

```typescript
interface ApiResponse<T> {
  success: boolean
  data: T
}
```

### Gestion d'erreurs (`services/api/apiProblem.ts`)

Categorisation des erreurs API :
- Erreur reseau (pas de connexion)
- Timeout (> 10s)
- Unauthorized (401) → trigger refresh
- Erreur serveur (5xx)
- Erreur inconnue

Tous les services retournent un type uniforme :
```typescript
type ServiceResult<T> =
  | { kind: "ok"; data: T }
  | { kind: "error"; message: string }
```

---

## 6. Services (Couche Donnees)

### Feed Service (`services/feed/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getFeed(params)` | `GET /api/feed?page=&limit=20&sortBy=` | `FeedResponse` (pagine) |
| `getItem(id)` | `GET /api/feed/item/:id` | `FeedItem` |
| `getDigest()` | `GET /api/feed/digest` | `DigestResponse \| null` |
| `getOverview()` | `GET /api/feed/overview` | `OverviewResponse` |

```typescript
interface FeedItem {
  id: number
  title: string
  content: string | null
  url: string | null
  author: string | null
  publishedAt: string | null
  imageUrl: string | null
  relevanceScore: number | null
  trustScore: number | null
  source: { id: number; name: string; type: string }
  summary: string | null
  likesCount: number
  commentsCount: number
  isLiked: boolean
  createdAt: string
}
```

### Search Service (`services/search/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `searchArticles(q, page, limit)` | `GET /api/search?q=&page=&limit=` | Articles pagines |
| `searchUsers(q, page, limit)` | `GET /api/user/search?q=&page=&limit=` | Users pagines |

### Like Service (`services/like/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(itemId)` | `POST /api/likes/:itemId/toggle` | `{ isLiked: boolean }` |
| `getCount(itemId)` | `GET /api/likes/:itemId/count` | `{ count: number }` |

### Bookmark Service (`services/bookmark/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(itemId)` | `POST /api/bookmarks/:itemId/toggle` | `{ isBookmarked: boolean }` |
| `getAll(page, limit)` | `GET /api/bookmarks?page=&limit=` | Bookmarks pagines |

### Comment Service (`services/comment/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getByItem(itemId, page, limit)` | `GET /api/comments/item/:itemId` | Commentaires pagines |
| `create(itemId, content)` | `POST /api/comments/item/:itemId` | Comment |
| `update(commentId, content)` | `PUT /api/comments/:commentId` | Comment |
| `delete(commentId)` | `DELETE /api/comments/:commentId` | void |

### Follow Service (`services/follow/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(userId)` | `POST /api/follow/:userId/toggle` | Toggle result |
| `getCounts(userId)` | `GET /api/follow/:userId/counts` | `{ followers, following }` |
| `getFollowers(userId, page, limit)` | `GET /api/follow/:userId/followers` | Users pagines |
| `getFollowing(userId, page, limit)` | `GET /api/follow/:userId/following` | Users pagines |
| `isFollowing(userId)` | `GET /api/follow/:userId/is-following` | `{ isFollowing: boolean }` |

### Source Service (`services/source/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getAll()` | `GET /api/sources` | `Source[]` |

### Subscription Service (`services/subscription/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(sourceId)` | `POST /api/subscriptions/:sourceId/toggle` | Toggle result |
| `getMine()` | `GET /api/subscriptions` | `Source[]` |
| `getCount(sourceId)` | `GET /api/subscriptions/:sourceId/count` | `{ count }` |

### Notification Service (`services/notification/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getAll(page, limit)` | `GET /api/notifications` | Notifications paginee |
| `getUnreadCount()` | `GET /api/notifications/unread-count` | `{ count }` |
| `markAsRead(id)` | `PATCH /api/notifications/:id/read` | void |
| `markAllAsRead()` | `PATCH /api/notifications/read-all` | void |
| `delete(id)` | `DELETE /api/notifications/:id` | void |

### User Service (`services/user/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getById(id)` | `GET /api/user/:id` | User |
| `getMe()` | `GET /api/user/me` | User |
| `updateMe(data)` | `PUT /api/user/me` | void |
| `update(id, data)` | `PUT /api/user/:id` | void |
| `delete(id)` | `DELETE /api/user/:id` | void |

### Trust Service (`services/trust/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `getScore(itemId)` | `GET /api/trust/:itemId` | `{ trustScore }` |
| `analyze(itemId)` | `POST /api/trust/:itemId/analyze` | `{ trustScore }` |

### Avatar Service (`services/avatar/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `upload(imageUri)` | `POST /api/user/me/avatar` (multipart) | `{ avatarUrl }` |

### Preferences Service (`services/preferences/`)

| Methode | Endpoint | Retour |
|---------|----------|--------|
| `get()` | `GET /api/preferences` | `UserPreferences` |
| `update(partial)` | `PUT /api/preferences` | `UserPreferences` |

---

## 7. State Management

### TanStack React Query (donnees serveur)

**Configuration par defaut :**
```typescript
{
  queries: {
    staleTime: 5 * 60 * 1000,    // 5 minutes
    retry: 2,
    refetchOnWindowFocus: false,  // specifique mobile
  },
  mutations: { retry: 0 }
}
```

**Query Keys Factory (`hooks/queryKeys.ts`) :**

```typescript
export const queryKeys = {
  feed:          { all: ["feed"], list: (page) => [...], item: (id) => [...] },
  search:        { articles: (q) => [...], users: (q) => [...] },
  comments:      { byItem: (id) => [...] },
  likes:         { item: (id) => [...] },
  bookmarks:     { all: [...] },
  follow:        { counts: (userId) => [...], isFollowing: (userId) => [...] },
  subscriptions: { mine: [...] },
  notifications: { all: [...], unreadCount: [...] },
  trust:         { item: (id) => [...] },
  sources:       { all: [...] },
  user:          { byId: (id) => [...] },
}
```

**Patterns d'utilisation :**

```typescript
// Lecture
const { data, isLoading } = useQuery({
  queryKey: queryKeys.feed.all,
  queryFn: async () => {
    const result = await feedService.getFeed()
    if (result.kind === "ok") return result.data
    throw new Error(result.message)
  },
})

// Mutation
const mutation = useMutation({
  mutationFn: (itemId) => likeService.toggle(itemId),
  onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.feed.all }),
})

// Scroll infini
const { data, fetchNextPage, hasNextPage } = useInfiniteQuery({
  queryKey: queryKeys.feed.all,
  queryFn: ({ pageParam = 1 }) => feedService.getFeed({ page: pageParam }),
  getNextPageParam: (lastPage) =>
    lastPage.page < lastPage.totalPages ? lastPage.page + 1 : undefined,
  initialPageParam: 1,
})
```

### Zustand (state client)

**Notification Store (`stores/notificationStore.ts`) :**
- State : `unreadCount`
- Action : `setUnreadCount(n)`
- Utilise pour le badge de l'onglet notifications

**Preferences Store (`stores/preferencesStore.ts`) :**
- State : preferences utilisateur + loading/error
- Actions : `fetchPreferences()`, `updatePreference(key, value)`
- Optimistic updates avec rollback serveur en cas d'erreur

---

## 8. Ecrans

### Feed (`(tabs)/index.tsx`)

- Message d'accueil personnalise ("Bonjour {name}")
- FlatList avec scroll infini (20 items/page)
- ArticleCard pour chaque item
- Actions : like, bookmark, navigation vers detail
- Pull-to-refresh
- Skeleton loading a l'initialisation
- Empty state si aucune source

### Recherche (`(tabs)/search.tsx`)

- Barre de recherche avec focus auto
- Onglets : "Articles" | "Utilisateurs"
- Debounce 300ms, minimum 2 caracteres
- Deux queries separees (conditional enable)
- Filtre l'utilisateur courant des resultats users
- Empty state avec texte d'aide

### Decouvrir (`(tabs)/discover.tsx`)

- Onglets : "Tendances" | "Recents" | "Sources"
- **Tendances** : articles tries par engagement
- **Recents** : articles tries par date (24h)
- **Sources** : toutes les sources avec bouton d'abonnement
- SourceCard avec compteur d'abonnes
- Pull-to-refresh sur les onglets articles

### Notifications (`(tabs)/notifications.tsx`)

- Badge compteur non lues dans l'onglet
- Bouton "Tout marquer comme lu" (conditionnel)
- NotificationItem avec actions : tap (marquer lu + naviguer), delete
- Pull-to-refresh
- Empty state

### Profil (`(tabs)/profile.tsx`)

- ProfileHeader : avatar, stats (articles/followers/following)
- Boutons edit profil & logout dans le header
- Onglets : "Articles" | "Bookmarks" | "Abonnements"
- Followers/following cliquables (navigation)
- Bouton settings

### Detail article (`article/[id].tsx`)

- Titre, auteur, source, TrustBadge
- Contenu (HTML strip) ou resume
- Barre d'actions : like, compteur commentaires, bookmark, partage
- Section commentaires avec ajout/suppression
- CommentInput sticky en bas
- KeyboardAvoidingView (iOS)

### Profil utilisateur (`user/[id].tsx`)

- Avatar + nom + bio
- Stats : followers, following (cliquables)
- Bouton follow/unfollow

### Followers/Following (`user/[id]/followers.tsx`, `following.tsx`)

- Liste d'utilisateurs paginee
- UserRow pour chaque utilisateur

### Parametres (`settings/`)

7 ecrans :
- **Index** : menu avec sections (Compte, Confidentialite, Notifications, Apparence, Securite, Donnees)
- **Account** : avatar modifiable (image picker), nom, email, bio, changement password
- **Privacy** : visibilite profil, affichage email/activite
- **Notifications** : toggles email/push/followers/comments/likes/articles
- **Appearance** : theme sombre/clair, taille texte
- **Security** : 2FA, alertes connexion
- **Data-Privacy** : gestion des donnees

---

## 9. Composants Reutilisables

| Composant | Props principales | Description |
|-----------|------------------|-------------|
| `ArticleCard` | article, onPress, onLike, onBookmark, isBookmarked | Carte article dans les listes |
| `SearchBar` | value, onChangeText, placeholder | Barre de recherche |
| `TabSelector` | tabs[], activeKey, onSelect | Selecteur d'onglets horizontal |
| `Avatar` | name, uri, size | Avatar avec fallback initiales |
| `NotificationItem` | notification, onPress, onDelete | Item de notification |
| `CommentItem` | comment, isOwn, onDelete | Commentaire avec suppression |
| `CommentInput` | onSubmit, isSubmitting | Input commentaire sticky |
| `TrustBadge` | score, size | Badge score de confiance (colore) |
| `SourceCard` | source, isSubscribed, onToggleSubscribe | Carte source avec abonnement |
| `ProfileHeader` | name, bio, avatar, stats, isOwnProfile, onEditProfile, onFollow | Header profil |
| `EmptyState` | icon, title, subtitle | Etat vide centre |
| `Toast` | message, visible, onDismiss | Notification toast (auto-dismiss 3s) |
| `Skeleton` | — | Placeholder loading avec shimmer |
| `Badge` | — | Badge generique |
| `IconButton` | — | Bouton icone |
| `SectionHeader` | — | Titre de section |
| `SettingsRow` | — | Ligne de parametres |
| `SettingsToggle` | — | Toggle dans les parametres |
| `UserRow` | — | Ligne utilisateur dans les listes |
| `AutoImage` | — | Image auto-dimensionnee |
| `ErrorBoundary` | catchErrors | Error boundary avec affichage erreur |

---

## 10. Design System

### Couleurs (`design/colors.ts`)

**Theme sombre (defaut) :**

| Token | Valeur | Usage |
|-------|--------|-------|
| `bg` | `#000000` | Fond principal |
| `surface` | `#1a1a1a` | Cartes, surfaces |
| `color` | `#f5f5f5` | Texte principal |
| `color2` | `#a8a8a8` | Texte secondaire |
| `color3` | `#737373` | Texte tertiaire |
| `border` | `#363636` | Bordures |
| `primary` | `#3a3a3a` | Couleur primaire |
| `danger` | `#ed4956` | Erreurs, suppressions |
| `success` | `#22C55E` | Succes, confirmations |

**Theme clair :**

| Token | Valeur |
|-------|--------|
| `bg` | `#ffffff` |
| `surface` | `#f8f8f8` |
| `color` | `#262626` |
| `border` | `#dbdbdb` |
| `primary` | `#e8e8e8` |

### Typographie (`design/typography.ts`)

**Police** : SpaceGrotesk

| Poids | Nom |
|-------|-----|
| 300 | Light |
| 400 | Regular |
| 500 | Medium |
| 600 | SemiBold |
| 700 | Bold |

### Tamagui (`tamagui.config.ts`)

- Tokens de design (spacing, sizes, colors)
- Themes light & dark
- Shorthands pour les props de style
- Mapping fonts (body, heading)
- Theme par defaut : dark

---

## 11. Internationalisation

### Langues supportees

| Code | Langue |
|------|--------|
| `fr` | Francais (defaut) |
| `en` | Anglais |
| `es` | Espagnol |
| `ar` | Arabe |
| `hi` | Hindi |
| `ja` | Japonais |
| `ko` | Coreen |

### Setup (`i18n/index.ts`)

- **Libraries** : i18next + react-i18next + expo-localization
- Initialisation async avant le rendu de l'app
- Detection automatique de la langue du device
- Support date-fns avec locale correspondante

### Utilisation

Chaque langue a son fichier de traduction (`i18n/fr.ts`, `i18n/en.ts`, etc.) contenant toutes les chaines de l'interface.

---

## 12. Configuration & Environnement

### Variables d'environnement (`.env.example`)

```
EXPO_PUBLIC_API_URL=https://api.syntheza.ovh/
EXPO_PUBLIC_GOOGLE_WEB_CLIENT_ID=...
EXPO_PUBLIC_GOOGLE_IOS_CLIENT_ID=...
EXPO_PUBLIC_GOOGLE_ANDROID_CLIENT_ID=...
EXPO_PUBLIC_GOOGLE_IOS_URL_SCHEME=com.googleusercontent.apps.{ID}
```

### Config multi-environnement (`config/`)

- `config.base.ts` — Configuration commune
- `config.dev.ts` — Surcharges dev (API locale)
- `config.prod.ts` — Surcharges prod (API syntheza.ovh)
- `index.ts` — Loader automatique selon `NODE_ENV`
- `google.ts` — IDs clients Google OAuth

### App Config (`app.json`)

| Champ | Valeur |
|-------|--------|
| App Name | Syntheza |
| Slug | syntheza |
| Package | com.frontendmobile |
| Icons | app-icon-all.png |
| Splash | logo.png |
| Theme | automatic (suit le systeme) |
| JS Engine | Hermes |
| New Architecture | active |
| EAS Project ID | d4685ecf-f1d8-443b-b7d8-e9cce0f8ed08 |

### Plugins Expo

- expo-localization
- expo-font
- expo-splash-screen
- react-native-edge-to-edge
- @react-native-google-signin/google-signin
- expo-image-picker

---

## 13. Testing

### Configuration

- **Framework** : Jest (jest-expo preset)
- **Setup** : `test/setup.ts`
- **Mocks** : MMKV, i18n
- **TypeScript** : ts-jest

### Tests existants

**Tests services** (`src/tests/services/`) :
- avatarService, bookmarkService, commentService, feedService
- followService, likeService, notificationService, searchService
- sourceService, subscriptionService, trustService

**Tests i18n** (`test/i18n.test.ts`)

**Tests stockage** (`src/utils/storage/storage.test.ts`)

### Scripts

```bash
npm test              # Run tests
npm test:watch        # Mode watch
npm test:maestro      # Tests UI Maestro
```

### Maestro (E2E)

Flows de test UI dans `.maestro/` pour les parcours utilisateur principaux.

---

## 14. Build & Deploiement

### Scripts de developpement

```bash
npm start             # Demarrer le serveur dev Expo
npm run android       # Build et lancer sur Android
npm run ios           # Build et lancer sur iOS
npm run web           # Demarrer en mode web
```

### Builds EAS (`eas.json`)

| Profil | Usage |
|--------|-------|
| `development` | Simulateur (dev client) |
| `development:device` | Device physique |
| `preview` | Build de preview (TestFlight/APK) |
| `production` | Build de production (App Store/Play Store) |

### Scripts de build

```bash
# iOS
npm run build:ios:sim          # Simulateur iOS
npm run build:ios:device       # Device iOS
npm run build:ios:preview      # Preview iOS
npm run build:ios:prod         # Production iOS

# Android
npm run build:android:sim      # Simulateur Android
npm run build:android:device   # Device Android
npm run build:android:preview  # Preview Android
npm run build:android:prod     # Production Android

# Web
npm run bundle:web             # Export bundle web
npm run serve:web              # Servir localement
```

### Qualite

```bash
npm run compile       # TypeScript check (no emit)
npm run lint          # ESLint fix
npm run lint:check    # ESLint check only
```

### Debugging

```bash
# Android : port forwarding pour adb
npm run adb           # Forward ports 9090, 3000, 9001, 8081
```

- **Reactotron** : configure en dev, avec plugin MMKV
- **React DevTools** : disponible en mode web
- **Expo Dev Tools** : integre au serveur dev

---

## 15. Performance

### Optimisations reseau

- **Stale time** : 5 minutes (reduit les refetches inutiles)
- **Retry** : 2 pour les queries, 0 pour les mutations
- **Pas de refetch au focus** (specifique mobile)
- **Debounce recherche** : 300ms + minimum 2 caracteres

### Optimisations rendu

- **Scroll infini** : pagination 20 items, `getNextPageParam`
- **Deduplication** : Set<number> cote client pour eviter les doublons
- **useMemo** : deduplication des articles
- **useCallback** : toggle bookmark
- **Lazy loading** : image picker charge uniquement dans les settings compte

### Stockage

- **MMKV** : stockage local ultra-rapide (remplacement AsyncStorage)
- **SecureStore** : tokens chiffres par le systeme
- **QueryClient** : cache en memoire des donnees API

---

## 16. Securite

### Stockage des tokens

| Donnee | Methode | Securite |
|--------|---------|----------|
| JWT Token | `expo-secure-store` | Chiffre par le Keychain iOS / Keystore Android |
| User JSON | MMKV | Chiffre au repos par l'OS |
| Donnees sensibles | Jamais loguees/exposees | — |

### Communication

- HTTPS uniquement en production (`https://api.syntheza.ovh/`)
- Headers securises : `Content-Type`, `Accept`, `Authorization: Bearer`

### Validation input

- Regex email cote client
- Password >= 8 caracteres
- Trim des textes avant soumission

### Error Boundary

- Enveloppe l'app entiere dans `(auth)` et `(app)` layouts
- Affiche `ErrorDetails` en cas de crash
- Mode `catchErrors="always"`

### Modules natifs requis

Certaines fonctionnalites necessitent un dev client (pas Expo Go) :
- `expo-image-picker` (upload avatar)
- `@react-native-google-signin/google-signin` (OAuth Google)

Apres installation de modules natifs :
```bash
npx expo run:ios      # Rebuild iOS
npx expo run:android  # Rebuild Android
```
