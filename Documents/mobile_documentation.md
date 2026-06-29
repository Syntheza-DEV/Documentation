# Syntheza Mobile — Documentation Technique

**Version** : Charte ISO Mobile (juin 2026)
**Stack** : Expo SDK 54 / React Native 0.81.5 / React 19.1.0 / TypeScript / Tamagui
**Date** : Juin 2026

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Architecture & Structure](#2-architecture--structure)
3. [Navigation & Routing](#3-navigation--routing)
4. [Authentification](#4-authentification)
5. [Client API](#5-client-api)
6. [Services (Couche Données)](#6-services-couche-données)
7. [State Management](#7-state-management)
8. [Écrans](#8-écrans)
9. [Composants Réutilisables](#9-composants-réutilisables)
10. [Design System](#10-design-system)
11. [Internationalisation](#11-internationalisation)
12. [Configuration & Environnement](#12-configuration--environnement)
13. [Testing](#13-testing)
14. [Build & Déploiement](#14-build--déploiement)
15. [Performance](#15-performance)
16. [Sécurité](#16-sécurité)

---

## 1. Vue d'ensemble

Syntheza Mobile est une application de veille informationnelle sociale permettant aux utilisateurs de :

- S'authentifier (email/password + Google OAuth)
- Parcourir un feed d'articles personnalisé avec scroll infini
- Rechercher des articles et des utilisateurs
- Découvrir des articles tendance/récents et des éditeurs (Publishers)
- Recevoir des notifications avec compteur de non lues
- Gérer son profil (avatar, bio, articles, bookmarks, abonnements à des éditeurs)
- Interagir : likes, commentaires, bookmarks, follow
- S'abonner à des éditeurs (Publisher Subscriptions)
- Consulter les scores de confiance (Trust Factor) des articles
- Personnaliser l'apparence (thème sombre/clair, langue)
- Gérer ses paramètres (compte, confidentialité, notifications, sécurité)

### Stack technique

| Couche | Technologie |
|--------|------------|
| Framework | Expo SDK 54, React Native 0.81.5 |
| Langage | TypeScript (strict) |
| Navigation | Expo Router 6 (file-based routing) |
| UI | Tamagui v2.0.0-rc.17 + React Native natif |
| State (serveur) | TanStack React Query v5 |
| State (client) | Zustand v5 |
| HTTP | Apisauce (wrapper axios) |
| Stockage sécurisé | expo-secure-store |
| Stockage local | react-native-mmkv 3.3.3 |
| Fonts | SpaceGrotesk |
| i18n | i18next v23 + react-i18next v15 |
| Auth Google | @react-native-google-signin/google-signin |
| Animations | react-native-reanimated v4 |
| Gestures | react-native-gesture-handler |
| SVG / Gradient texte | react-native-svg |
| JS Engine | Hermes |
| Architecture | React Native New Architecture (Fabric + TurboModules) |
| Package manager | pnpm 10.33.0 |

---

## 2. Architecture & Structure

```
frontend-mobile/
├── app.config.ts                         # Config Expo (TypeScript)
├── app.json                              # Config Expo (JSON)
├── package.json                          # Dependencies (pnpm 10.33.0, node >= 20)
├── tamagui.config.ts                     # Config thème Tamagui
├── babel.config.js                       # Babel
├── metro.config.js                       # Metro bundler
├── eas.json                              # EAS Build profiles
├── src/
│   ├── app/                              # Expo Router (file-based routes)
│   │   ├── _layout.tsx                   # Root layout (providers)
│   │   ├── index.tsx                     # Splash/redirect auth
│   │   ├── (auth)/                       # Groupe auth (non protégé)
│   │   │   ├── _layout.tsx
│   │   │   ├── login.tsx
│   │   │   ├── register.tsx
│   │   │   └── forgot-password.tsx
│   │   └── (app)/                        # Groupe app (protégé)
│   │       ├── _layout.tsx
│   │       ├── (tabs)/                   # Navigation par onglets
│   │       │   ├── _layout.tsx
│   │       │   ├── index.tsx             # Feed
│   │       │   ├── search.tsx            # Recherche
│   │       │   ├── discover.tsx          # Découvrir
│   │       │   ├── notifications.tsx     # Notifications
│   │       │   └── profile.tsx           # Profil
│   │       ├── article/[id].tsx          # Détail article
│   │       ├── user/[id].tsx             # Profil utilisateur
│   │       ├── user/[id]/followers.tsx
│   │       ├── user/[id]/following.tsx
│   │       └── settings/                 # Paramètres
│   │           ├── _layout.tsx
│   │           ├── index.tsx
│   │           ├── account.tsx
│   │           ├── privacy.tsx
│   │           ├── notifications.tsx
│   │           ├── appearance.tsx
│   │           ├── security.tsx
│   │           └── data-privacy.tsx
│   ├── components/                       # Composants UI réutilisables (24 fichiers .tsx)
│   ├── contexts/                         # React Context (Auth)
│   ├── services/                         # Couche API (15 dossiers de services)
│   ├── stores/                           # Zustand stores
│   ├── hooks/                            # Custom hooks (queryKeys)
│   ├── design/                           # Design tokens & thème
│   ├── config/                           # Configuration app
│   ├── i18n/                             # Traductions (8 langues)
│   ├── utils/                            # Utilitaires (storage, etc.)
│   ├── mocks/                            # Données mock
│   ├── devtools/                         # Reactotron (dev only)
│   └── tests/                            # Tests services (11 fichiers)
├── test/                                 # Setup Jest + tests i18n
└── .maestro/                             # Tests UI Maestro
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

#### Point d'entrée (`index.tsx`)

- Utilisateur authentifié → redirect `/(app)`
- Utilisateur non authentifié → redirect `/(auth)/login`

#### Groupe Auth (`(auth)/`)

Stack navigator avec animation `slide_from_right` :
- `login` — Connexion email/password + Google OAuth
- `register` — Inscription avec validation
- `forgot-password` — Reset password par email

#### Groupe App (`(app)/`)

Protégé par `AuthProvider` (redirect vers login si non authentifié).

**Tab Navigation (`(tabs)/`)** — 5 onglets :

| Onglet | Écran | Icône | Description |
|--------|-------|-------|-------------|
| Accueil | `index.tsx` | home | Feed avec scroll infini |
| Recherche | `search.tsx` | search | Articles & utilisateurs |
| Découvrir | `discover.tsx` | compass | Tendances, récents, éditeurs |
| Notifications | `notifications.tsx` | bell + badge | Liste des notifications |
| Profil | `profile.tsx` | user | Mon profil |

**Écrans supplémentaires :**
- `article/[id]` — Détail article avec commentaires
- `user/[id]` — Profil d'un autre utilisateur
- `user/[id]/followers` — Liste des followers
- `user/[id]/following` — Liste des suivis
- `settings/*` — 7 écrans de paramètres

**Total : 20 écrans (hors layouts)**

---

## 4. Authentification

### AuthContext (`contexts/AuthContext.tsx`)

**State :**
- `user` : objet utilisateur courant
- `isAuthenticated` : boolean
- `isLoading` : true pendant la restauration du token

**Méthodes :**
- `login(email, password)` → LoginResult
- `register(name, email, password)` → RegisterResult
- `forgotPassword(email)` → ForgotPasswordResult
- `loginWithGoogle(idToken)` → LoginResult
- `logout()` → Clear token + user, redirect login

### Gestion des tokens (`services/auth/tokenService.ts`)

| Donnée | Stockage | Clé |
|--------|----------|-----|
| Token JWT (access) | `expo-secure-store` (chiffré) | `auth.token` |
| Refresh token | `expo-secure-store` (chiffré) | `auth.refreshToken` |
| User | MMKV (localStorage) | `auth.user` |

Au démarrage de l'app : restauration automatique du token + refresh token + user depuis le stockage.

### Intercepteur de token

L'API client intercepte les réponses 401 :
1. Tente un refresh via `POST /api/user/refresh`
2. Si succès → met à jour le token, rejoue la requête
3. Si échec → logout + redirect login
4. File d'attente pour éviter les refreshs multiples simultanément

### Google OAuth (`hooks/useGoogleAuth.ts`)

- Utilise `@react-native-google-signin/google-signin`
- Gère gracieusement l'absence du module natif (fallback)
- Retourne : `isAvailable`, `isLoading`, `signIn()`

### Écrans d'authentification

**Login :**
- Email + password + validation
- Bouton Google OAuth (si disponible)
- Lien "Mot de passe oublié ?"
- ActivityIndicator pendant le chargement

**Register :**
- Name, email, password, confirm password
- Validation : name requis, format email, password >= 8 chars, confirmation identique
- Feedback visuel (bordure rouge) sur mismatch password

**Forgot Password :**
- Input email + bouton envoi
- État de succès avec checkmark
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

### Format de réponse

```typescript
interface ApiResponse<T> {
  success: boolean
  data: T
}
```

### Gestion d'erreurs (`services/api/apiProblem.ts`)

Catégorisation des erreurs API :
- Erreur réseau (pas de connexion)
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

## 6. Services (Couche Données)

15 dossiers de services. Les anciens services `source` et `subscription` ont été supprimés et remplacés par `publisher` et `publisherSubscription`.

### Feed Service (`services/feed/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getFeed(params)` | `GET /api/feed?page=&limit=20&sortBy=` | `FeedResponse` (paginé) |
| `getItem(id)` | `GET /api/feed/item/:id` | `FeedItem` |
| `getDigest()` | `GET /api/feed/digest` | `DigestResponse \| null` |
| `getOverview()` | `GET /api/feed/overview` | `OverviewResponse` |

```typescript
interface FeedPublisher {
  id: number
  name: string
  slug?: string
}

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
  publisher: FeedPublisher       // remplace l'ancien champ "source"
  summary: string | null
  likesCount: number
  commentsCount: number
  isLiked: boolean
  createdAt: string
}
```

### Search Service (`services/search/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `searchArticles(q, page, limit)` | `GET /api/search?q=&page=&limit=` | Articles paginés |
| `searchUsers(q, page, limit)` | `GET /api/user/search?q=&page=&limit=` | Users paginés |

### Like Service (`services/like/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(itemId)` | `POST /api/likes/:itemId/toggle` | `{ isLiked: boolean }` |
| `getCount(itemId)` | `GET /api/likes/:itemId/count` | `{ count: number }` |

### Bookmark Service (`services/bookmark/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(itemId)` | `POST /api/bookmarks/:itemId/toggle` | `{ isBookmarked: boolean }` |
| `getAll(page, limit)` | `GET /api/bookmarks?page=&limit=` | Bookmarks paginés |

### Comment Service (`services/comment/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getByItem(itemId, page, limit)` | `GET /api/comments/item/:itemId` | Commentaires paginés |
| `create(itemId, content)` | `POST /api/comments/item/:itemId` | Comment |
| `update(commentId, content)` | `PUT /api/comments/:commentId` | Comment |
| `delete(commentId)` | `DELETE /api/comments/:commentId` | void |

### Follow Service (`services/follow/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(userId)` | `POST /api/follow/:userId/toggle` | Toggle result |
| `getCounts(userId)` | `GET /api/follow/:userId/counts` | `{ followers, following }` |
| `getFollowers(userId, page, limit)` | `GET /api/follow/:userId/followers` | Users paginés |
| `getFollowing(userId, page, limit)` | `GET /api/follow/:userId/following` | Users paginés |
| `isFollowing(userId)` | `GET /api/follow/:userId/is-following` | `{ isFollowing: boolean }` |

### Publisher Service (`services/publisher/`) ← remplace l'ancien Source Service

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getAll()` | `GET /api/publishers` | `Publisher[]` |
| `getById(id)` | `GET /api/publishers/:id` | `Publisher` |

### Publisher Subscription Service (`services/publisherSubscription/`) ← remplace l'ancien Subscription Service

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `toggle(publisherId)` | `POST /api/publisher-subscriptions/:publisherId/toggle` | Toggle result |
| `getMine()` | `GET /api/publisher-subscriptions` | `PublisherSubscription[]` |

> Note : la route `GET /api/publisher-subscriptions/:publisherId/count` n'est pas implémentée côté backend (404 confirmé en prod). La méthode client correspondante a été supprimée.

### Notification Service (`services/notification/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getAll(page, limit)` | `GET /api/notifications` | Notifications paginées |
| `getUnreadCount()` | `GET /api/notifications/unread-count` | `{ count }` |
| `markAsRead(id)` | `PATCH /api/notifications/:id/read` | void |
| `markAllAsRead()` | `PATCH /api/notifications/read-all` | void |
| `delete(id)` | `DELETE /api/notifications/:id` | void |

### User Service (`services/user/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getById(id)` | `GET /api/user/:id` | User |
| `getMe()` | `GET /api/user/me` | User |
| `updateMe(data)` | `PUT /api/user/me` | void |
| `update(id, data)` | `PUT /api/user/:id` | void |
| `delete(id)` | `DELETE /api/user/:id` | void |

### Trust Service (`services/trust/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `getScore(itemId)` | `GET /api/trust/:itemId` | `{ trustScore }` |
| `analyze(itemId)` | `POST /api/trust/:itemId/analyze` | `{ trustScore }` |

### Avatar Service (`services/avatar/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `upload(imageUri)` | `POST /api/user/me/avatar` (multipart) | `{ avatarUrl }` |

### Preferences Service (`services/preferences/`)

| Méthode | Endpoint | Retour |
|---------|----------|--------|
| `get()` | `GET /api/preferences` | `UserPreferences` |
| `update(partial)` | `PATCH /api/preferences` | `UserPreferences` |

---

## 7. State Management

### TanStack React Query (données serveur)

**Configuration par défaut :**
```typescript
{
  queries: {
    staleTime: 5 * 60 * 1000,    // 5 minutes
    retry: 2,
    refetchOnWindowFocus: false,  // spécifique mobile
  },
  mutations: { retry: 0 }
}
```

**Query Keys Factory (`hooks/queryKeys.ts`) :**

```typescript
export const queryKeys = {
  feed:                    { all, list, item, digest, overview },
  search:                  { articles, users },
  likes:                   { item },
  comments:                { byItem },
  bookmarks:               { all },
  follow:                  { counts, followers, following, isFollowing },
  publisherSubscriptions:  { mine, count },   // remplace "subscriptions"
  notifications:           { all, unreadCount },
  trust:                   { item },
  publishers:              { all },            // remplace "sources"
  user:                    { me, byId },
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
- Utilisé pour le badge de l'onglet notifications

**Preferences Store (`stores/preferencesStore.ts`) :**
- State : preferences utilisateur + loading/error
- Actions : `fetchPreferences()`, `updatePreference(key, value)`
- Optimistic updates avec rollback serveur en cas d'erreur

---

## 8. Écrans

20 écrans au total (hors `_layout.tsx`).

### Feed (`(tabs)/index.tsx`)

- Message d'accueil personnalisé avec prénom en dégradé (`GradientText`)
- FlatList avec scroll infini (20 items/page)
- ArticleCard pour chaque item
- Actions : like, bookmark, navigation vers détail
- Pull-to-refresh
- Skeleton loading à l'initialisation
- Empty state si aucun abonnement éditeur

### Recherche (`(tabs)/search.tsx`)

- Barre de recherche avec focus auto
- Onglets : "Articles" | "Utilisateurs"
- Debounce 300ms, minimum 2 caractères
- Deux queries séparées (conditional enable)
- Filtre l'utilisateur courant des résultats users
- Empty state avec texte d'aide

### Découvrir (`(tabs)/discover.tsx`)

- Onglets : "Tendances" | "Récents" | "Éditeurs"
- **Tendances** : articles triés par engagement
- **Récents** : articles triés par date
- **Éditeurs** : tous les publishers avec bouton d'abonnement
- `PublisherCard` avec compteur d'abonnés (remplace l'ancienne `SourceCard`)
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
- Onglet Abonnements : liste des publishers souscrits via `PublisherCard`
- Followers/following cliquables (navigation)
- Bouton settings

### Détail article (`article/[id].tsx`)

- `ScreenHeader` (flèche retour + titre wordmark)
- Titre, auteur, éditeur (publisher), `TrustBadge`
- Contenu (HTML strip) ou résumé
- Barre d'actions : like, compteur commentaires, bookmark, partage
- `TrustDetailSheet` modal pour le détail du score de confiance
- Section commentaires avec ajout/suppression
- CommentInput sticky en bas
- KeyboardAvoidingView (iOS)

### Profil utilisateur (`user/[id].tsx`)

- `ScreenHeader` (flèche retour)
- Avatar + nom + bio
- Stats : followers, following (cliquables)
- Bouton follow/unfollow

### Followers/Following (`user/[id]/followers.tsx`, `following.tsx`)

- `ScreenHeader`
- Liste d'utilisateurs paginée
- UserRow pour chaque utilisateur

### Paramètres (`settings/`)

7 écrans, tous avec `ScreenHeader` :
- **Index** : menu avec sections (Compte, Confidentialité, Notifications, Apparence, Sécurité, Données)
- **Account** : avatar modifiable (image picker), nom, email, bio, changement password
- **Privacy** : visibilité profil, affichage email/activité
- **Notifications** : toggles email/push/followers/comments/likes/articles
- **Appearance** : thème sombre/clair, taille texte
- **Security** : 2FA, alertes connexion
- **Data-Privacy** : gestion des données

---

## 9. Composants Réutilisables

24 fichiers `.tsx` de composants (dont 2 dans `ErrorBoundary/`).

| Composant | Description |
|-----------|-------------|
| `ArticleCard` | Carte article dans les listes (like, bookmark, navigation) |
| `AutoImage` | Image auto-dimensionnée |
| `Avatar` | Avatar avec fallback initiales |
| `Badge` | Badge générique |
| `CommentInput` | Input commentaire sticky |
| `CommentItem` | Commentaire avec suppression |
| `EmptyState` | État vide centré |
| `ErrorBoundary` | Error boundary avec affichage erreur |
| `ErrorDetails` | Détail d'erreur (affiché par ErrorBoundary) |
| `GradientText` | Texte avec dégradé horizontal via SVG (équivalent RN de `bg-clip-text`) |
| `IconButton` | Bouton icône |
| `NotificationItem` | Item de notification |
| `ProfileHeader` | Header profil (avatar, stats, actions) |
| `PublisherCard` | Carte éditeur avec bouton d'abonnement (remplace SourceCard) |
| `ScreenHeader` | En-tête écrans détail/réglages : flèche retour + titre wordmark optionnel |
| `SearchBar` | Barre de recherche |
| `SectionHeader` | Titre de section |
| `SettingsRow` | Ligne de paramètres |
| `SettingsToggle` | Toggle dans les paramètres |
| `Skeleton` | Placeholder loading avec shimmer (+ `ArticleCardSkeleton`) |
| `TabSelector` | Sélecteur d'onglets horizontal |
| `ThemeCard` | Carte de thème (non utilisée dans l'app, dead code) |
| `Toast` | Notification toast (auto-dismiss 3s) |
| `TrustBadge` | Badge score de confiance (coloré) |
| `TrustDetailSheet` | Modal détail du score de confiance (analyse IA) |
| `UserRow` | Ligne utilisateur dans les listes |

---

## 10. Design System

### Charte iso web (juin 2026)

La charte mobile est alignée sur le design web : mêmes tokens, mêmes conventions visuelles (primaryText inversé, radius, wordmark SpaceGrotesk).

### Couleurs (`design/colors.ts`)

**Thème sombre (défaut) :**

| Token | Valeur | Usage |
|-------|--------|-------|
| `bg` | `#000000` | Fond principal |
| `bg2` | `#121212` | Fond secondaire |
| `surface` | `#1a1a1a` | Cartes, surfaces |
| `surface2` | `#262626` | Surfaces secondaires |
| `color` | `#f5f5f5` | Texte principal |
| `color2` | `#a8a8a8` | Texte secondaire |
| `color3` | `#737373` | Texte tertiaire |
| `border` | `#363636` | Bordures |
| `primary` | `#f5f5f5` | Couleur primaire (texte inversé vs web) |
| `primaryText` | `#000000` | Texte sur fond primaire |
| `danger` | `#ed4956` | Erreurs, suppressions |
| `success` | `#22C55E` | Succès, confirmations |
| `gradientStart` | `#667eea` | Début dégradé (GradientText, home) |
| `gradientEnd` | `#764ba2` | Fin dégradé |

**Thème clair :**

| Token | Valeur |
|-------|--------|
| `bg` | `#ffffff` |
| `surface` | `#f8f8f8` |
| `color` | `#262626` |
| `border` | `#dbdbdb` |
| `primary` | `#262626` |
| `primaryText` | `#ffffff` |

### Typographie (`design/typography.ts`)

**Police** : SpaceGrotesk (5 graisses : Light 300, Regular 400, Medium 500, SemiBold 600, Bold 700)

La fonction `wordmarkTitle(fontSize)` retourne le style du titre wordmark (SpaceGrotesk Bold, utilisé dans `ScreenHeader` et feed).

### Composants structurants de charte

**`ScreenHeader`** : en-tête unifié pour tous les écrans de détail et réglages. Porte son propre `paddingHorizontal: 16` et `paddingTop: 20`. Props : `title?` (wordmark optionnel), `onBack?` (flèche retour, défaut : `router.back()`).

**`GradientText`** : texte avec dégradé horizontal via `react-native-svg` (LinearGradient masqué sur un SVG). Utilisé pour le prénom dans l'écran d'accueil. Props : `text`, `fontSize`, `fontFamily`, `letterSpacing`, `colors` (tuple `[start, end]`).

### Tamagui (`tamagui.config.ts`)

- Tokens de design (spacing, sizes, colors)
- Thèmes light & dark
- Shorthands pour les props de style
- Mapping fonts (body, heading) → SpaceGrotesk
- Thème par défaut : dark

---

## 11. Internationalisation

### Langues supportées (8)

| Code | Langue |
|------|--------|
| `fr` | Français (défaut) |
| `en` | Anglais |
| `es` | Espagnol |
| `ar` | Arabe |
| `hi` | Hindi |
| `ja` | Japonais |
| `ko` | Coréen |
| _(8e)_ | _(vérifier `i18n/index.ts` si une langue est ajoutée)_ |

> La doc précédente mentionnait 7 langues. Le répertoire `i18n/` contient 7 fichiers de traduction (`ar`, `en`, `es`, `fr`, `hi`, `ja`, `ko`) plus `index.ts` et `translate.ts`, soit **7 langues effectives**.

### Setup (`i18n/index.ts`)

- **Libraries** : i18next + react-i18next + expo-localization
- Initialisation async avant le rendu de l'app
- Détection automatique de la langue du device
- Support date-fns avec locale correspondante

### Utilisation

Chaque langue a son fichier de traduction (`i18n/fr.ts`, `i18n/en.ts`, etc.) contenant toutes les chaînes de l'interface.

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

### App Config (`app.config.ts` / `app.json`)

| Champ | Valeur |
|-------|--------|
| App Name | Syntheza |
| Slug | syntheza |
| Package | com.frontendmobile |
| Icons | app-icon-all.png |
| Splash | logo.png |
| Thème | automatic (suit le système) |
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
- expo-build-properties

---

## 13. Testing

### Configuration

- **Framework** : Jest 29 (jest-expo preset)
- **Setup** : `test/setup.ts`
- **Mocks** : MMKV, i18n
- **TypeScript** : ts-jest

### Résultat actuel : 82 tests, 0 échec

### Tests existants

**Tests services** (`src/tests/services/`) — 11 fichiers :
- avatarService, bookmarkService, commentService, feedService
- followService, likeService, notificationService, searchService
- **publisherService** (remplace sourceService)
- **publisherSubscriptionService** (remplace subscriptionService)
- trustService

**Tests i18n** (`test/i18n.test.ts`)

**Tests stockage** (`src/utils/storage/storage.test.ts`)

**Tests API problem** (`src/services/api/apiProblem.test.ts`)

### Scripts

```bash
pnpm test              # Run tests
pnpm test:watch        # Mode watch
pnpm test:maestro      # Tests UI Maestro
```

### Maestro (E2E)

Flows de test UI dans `.maestro/` pour les parcours utilisateur principaux.

---

## 14. Build & Déploiement

### Scripts de développement

```bash
pnpm start             # Démarrer le serveur dev Expo (dev-client)
pnpm android           # Build et lancer sur Android
pnpm ios               # Build et lancer sur iOS
pnpm web               # Démarrer en mode web
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
pnpm build:ios:sim          # Simulateur iOS
pnpm build:ios:device       # Device iOS
pnpm build:ios:preview      # Preview iOS
pnpm build:ios:prod         # Production iOS

# Android
pnpm build:android:sim      # Simulateur Android
pnpm build:android:device   # Device Android
pnpm build:android:preview  # Preview Android
pnpm build:android:prod     # Production Android

# Web
pnpm bundle:web             # Export bundle web
pnpm serve:web              # Servir localement
```

> Il n'y a pas de CI/CD mobile automatique. Le déploiement device se fait via `eas update` manuellement.

### Qualité

```bash
pnpm compile       # TypeScript check (no emit)
pnpm lint          # ESLint fix
pnpm lint:check    # ESLint check only
```

### Debugging

```bash
# Android : port forwarding pour adb
pnpm adb           # Forward ports 9090, 3000, 9001, 8081
```

- **Reactotron** : configuré en dev, avec plugin MMKV
- **React DevTools** : disponible en mode web
- **Expo Dev Tools** : intégré au serveur dev

---

## 15. Performance

### Optimisations réseau

- **Stale time** : 5 minutes (réduit les refetches inutiles)
- **Retry** : 2 pour les queries, 0 pour les mutations
- **Pas de refetch au focus** (spécifique mobile)
- **Debounce recherche** : 300ms + minimum 2 caractères

### Optimisations rendu

- **Scroll infini** : pagination 20 items, `getNextPageParam`
- **Déduplication** : Set<number> côté client pour éviter les doublons
- **useMemo** : déduplication des articles
- **useCallback** : toggle bookmark
- **Lazy loading** : image picker chargé uniquement dans les settings compte

### Stockage

- **MMKV** : stockage local ultra-rapide (remplacement AsyncStorage)
- **SecureStore** : tokens chiffrés par le système (access token + refresh token)
- **QueryClient** : cache en mémoire des données API

---

## 16. Sécurité

### Stockage des tokens

| Donnée | Méthode | Sécurité |
|--------|---------|----------|
| JWT Access Token | `expo-secure-store` | Chiffré par le Keychain iOS / Keystore Android |
| Refresh Token | `expo-secure-store` | Chiffré par le Keychain iOS / Keystore Android |
| User JSON | MMKV | Chiffré au repos par l'OS |
| Données sensibles | Jamais loguées/exposées | — |

### Communication

- HTTPS uniquement en production (`https://api.syntheza.ovh/`)
- Headers sécurisés : `Content-Type`, `Accept`, `Authorization: Bearer`

### Validation input

- Regex email côté client
- Password >= 8 caractères
- Trim des textes avant soumission

### Error Boundary

- Enveloppe l'app entière dans `(auth)` et `(app)` layouts
- Affiche `ErrorDetails` en cas de crash
- Mode `catchErrors="always"`

### Modules natifs requis

Certaines fonctionnalités nécessitent un dev client (pas Expo Go) :
- `expo-image-picker` (upload avatar)
- `@react-native-google-signin/google-signin` (OAuth Google)

Après installation de modules natifs :
```bash
npx expo run:ios      # Rebuild iOS
npx expo run:android  # Rebuild Android
```
