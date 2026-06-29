# Adaptation mobile au refacto Publisher/Channel/Item — Plan d'implémentation

> **Pour agents :** SOUS-COMPÉTENCE REQUISE : utiliser `superpowers:subagent-driven-development` (recommandé) ou `superpowers:executing-plans` pour implémenter ce plan tâche par tâche. Les étapes utilisent la syntaxe checkbox (`- [ ]`).

**Objectif :** Adapter le frontend mobile (Expo SDK 54 / RN 0.81) au refacto backend Publisher/Channel/Item (mergé en prod le 17 mai 2026) — mapping 1-1 strict des endpoints, switch complet du shape `FeedItem` vers `publisher`, aucune préservation de l'ancien shape `source`.

**Architecture :** Branche unique `feat/publisher-channel-mobile-adapt` dans `frontend-mobile/`, 6 commits atomiques. Chaque commit laisse le repo compilable (`tsc --noEmit` zéro erreur) et testable (`jest` vert). Pas de feature flag, pas de fallback legacy, pas de migration runtime — coupe nette alignée sur le backend.

**Tech Stack :** TypeScript 5.x, Jest 29, Apisauce (wrapper axios), React Query 5, Expo Router 6, Tamagui, Zustand 5, MMKV, pnpm.

**Working directory :** `/Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile`

---

## Cartographie complète

**Services à supprimer :**
- `src/services/source/sourceService.ts` + `sourceTypes.ts`
- `src/services/subscription/subscriptionService.ts` + `subscriptionTypes.ts`

**Services à créer :**
- `src/services/publisher/publisherService.ts` + `publisherTypes.ts`
- `src/services/publisherSubscription/publisherSubscriptionService.ts` + `publisherSubscriptionTypes.ts`

**Tests à supprimer :**
- `src/tests/services/sourceService.test.ts`
- `src/tests/services/subscriptionService.test.ts`

**Tests à créer :**
- `src/tests/services/publisherService.test.ts`
- `src/tests/services/publisherSubscriptionService.test.ts`

**Types à modifier :**
- `src/services/feed/feedTypes.ts` : `FeedItem.source` → `publisher`
- `src/services/bookmark/bookmarkTypes.ts` : `BookmarkItem.item.source` → `publisher`

**Composants à modifier :**
- `src/components/ArticleCard.tsx` : `article.source` → `article.publisher`
- `src/components/SourceCard.tsx` → renommer en `PublisherCard.tsx`

**Écrans à modifier :**
- `src/app/(app)/(tabs)/discover.tsx` : services + composant + label "Sources" → "Éditeurs"
- `src/app/(app)/(tabs)/profile.tsx` : services + composant + mapping bookmark `source` → `publisher`
- `src/app/(app)/article/[id].tsx` : `article.source?.name` → `article.publisher?.name`

**Hooks :**
- `src/hooks/queryKeys.ts` : `sources` → `publishers`, `subscriptions` → `publisherSubscriptions`

**NON impactés (audit confirmé) :**
- `src/app/(app)/(tabs)/index.tsx` (home) — consomme `<ArticleCard article={item} />`, suit le changement de FeedItem automatiquement
- `src/components/TrustDetailSheet.tsx` — le mot "sources" trouvé est dans un texte UI, pas un consommateur de champ
- `src/services/feed/feedTypes.ts > OverviewResponse` — `totalSources/activeSources/sourceBreakdown` sont les noms legacy renvoyés par le backend, on respecte iso backend

**Endpoints backend cible :**
- `GET /api/publishers` → `{ success: true, data: { publishers: [{ id, name, slug, domain?, description?, logoUrl?, trustScore?, status, createdAt, updatedAt }] } }`
- `POST /api/publisher-subscriptions/:publisherId/toggle` → `{ success: true, data: { subscribed: boolean } }`
- `GET /api/publisher-subscriptions` → `{ success: true, data: [{ id, userId, publisherId, createdAt, publisher: { id, name, slug } }] }`
- `GET /api/publisher-subscriptions/:publisherId/count` → `{ success: true, data: { count: number } }`

**Shape `FeedItem` backend confirmé (feedService.ts:44-45) :**
```ts
{ id, title, content, url, author, publishedAt, imageUrl, relevanceScore, trustScore,
  source: { id, name, type: 'RSS' },        // legacy, à IGNORER côté mobile
  publisher: { id, name, slug },
  summary, likesCount, commentsCount, isLiked, createdAt, ... }
```

**Critères de done (à valider en Task 7) :**
1. `pnpm tsc --noEmit` zéro erreur
2. `pnpm test` tous verts
3. `pnpm lint` pas de régression
4. `grep -rE "(sourceService|subscriptionService|/api/sources|/api/subscriptions|SourceCard)" src/` → zéro hit
5. Smoke device (par l'user)
6. Merge ff sur main + CI/CD vert
7. `MEMORY.md` mis à jour

---

## Task 0 : Préparer la branche

**Files :** repo `frontend-mobile/`

- [ ] **Step 1 : Vérifier l'état du repo**

```bash
cd /Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile
git status
git branch --show-current
```

Attendu : working tree clean, branche `main`.

- [ ] **Step 2 : Pull main à jour**

```bash
git fetch origin
git pull --ff-only origin main
```

- [ ] **Step 3 : Créer la branche**

```bash
git checkout -b feat/publisher-channel-mobile-adapt
```

---

## Task 1 : Service `publisherService` (TDD)

**Files :**
- Create : `src/services/publisher/publisherTypes.ts`
- Create : `src/services/publisher/publisherService.ts`
- Create : `src/tests/services/publisherService.test.ts`

- [ ] **Step 1 : Écrire les types**

`src/services/publisher/publisherTypes.ts` :

```ts
export type PublisherStatus = "ACTIVE" | "INACTIVE" | "PENDING" | "BLACKLISTED"

export interface Publisher {
  id: number
  name: string
  slug: string
  domain: string | null
  description: string | null
  logoUrl: string | null
  trustScore: number | null
  status: PublisherStatus
  createdAt: string
  updatedAt: string
}

export type GetPublishersResult =
  | { kind: "ok"; data: Publisher[] }
  | { kind: "error"; message: string }

export type GetPublisherResult =
  | { kind: "ok"; data: Publisher }
  | { kind: "error"; message: string }
```

- [ ] **Step 2 : Écrire le test failing**

`src/tests/services/publisherService.test.ts` :

```ts
import { publisherService } from "@/services/publisher/publisherService"
import { api } from "@/services/api"

jest.mock("@/services/api", () => ({
  api: { apisauce: { get: jest.fn(), post: jest.fn(), put: jest.fn(), patch: jest.fn(), delete: jest.fn() } },
}))

const mockGet = api.apisauce.get as jest.Mock

describe("publisherService", () => {
  beforeEach(() => jest.clearAllMocks())

  describe("getAll", () => {
    it("returns publishers on success", async () => {
      mockGet.mockResolvedValue({
        ok: true,
        data: { success: true, data: { publishers: [{ id: 1, name: "TechCrunch", slug: "techcrunch" }] } },
      })
      const result = await publisherService.getAll()
      expect(result.kind).toBe("ok")
      if (result.kind === "ok") expect(result.data[0].name).toBe("TechCrunch")
    })
    it("returns error on failure", async () => {
      mockGet.mockResolvedValue({ ok: false, problem: "SERVER_ERROR" })
      expect((await publisherService.getAll()).kind).toBe("error")
    })
  })

  describe("getById", () => {
    it("returns a publisher on success", async () => {
      mockGet.mockResolvedValue({
        ok: true,
        data: { success: true, data: { publisher: { id: 3, name: "Le Monde", slug: "le-monde" } } },
      })
      const result = await publisherService.getById(3)
      expect(result.kind).toBe("ok")
      if (result.kind === "ok") expect(result.data.id).toBe(3)
    })
    it("returns error on failure", async () => {
      mockGet.mockResolvedValue({ ok: false, problem: "NOT_FOUND" })
      expect((await publisherService.getById(999)).kind).toBe("error")
    })
  })
})
```

- [ ] **Step 3 : Lancer le test, vérifier qu'il échoue**

```bash
pnpm test src/tests/services/publisherService.test.ts
```

Attendu : `Cannot find module '@/services/publisher/publisherService'`.

- [ ] **Step 4 : Écrire l'implémentation minimale**

`src/services/publisher/publisherService.ts` :

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { GetPublishersResult, GetPublisherResult } from "./publisherTypes"

export const publisherService = {
  getAll: async (): Promise<GetPublishersResult> => {
    const response = await api.apisauce.get("/api/publishers")
    if (response.ok && response.data && (response.data as any).success) {
      const payload = (response.data as any).data
      const list = payload.publishers ?? payload
      return { kind: "ok", data: list }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getById: async (id: number): Promise<GetPublisherResult> => {
    const response = await api.apisauce.get(`/api/publishers/${id}`)
    if (response.ok && response.data && (response.data as any).success) {
      const payload = (response.data as any).data
      const publisher = payload.publisher ?? payload
      return { kind: "ok", data: publisher }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 5 : Relancer le test, vérifier qu'il passe**

```bash
pnpm test src/tests/services/publisherService.test.ts
```

Attendu : 4 tests passent.

---

## Task 2 : Service `publisherSubscriptionService` (TDD)

**Files :**
- Create : `src/services/publisherSubscription/publisherSubscriptionTypes.ts`
- Create : `src/services/publisherSubscription/publisherSubscriptionService.ts`
- Create : `src/tests/services/publisherSubscriptionService.test.ts`

- [ ] **Step 1 : Écrire les types**

`src/services/publisherSubscription/publisherSubscriptionTypes.ts` :

```ts
export interface SubscribedPublisher {
  id: number
  name: string
  slug: string
}

export interface PublisherSubscription {
  id: number
  userId: number
  publisherId: number
  createdAt: string
  publisher: SubscribedPublisher
}

export type TogglePublisherSubscriptionResult =
  | { kind: "ok"; data: { subscribed: boolean } }
  | { kind: "error"; message: string }

export type GetMyPublisherSubscriptionsResult =
  | { kind: "ok"; data: PublisherSubscription[] }
  | { kind: "error"; message: string }

export type GetPublisherSubscriberCountResult =
  | { kind: "ok"; data: { count: number } }
  | { kind: "error"; message: string }
```

- [ ] **Step 2 : Écrire le test failing**

`src/tests/services/publisherSubscriptionService.test.ts` :

```ts
import { publisherSubscriptionService } from "@/services/publisherSubscription/publisherSubscriptionService"
import { api } from "@/services/api"

jest.mock("@/services/api", () => ({
  api: { apisauce: { get: jest.fn(), post: jest.fn(), put: jest.fn(), patch: jest.fn(), delete: jest.fn() } },
}))

const mockGet = api.apisauce.get as jest.Mock
const mockPost = api.apisauce.post as jest.Mock

describe("publisherSubscriptionService", () => {
  beforeEach(() => jest.clearAllMocks())

  describe("toggle", () => {
    it("returns subscribed status on success", async () => {
      mockPost.mockResolvedValue({ ok: true, data: { success: true, data: { subscribed: true } } })
      const result = await publisherSubscriptionService.toggle(1)
      expect(result.kind).toBe("ok")
    })
    it("returns error on failure", async () => {
      mockPost.mockResolvedValue({ ok: false, problem: "SERVER_ERROR" })
      expect((await publisherSubscriptionService.toggle(1)).kind).toBe("error")
    })
  })

  describe("getMine", () => {
    it("returns subscriptions on success", async () => {
      mockGet.mockResolvedValue({
        ok: true,
        data: { success: true, data: { subscriptions: [{ id: 1, userId: 10, publisherId: 3, createdAt: "x", publisher: { id: 3, name: "Le Monde", slug: "le-monde" } }] } },
      })
      const result = await publisherSubscriptionService.getMine()
      expect(result.kind).toBe("ok")
      if (result.kind === "ok") expect(result.data[0].publisher.slug).toBe("le-monde")
    })
    it("returns error on failure", async () => {
      mockGet.mockResolvedValue({ ok: false, problem: "SERVER_ERROR" })
      expect((await publisherSubscriptionService.getMine()).kind).toBe("error")
    })
  })

  describe("getSubscriberCount", () => {
    it("returns count on success", async () => {
      mockGet.mockResolvedValue({ ok: true, data: { success: true, data: { count: 42 } } })
      const result = await publisherSubscriptionService.getSubscriberCount(1)
      expect(result.kind).toBe("ok")
      if (result.kind === "ok") expect(result.data.count).toBe(42)
    })
    it("returns error on failure", async () => {
      mockGet.mockResolvedValue({ ok: false, problem: "SERVER_ERROR" })
      expect((await publisherSubscriptionService.getSubscriberCount(1)).kind).toBe("error")
    })
  })
})
```

- [ ] **Step 3 : Lancer le test, vérifier qu'il échoue**

```bash
pnpm test src/tests/services/publisherSubscriptionService.test.ts
```

Attendu : `Cannot find module '@/services/publisherSubscription/publisherSubscriptionService'`.

- [ ] **Step 4 : Écrire l'implémentation minimale**

`src/services/publisherSubscription/publisherSubscriptionService.ts` :

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  TogglePublisherSubscriptionResult,
  GetMyPublisherSubscriptionsResult,
  GetPublisherSubscriberCountResult,
} from "./publisherSubscriptionTypes"

export const publisherSubscriptionService = {
  toggle: async (publisherId: number): Promise<TogglePublisherSubscriptionResult> => {
    const response = await api.apisauce.post(`/api/publisher-subscriptions/${publisherId}/toggle`)
    if (response.ok && response.data && (response.data as any).success) {
      return { kind: "ok", data: (response.data as any).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getMine: async (): Promise<GetMyPublisherSubscriptionsResult> => {
    const response = await api.apisauce.get("/api/publisher-subscriptions")
    if (response.ok && response.data && (response.data as any).success) {
      const payload = (response.data as any).data
      const list = payload.subscriptions ?? payload
      return { kind: "ok", data: list }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getSubscriberCount: async (publisherId: number): Promise<GetPublisherSubscriberCountResult> => {
    const response = await api.apisauce.get(`/api/publisher-subscriptions/${publisherId}/count`)
    if (response.ok && response.data && (response.data as any).success) {
      return { kind: "ok", data: (response.data as any).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 5 : Relancer le test, vérifier qu'il passe**

```bash
pnpm test src/tests/services/publisherSubscriptionService.test.ts
```

Attendu : 6 tests passent.

- [ ] **Step 6 : Commit 1**

```bash
cd /Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile
git add src/services/publisher src/services/publisherSubscription src/tests/services/publisherService.test.ts src/tests/services/publisherSubscriptionService.test.ts
git commit -m "feat(mobile): add publisherService + publisherSubscriptionService + types + tests"
```

---

## Task 3 : Switch `FeedItem` shape + adapter tous les consommateurs

**Files :**
- Modify : `src/services/feed/feedTypes.ts`
- Modify : `src/services/bookmark/bookmarkTypes.ts`
- Modify : `src/components/ArticleCard.tsx`
- Modify : `src/app/(app)/article/[id].tsx`
- Modify : `src/app/(app)/(tabs)/profile.tsx` (ligne ~129 du mapping bookmark)

- [ ] **Step 1 : Modifier `feedTypes.ts`**

Remplacer dans `src/services/feed/feedTypes.ts` lignes 1-23 :

```ts
export interface FeedPublisher {
  id: number
  name: string
  slug: string
}

export interface FeedItem {
  id: number
  title: string
  content: string | null
  url: string | null
  author: string | null
  publishedAt: string | null
  imageUrl: string | null
  relevanceScore: number | null
  trustScore: number | null
  publisher: FeedPublisher
  summary: string | null
  likesCount: number
  commentsCount: number
  isLiked: boolean
  createdAt: string
}
```

(retrait de `FeedSource` et de `source: FeedSource`, ajout de `FeedPublisher` et `publisher: FeedPublisher`)

- [ ] **Step 2 : Modifier `bookmarkTypes.ts`**

Remplacer dans `src/services/bookmark/bookmarkTypes.ts` lignes 1-15 :

```ts
export interface BookmarkItem {
  id: number
  userId: number
  itemId: number
  createdAt: string
  item: {
    id: number
    title: string
    content: string | null
    url: string | null
    imageUrl: string | null
    publishedAt: string | null
    publisher: { id: number; name: string; slug?: string }
  }
}
```

(remplacement de `source: { id, name, type }` par `publisher: { id, name, slug? }`. `slug?` est optionnel car le backend `BookmarkService.getUserBookmarks` ne retourne que `{ id, name }`.)

- [ ] **Step 3 : Modifier `ArticleCard.tsx`**

Dans `src/components/ArticleCard.tsx`, remplacer la ligne 20 :

```ts
  const sourceName = article.publisher?.name ?? "Source"
```

(seule cette ligne change ; `sourceInitials` à la ligne 21 et tout le reste continuent de marcher car ils s'appuient sur `sourceName`)

- [ ] **Step 4 : Modifier `article/[id].tsx`**

Dans `src/app/(app)/article/[id].tsx`, remplacer ligne 148 :

```tsx
                {article.publisher?.name ?? "Source"}
```

- [ ] **Step 5 : Modifier `profile.tsx` (mapping bookmark ligne ~129)**

Dans `src/app/(app)/(tabs)/profile.tsx`, remplacer la ligne 129 :

```tsx
                publisher: item.item.publisher,
```

(on remplace `source: item.item.source` par `publisher: item.item.publisher`)

- [ ] **Step 6 : Vérifier que TypeScript compile**

```bash
pnpm tsc --noEmit
```

Attendu : zéro erreur sur les fichiers modifiés ci-dessus. Si erreurs résiduelles sur les imports de `sourceService` ou `subscriptionService` dans `discover.tsx` ou `profile.tsx`, c'est attendu — elles seront résolues en Task 5 (rename queryKeys) puis Task 6 (cleanup). Pour faire passer `tsc` ici, on garde ces imports tels quels (le code obsolète reste compilable même s'il appellera des 404 à l'exécution).

Si TS échoue uniquement à cause des modifs Task 3 : corriger avant de continuer.

- [ ] **Step 7 : Lancer les tests pertinents**

```bash
pnpm test
```

Attendu : tous les tests passent (les 11 + les 2 nouveaux).

- [ ] **Step 8 : Commit 2**

```bash
git add src/services/feed/feedTypes.ts src/services/bookmark/bookmarkTypes.ts src/components/ArticleCard.tsx "src/app/(app)/article/[id].tsx" "src/app/(app)/(tabs)/profile.tsx"
git commit -m "refactor(mobile): switch FeedItem shape (source to publisher) + adapt consumers"
```

---

## Task 4 : Renommer les `queryKeys`

**Files :**
- Modify : `src/hooks/queryKeys.ts`
- Modify : `src/app/(app)/(tabs)/discover.tsx` (ajustement des keys utilisées)
- Modify : `src/app/(app)/(tabs)/profile.tsx` (idem)

- [ ] **Step 1 : Modifier `queryKeys.ts`**

Remplacer dans `src/hooks/queryKeys.ts` les blocs `subscriptions` et `sources` :

```ts
  publisherSubscriptions: {
    mine: ["publisherSubscriptions", "mine"] as const,
    count: (publisherId: number) => ["publisherSubscriptions", publisherId, "count"] as const,
  },
  publishers: {
    all: ["publishers"] as const,
  },
```

(remplace `subscriptions` et `sources`)

- [ ] **Step 2 : Adapter `discover.tsx` aux nouvelles keys**

Dans `src/app/(app)/(tabs)/discover.tsx`, remplacer toutes les références :
- `queryKeys.sources.all` → `queryKeys.publishers.all`
- `queryKeys.subscriptions.mine` → `queryKeys.publisherSubscriptions.mine`

(à ce stade les services `sourceService` et `subscriptionService` sont encore référencés — c'est Task 5 qui les remplace)

- [ ] **Step 3 : Adapter `profile.tsx` aux nouvelles keys**

Dans `src/app/(app)/(tabs)/profile.tsx` :
- `queryKeys.subscriptions.mine` → `queryKeys.publisherSubscriptions.mine` (2 occurrences : `queryFn` et `invalidateQueries`)

- [ ] **Step 4 : Vérifier que TypeScript compile**

```bash
pnpm tsc --noEmit
```

Attendu : zéro erreur introduite par cette tâche. Les imports `sourceService`/`subscriptionService` restent (à supprimer en Task 6).

- [ ] **Step 5 : Lancer les tests**

```bash
pnpm test
```

Attendu : tous verts.

- [ ] **Step 6 : Commit 3**

```bash
git add src/hooks/queryKeys.ts "src/app/(app)/(tabs)/discover.tsx" "src/app/(app)/(tabs)/profile.tsx"
git commit -m "refactor(mobile): rename queryKeys (sources/subscriptions to publishers/publisher-subscriptions)"
```

---

## Task 5 : Renommer `SourceCard` en `PublisherCard` + adapter `discover.tsx` aux nouveaux services

**Files :**
- Create : `src/components/PublisherCard.tsx`
- Delete : `src/components/SourceCard.tsx`
- Modify : `src/app/(app)/(tabs)/discover.tsx`
- Modify : `src/app/(app)/(tabs)/profile.tsx`

- [ ] **Step 1 : Créer `PublisherCard.tsx`**

`src/components/PublisherCard.tsx` :

```tsx
import { Pressable, StyleSheet } from "react-native"
import { XStack, YStack, Text } from "tamagui"
import { Ionicons } from "@expo/vector-icons"
import { useColors } from "@/design/colors"

interface PublisherCardProps {
  publisher: { id: number; name: string; slug?: string; subscriberCount?: number }
  isSubscribed: boolean
  onToggleSubscribe: () => void
}

export function PublisherCard({ publisher, isSubscribed, onToggleSubscribe }: PublisherCardProps) {
  const c = useColors()

  return (
    <XStack
      alignItems="center"
      gap={12}
      paddingVertical={12}
      paddingHorizontal={4}
      borderBottomWidth={0.5}
      borderBottomColor="$borderMuted"
    >
      <XStack
        width={44}
        height={44}
        borderRadius={12}
        backgroundColor="$surface2"
        alignItems="center"
        justifyContent="center"
      >
        <Ionicons name="newspaper-outline" size={20} color={c.color2} />
      </XStack>

      <YStack flex={1} gap={2}>
        <Text color="$color" fontSize={15} fontWeight="700">
          {publisher.name}
        </Text>
        {publisher.subscriberCount !== undefined && (
          <Text color="$color3" fontSize={13}>
            {publisher.subscriberCount} abonnes
          </Text>
        )}
      </YStack>

      <Pressable
        onPress={onToggleSubscribe}
        style={[
          styles.btn,
          {
            backgroundColor: isSubscribed ? "transparent" : c.color,
            borderColor: isSubscribed ? c.border : c.color,
          },
        ]}
      >
        <Text color={isSubscribed ? "$color2" : "white"} fontSize={13} fontWeight="600">
          {isSubscribed ? "Abonne" : "S'abonner"}
        </Text>
      </Pressable>
    </XStack>
  )
}

const styles = StyleSheet.create({
  btn: {
    paddingHorizontal: 14,
    paddingVertical: 7,
    borderRadius: 16,
    borderWidth: 1,
  },
})
```

(différence vs `SourceCard.tsx` : prop `publisher` au lieu de `source`, plus de champ `type` affiché, garde `subscriberCount` optionnel)

- [ ] **Step 2 : Adapter `discover.tsx`**

Dans `src/app/(app)/(tabs)/discover.tsx` :

1. Remplacer les imports :
```tsx
import { PublisherCard } from "@/components/PublisherCard"
import { publisherService } from "@/services/publisher/publisherService"
import { publisherSubscriptionService } from "@/services/publisherSubscription/publisherSubscriptionService"
```
(retirer les imports `SourceCard`, `sourceService`, `subscriptionService`)

2. Remplacer le label du tab :
```tsx
const DISCOVER_TABS = [
  { key: "trending", label: "Tendances" },
  { key: "recent", label: "Recents" },
  { key: "publishers", label: "Editeurs" },
]
```

3. Remplacer la chaîne de comparaison `activeTab === "sources"` partout dans le fichier par `activeTab === "publishers"`.

4. Remplacer les query functions :
```tsx
  const publishersQuery = useQuery({
    queryKey: queryKeys.publishers.all,
    queryFn: async () => {
      const result = await publisherService.getAll()
      if (result.kind === "ok") return result.data
      throw new Error(result.message)
    },
    enabled: activeTab === "publishers",
  })

  const mySubsQuery = useQuery({
    queryKey: queryKeys.publisherSubscriptions.mine,
    queryFn: async () => {
      const result = await publisherSubscriptionService.getMine()
      if (result.kind === "ok") return result.data
      throw new Error(result.message)
    },
    enabled: activeTab === "publishers",
  })
```
(la variable `sourcesQuery` est renommée `publishersQuery`)

5. Adapter le `subscribedIds` :
```tsx
  const subscribedIds = new Set((mySubsQuery.data ?? []).map((s) => s.publisherId))
```
(avant : `.map((s) => s.id)` qui pointait sur `Source.id` ; maintenant on a `PublisherSubscription` avec `publisherId`)

6. Adapter la mutation :
```tsx
  const subscribeMutation = useMutation({
    mutationFn: (publisherId: number) => publisherSubscriptionService.toggle(publisherId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.publisherSubscriptions.mine })
      queryClient.invalidateQueries({ queryKey: queryKeys.feed.all })
    },
    onError: () => setToastMsg("Erreur de connexion"),
  })
```

7. Adapter le `FlatList` du tab `publishers` :
```tsx
        {activeTab === "publishers" ? (
          <FlatList
            data={publishersQuery.data ?? []}
            keyExtractor={(item) => String(item.id)}
            contentContainerStyle={styles.list}
            renderItem={({ item }) => (
              <PublisherCard
                publisher={item}
                isSubscribed={subscribedIds.has(item.id)}
                onToggleSubscribe={() => subscribeMutation.mutate(item.id)}
              />
            )}
            ListEmptyComponent={<EmptyState icon="newspaper-outline" title="Aucun editeur disponible" />}
          />
```

- [ ] **Step 3 : Adapter `profile.tsx`**

Dans `src/app/(app)/(tabs)/profile.tsx` :

1. Remplacer les imports :
```tsx
import { PublisherCard } from "@/components/PublisherCard"
import { publisherSubscriptionService } from "@/services/publisherSubscription/publisherSubscriptionService"
```
(retirer `SourceCard` et `subscriptionService`)

2. Remplacer `subsQuery` :
```tsx
  const subsQuery = useQuery({
    queryKey: queryKeys.publisherSubscriptions.mine,
    queryFn: async () => {
      const result = await publisherSubscriptionService.getMine()
      if (result.kind === "ok") return result.data
      throw new Error(result.message)
    },
    enabled: activeTab === "following",
  })
```

3. Remplacer la mutation `unsubMutation` :
```tsx
  const unsubMutation = useMutation({
    mutationFn: (publisherId: number) => publisherSubscriptionService.toggle(publisherId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.publisherSubscriptions.mine })
      queryClient.invalidateQueries({ queryKey: queryKeys.feed.all })
    },
  })
```

4. Adapter le bloc `following` (lignes ~152-178). Remplacer :
```tsx
  if (activeTab === "following") {
    const subscribedIds = new Set((subsQuery.data ?? []).map((s) => s.publisherId))
    return (
      <SafeAreaView style={[styles.safe, { backgroundColor: c.bg }]} edges={["top"]}>
        <FlatList
          data={subsQuery.data ?? []}
          keyExtractor={(item) => String(item.id)}
          contentContainerStyle={styles.list}
          ListHeaderComponent={header}
          renderItem={({ item }) => (
            <PublisherCard
              publisher={item.publisher}
              isSubscribed={subscribedIds.has(item.publisherId)}
              onToggleSubscribe={() => unsubMutation.mutate(item.publisherId)}
            />
          )}
          ListEmptyComponent={
            <EmptyState
              icon="newspaper-outline"
              title="Aucun abonnement"
              subtitle="Abonnez-vous a des editeurs dans Decouvrir"
            />
          }
        />
      </SafeAreaView>
    )
  }
```

(la donnée `subsQuery.data` est maintenant `PublisherSubscription[]`, donc chaque `item` a `publisherId` + `publisher: { id, name, slug }`. On passe `item.publisher` à `<PublisherCard>`.)

- [ ] **Step 4 : Vérifier que TypeScript compile**

```bash
pnpm tsc --noEmit
```

Attendu : zéro erreur.

- [ ] **Step 5 : Lancer les tests**

```bash
pnpm test
```

Attendu : tous verts.

- [ ] **Step 6 : Commit 4**

```bash
git add src/components/PublisherCard.tsx "src/app/(app)/(tabs)/discover.tsx" "src/app/(app)/(tabs)/profile.tsx"
git commit -m "refactor(mobile): adapt discover/profile to publisher services + add PublisherCard"
```

---

## Task 6 : Nettoyage — supprimer les artefacts obsolètes

**Files (delete) :**
- `src/services/source/sourceService.ts`
- `src/services/source/sourceTypes.ts`
- `src/services/source/` (dossier vide)
- `src/services/subscription/subscriptionService.ts`
- `src/services/subscription/subscriptionTypes.ts`
- `src/services/subscription/` (dossier vide)
- `src/components/SourceCard.tsx`
- `src/tests/services/sourceService.test.ts`
- `src/tests/services/subscriptionService.test.ts`

- [ ] **Step 1 : Supprimer les fichiers et dossiers obsolètes**

```bash
cd /Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile
rm -rf src/services/source src/services/subscription
rm src/components/SourceCard.tsx
rm src/tests/services/sourceService.test.ts src/tests/services/subscriptionService.test.ts
```

- [ ] **Step 2 : Vérifier qu'aucune référence morte ne subsiste**

```bash
grep -rE "(sourceService|subscriptionService|/api/sources|/api/subscriptions|SourceCard)" src/
```

Attendu : zéro hit. Si hit → ajouter la correction au commit avant de continuer.

- [ ] **Step 3 : TypeScript compile**

```bash
pnpm tsc --noEmit
```

Attendu : zéro erreur.

- [ ] **Step 4 : Tests passent**

```bash
pnpm test
```

Attendu : tous verts. Compte de tests : 19 anciens − 2 (source+sub) + 10 (publisher+publisherSub) = 27. (Le compte exact peut varier selon les `it()` ajoutés ; viser approximativement.)

- [ ] **Step 5 : Lint (rapport de régression)**

```bash
pnpm lint 2>&1 | tail -20
```

Attendu : compteur erreurs/warnings ≤ baseline d'avant le chantier. Si régression nette → corriger les nouveaux warnings introduits par nos fichiers (`publisherService.ts`, `publisherSubscriptionService.ts`, `PublisherCard.tsx`, etc.).

- [ ] **Step 6 : Commit 5**

```bash
git add -A
git commit -m "chore(mobile): remove obsolete source/subscription services + tests + SourceCard"
```

---

## Task 7 : Validation finale, push, merge, mémoire

**Files :**
- Modify : `/Users/ilhan.neuville/.claude/projects/-Users-ilhan-neuville-Desktop-eip-Syntheza/memory/MEMORY.md`
- Modify : `/Users/ilhan.neuville/.claude/projects/-Users-ilhan-neuville-Desktop-eip-Syntheza/memory/frontend-architecture.md`

- [ ] **Step 1 : Re-vérifier les 4 critères automatiques de "done"**

```bash
cd /Users/ilhan.neuville/Desktop/eip/Syntheza/frontend-mobile
pnpm tsc --noEmit
pnpm test
pnpm lint 2>&1 | tail -5
grep -rE "(sourceService|subscriptionService|/api/sources|/api/subscriptions|SourceCard)" src/ && echo "FAIL: ref morte" || echo "OK: zero ref morte"
```

Attendu : 4 lignes vertes.

- [ ] **Step 2 : Push de la branche**

```bash
git push -u origin feat/publisher-channel-mobile-adapt
```

- [ ] **Step 3 : STOP — handoff pour smoke test device**

⚠️ **Avant le merge sur main, l'utilisateur doit smoke-tester sur device réel ou Expo Go** avec l'API prod `https://api.syntheza.ovh` :

1. Login `dev@syntheza.app` / `password123` → OK
2. Tab Discover → onglet "Éditeurs" → liste affiche TechCrunch, Hacker News, Le Monde
3. Toggle "S'abonner" sur un publisher → bouton passe à "Abonné", reste après refresh
4. Tab Home → feed affiche les items avec le nom du publisher
5. Tap article → écran détail affiche le nom du publisher

Si OK → continuer à Step 4. Si KO → ouvrir un nouveau commit fix sur la même branche.

- [ ] **Step 4 : Merge fast-forward sur main**

```bash
git checkout main
git pull --ff-only origin main
git merge --ff-only feat/publisher-channel-mobile-adapt
git push origin main
```

- [ ] **Step 5 : Vérifier que la CI/CD passe**

Surveiller GitHub Actions sur le repo frontend-mobile. Attendu : build EAS preview vert.

- [ ] **Step 6 : Supprimer la branche locale et distante (optionnel)**

```bash
git branch -d feat/publisher-channel-mobile-adapt
git push origin --delete feat/publisher-channel-mobile-adapt
```

- [ ] **Step 7 : Mettre à jour la mémoire `MEMORY.md`**

Dans `/Users/ilhan.neuville/.claude/projects/-Users-ilhan-neuville-Desktop-eip-Syntheza/memory/MEMORY.md` :

- Mettre à jour le bloc "État du projet" : remplacer "Frontend mobile : ... NON adapté au refacto — chantier en cours" par "Frontend mobile : adapté au refacto Publisher/Channel/Item ✅, X tests passent, build EAS preview vert ✅"
- Mettre à jour le bloc "Points restants" : retirer le point 1 (frontend mobile adapté), renuméroter les suivants

- [ ] **Step 8 : Mettre à jour `frontend-architecture.md`**

Dans `/Users/ilhan.neuville/.claude/projects/-Users-ilhan-neuville-Desktop-eip-Syntheza/memory/frontend-architecture.md` :

- Retirer l'avertissement `> ⚠️ À jour à mi-mars 2026...` en haut
- Mettre à jour la liste des services mobile : remplacer `source/`, `subscription/` par `publisher/`, `publisherSubscription/`
- Mettre à jour la date d'à-jour

- [ ] **Step 9 : Annoncer le chantier terminé à l'utilisateur**

Message bref : "Chantier mobile adapté au refacto Publisher/Channel/Item terminé. X tests verts. Merge ff sur main, CI/CD vert. Mémoire à jour. Prochain chantier candidat : refonte Discover autour de Publishers ou Suggestion/Vote (cf. `future_features_mobile.md`)."

---

## Self-Review

**1. Spec coverage**

| Décision lockée | Tâche couvrante |
|---|---|
| Scope mapping 1-1 | T1, T2 (services), T4 (queryKeys), T5 (écrans + cleanup composant) |
| FeedItem iso backend | T3 (switch shape) |
| Types handwritten | T1, T2, T3 (tous les types écrits à la main) |
| Tests port iso | T1 (4 tests), T2 (6 tests) — couverture équivalente aux 2 anciens (2+3 it) |
| Branche unique 5 commits | T0 (branche), T1+T2 commit 1, T3 commit 2, T4 commit 3, T5 commit 4, T6 commit 5 |
| 7 critères done | T6 (tsc/test/lint/grep), T7 (smoke device + push + CI + mémoire) |

Couverture : 7/7 décisions ✅

**2. Placeholder scan** : zéro `TBD`/`TODO`/`fill in`/`similar to` ✅

**3. Type consistency** :
- `Publisher.status` typé en T1, jamais re-typé ✅
- `PublisherSubscription.publisher` (slug obligatoire) en T2 vs `BookmarkItem.item.publisher` (slug optionnel) en T3 → divergence assumée : le backend bookmark ne retourne que `{ id, name }` (vérifié dans `backend/src/services/bookmarkService.ts:27`), donc `slug?` côté bookmark est correct.
- `PublisherCard.tsx` prop `publisher: { id, name, slug?, subscriberCount? }` ✅ accepte autant un `Publisher` complet (T1) qu'un `SubscribedPublisher` (T2) qu'un `BookmarkItem.item.publisher` (slug optionnel) — large enough.
- `subscribedIds` en T5 Step 2 utilise `.publisherId` (champ de `PublisherSubscription`) — correct.

Aucun bug de cohérence détecté ✅
