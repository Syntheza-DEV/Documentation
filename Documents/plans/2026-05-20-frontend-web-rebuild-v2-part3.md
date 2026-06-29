# Frontend Web Rebuild v2 — Part 3 (Phases 14-23)

> Continuation of `2026-05-20-frontend-web-rebuild-v2-part2.md`. Refer to the header and overview in Part 1.

---

## Phase 14 — Home (Feed) screen

**Files:** Replace `src/routes/(app)/home.tsx`

- [ ] **Step 14.1: Install infinite-scroll observer helper (use IntersectionObserver native + a small hook)**

Create `src/hooks/useInView.ts`:

```ts
import { useEffect, useRef, useState } from "react"

export function useInView<T extends HTMLElement>(options?: IntersectionObserverInit) {
  const ref = useRef<T | null>(null)
  const [inView, setInView] = useState(false)

  useEffect(() => {
    const el = ref.current
    if (!el) return
    const observer = new IntersectionObserver(([entry]) => setInView(entry.isIntersecting), options)
    observer.observe(el)
    return () => observer.disconnect()
  }, [options])

  return { ref, inView }
}
```

- [ ] **Step 14.2: Replace `src/routes/(app)/home.tsx`**

```tsx
import { useMemo, useState } from "react"
import { useInfiniteQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { useNavigate } from "react-router-dom"
import { useAuth } from "@/contexts/AuthContext"
import { feedService } from "@/services/feed/feedService"
import { likeService } from "@/services/like/likeService"
import { bookmarkService } from "@/services/bookmark/bookmarkService"
import { queryKeys } from "@/hooks/queryKeys"
import { useInView } from "@/hooks/useInView"
import { ArticleCard, ArticleCardSkeleton, EmptyState, SectionHeader, Toast } from "@/components"
import type { FeedItem } from "@/services/feed/feedTypes"

const PAGE_SIZE = 20

export default function HomePage() {
  const { user } = useAuth()
  const navigate = useNavigate()
  const queryClient = useQueryClient()
  const [toast, setToast] = useState<string>("")
  const [bookmarkedIds, setBookmarkedIds] = useState<Set<number>>(new Set())

  const { data, fetchNextPage, hasNextPage, isFetchingNextPage, isLoading, isError, refetch } =
    useInfiniteQuery({
      queryKey: queryKeys.feed.all,
      queryFn: async ({ pageParam }) => {
        const result = await feedService.getFeed({ page: pageParam, limit: PAGE_SIZE })
        if (result.kind === "ok") return result.data
        throw new Error(result.message)
      },
      initialPageParam: 1 as number,
      getNextPageParam: (last) => (last.page < last.totalPages ? last.page + 1 : undefined),
    })

  const articles = useMemo(() => {
    if (!data?.pages) return []
    const seen = new Set<number>()
    const items: FeedItem[] = []
    for (const page of data.pages) {
      for (const item of page.items) {
        if (!seen.has(item.id)) {
          seen.add(item.id)
          items.push(item)
        }
      }
    }
    return items
  }, [data])

  const likeMutation = useMutation({
    mutationFn: (id: number) => likeService.toggle(id),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.feed.all }),
    onError: () => setToast("Erreur de connexion"),
  })

  const bookmarkMutation = useMutation({
    mutationFn: (id: number) => bookmarkService.toggle(id),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.bookmarks.all }),
    onError: () => setToast("Erreur de connexion"),
  })

  const { ref, inView } = useInView<HTMLDivElement>({ rootMargin: "200px" })
  if (inView && hasNextPage && !isFetchingNextPage) void fetchNextPage()

  return (
    <div className="space-y-4">
      <header className="flex items-center justify-between">
        <h1 className="text-2xl font-bold tracking-tight text-color">
          Bonjour {user?.name ?? "Utilisateur"} 👋
        </h1>
        <button
          onClick={() => void refetch()}
          className="rounded-md border border-border bg-surface px-3 py-1.5 text-sm text-color2 hover:bg-hover"
        >
          Rafraîchir
        </button>
      </header>
      <SectionHeader title="Votre fil d'actualité" />

      {isLoading ? (
        <div className="space-y-3">
          {[1, 2, 3].map((i) => (
            <ArticleCardSkeleton key={i} />
          ))}
        </div>
      ) : isError ? (
        <EmptyState title="Erreur de connexion" subtitle="Vérifiez votre connexion et réessayez" />
      ) : articles.length === 0 ? (
        <EmptyState
          title="Aucun article"
          subtitle="Abonnez-vous à des éditeurs depuis Découvrir pour remplir votre feed."
        />
      ) : (
        <div className="space-y-3">
          {articles.map((article) => (
            <ArticleCard
              key={article.id}
              article={article}
              isBookmarked={bookmarkedIds.has(article.id)}
              onPress={() => navigate(`/article/${article.id}`)}
              onLike={() => likeMutation.mutate(article.id)}
              onBookmark={() => {
                setBookmarkedIds((prev) => {
                  const next = new Set(prev)
                  if (next.has(article.id)) next.delete(article.id)
                  else next.add(article.id)
                  return next
                })
                bookmarkMutation.mutate(article.id)
              }}
            />
          ))}
          <div ref={ref} className="h-10" />
          {isFetchingNextPage && <ArticleCardSkeleton />}
        </div>
      )}

      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </div>
  )
}
```

- [ ] **Step 14.3: Verify in browser**

```bash
pnpm dev
```

Manual: navigate to `/home`, see 20 articles, scroll, see next 20 loaded, click heart → count updates.

- [ ] **Step 14.4: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): Home/Feed screen with infinite scroll, likes, bookmarks"
```

---

## Phase 15 — Discover screen

**Files:** Replace `src/routes/(app)/discover.tsx`

- [ ] **Step 15.1: Replace `src/routes/(app)/discover.tsx`**

```tsx
import { useState } from "react"
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { useNavigate } from "react-router-dom"
import { feedService } from "@/services/feed/feedService"
import { publisherService } from "@/services/publisher/publisherService"
import { publisherSubscriptionService } from "@/services/publisherSubscription/publisherSubscriptionService"
import { queryKeys } from "@/hooks/queryKeys"
import { ArticleCard, ArticleCardSkeleton, EmptyState, PublisherCard, TabSelector, Toast } from "@/components"

const DISCOVER_TABS = [
  { key: "trending", label: "Tendances" },
  { key: "recent", label: "Récents" },
  { key: "publishers", label: "Éditeurs" },
]

export default function DiscoverPage() {
  const navigate = useNavigate()
  const queryClient = useQueryClient()
  const [activeTab, setActiveTab] = useState("trending")
  const [toast, setToast] = useState("")

  const trendingQuery = useQuery({
    queryKey: [...queryKeys.feed.all, "trending"],
    queryFn: async () => {
      const r = await feedService.getFeed({ sortBy: "engagement", limit: 20 })
      if (r.kind === "ok") return r.data.items
      throw new Error(r.message)
    },
    enabled: activeTab === "trending",
  })

  const recentQuery = useQuery({
    queryKey: [...queryKeys.feed.all, "recent"],
    queryFn: async () => {
      const r = await feedService.getFeed({ sortBy: "date", limit: 20 })
      if (r.kind === "ok") return r.data.items
      throw new Error(r.message)
    },
    enabled: activeTab === "recent",
  })

  const publishersQuery = useQuery({
    queryKey: queryKeys.publishers.all,
    queryFn: async () => {
      const r = await publisherService.getAll()
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: activeTab === "publishers",
  })

  const subsQuery = useQuery({
    queryKey: queryKeys.publisherSubscriptions.mine,
    queryFn: async () => {
      const r = await publisherSubscriptionService.getMine()
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: activeTab === "publishers",
  })

  const subscribedIds = new Set((subsQuery.data ?? []).map((s) => s.publisherId))

  const subscribeMutation = useMutation({
    mutationFn: (publisherId: number) => publisherSubscriptionService.toggle(publisherId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.publisherSubscriptions.mine })
      queryClient.invalidateQueries({ queryKey: queryKeys.feed.all })
    },
    onError: () => setToast("Erreur de connexion"),
  })

  const articles = activeTab === "trending" ? trendingQuery.data : recentQuery.data
  const isLoading =
    activeTab === "publishers"
      ? publishersQuery.isLoading
      : activeTab === "trending"
        ? trendingQuery.isLoading
        : recentQuery.isLoading

  return (
    <div className="space-y-4">
      <h1 className="text-2xl font-bold tracking-tight text-color">Découvrir</h1>
      <TabSelector tabs={DISCOVER_TABS} active={activeTab} onChange={setActiveTab} />

      {isLoading ? (
        <div className="space-y-3">
          <ArticleCardSkeleton />
          <ArticleCardSkeleton />
        </div>
      ) : activeTab === "publishers" ? (
        publishersQuery.data && publishersQuery.data.length > 0 ? (
          <div>
            {publishersQuery.data.map((p) => (
              <PublisherCard
                key={p.id}
                publisher={p}
                isSubscribed={subscribedIds.has(p.id)}
                onToggleSubscribe={() => subscribeMutation.mutate(p.id)}
              />
            ))}
          </div>
        ) : (
          <EmptyState title="Aucun éditeur disponible" />
        )
      ) : articles && articles.length > 0 ? (
        <div className="space-y-3">
          {articles.map((a) => (
            <ArticleCard
              key={a.id}
              article={a}
              onPress={() => navigate(`/article/${a.id}`)}
            />
          ))}
        </div>
      ) : (
        <EmptyState title="Rien à afficher" subtitle="Reviens plus tard ou abonne-toi à plus d'éditeurs." />
      )}

      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </div>
  )
}
```

- [ ] **Step 15.2: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): Discover screen with 3 tabs + publisher subscriptions"
```

---

## Phase 16 — Search screen

**Files:** Replace `src/routes/(app)/search.tsx`

- [ ] **Step 16.1: Replace `src/routes/(app)/search.tsx`**

```tsx
import { useState } from "react"
import { useQuery } from "@tanstack/react-query"
import { useNavigate } from "react-router-dom"
import { searchService } from "@/services/search/searchService"
import { queryKeys } from "@/hooks/queryKeys"
import { useDebounce } from "@/hooks/useDebounce"
import { ArticleCard, ArticleCardSkeleton, EmptyState, SearchBar } from "@/components"

export default function SearchPage() {
  const navigate = useNavigate()
  const [query, setQuery] = useState("")
  const debouncedQuery = useDebounce(query, 400)

  const { data, isLoading, isFetching } = useQuery({
    queryKey: queryKeys.search.articles(debouncedQuery),
    queryFn: async () => {
      const r = await searchService.searchArticles(debouncedQuery)
      if (r.kind === "ok") return r.data.items
      throw new Error(r.message)
    },
    enabled: debouncedQuery.trim().length > 1,
  })

  return (
    <div className="space-y-4">
      <h1 className="text-2xl font-bold tracking-tight text-color">Recherche</h1>
      <SearchBar value={query} onChange={setQuery} placeholder="Rechercher un article…" />

      {!debouncedQuery.trim() ? (
        <EmptyState title="Tape ta recherche" subtitle="Articles, sujets, auteurs…" />
      ) : isLoading || isFetching ? (
        <div className="space-y-3">
          <ArticleCardSkeleton />
          <ArticleCardSkeleton />
        </div>
      ) : data && data.length > 0 ? (
        <div className="space-y-3">
          {data.map((a) => (
            <ArticleCard key={a.id} article={a} onPress={() => navigate(`/article/${a.id}`)} />
          ))}
        </div>
      ) : (
        <EmptyState title="Aucun résultat" subtitle={`Rien trouvé pour « ${debouncedQuery} »`} />
      )}
    </div>
  )
}
```

- [ ] **Step 16.2: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): Search screen with debounced query"
```

---

## Phase 17 — Notifications screen

**Files:** Replace `src/routes/(app)/notifications.tsx`

- [ ] **Step 17.1: Replace `src/routes/(app)/notifications.tsx`**

```tsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { notificationService } from "@/services/notification/notificationService"
import { queryKeys } from "@/hooks/queryKeys"
import { EmptyState, NotificationItem, Skeleton } from "@/components"
import { Button } from "@/components/ui/button"

export default function NotificationsPage() {
  const queryClient = useQueryClient()

  const listQuery = useQuery({
    queryKey: queryKeys.notifications.all,
    queryFn: async () => {
      const r = await notificationService.getAll()
      if (r.kind === "ok") return r.data.notifications
      throw new Error(r.message)
    },
  })

  const markReadMutation = useMutation({
    mutationFn: (id: number) => notificationService.markRead(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.all })
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.unreadCount })
    },
  })

  const markAllMutation = useMutation({
    mutationFn: () => notificationService.markAllRead(),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.all })
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.unreadCount })
    },
  })

  const deleteMutation = useMutation({
    mutationFn: (id: number) => notificationService.delete(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.all })
      queryClient.invalidateQueries({ queryKey: queryKeys.notifications.unreadCount })
    },
  })

  return (
    <div className="space-y-4">
      <div className="flex items-center justify-between">
        <h1 className="text-2xl font-bold tracking-tight text-color">Notifications</h1>
        <Button variant="ghost" size="sm" onClick={() => markAllMutation.mutate()}>
          Tout marquer lu
        </Button>
      </div>

      {listQuery.isLoading ? (
        <div className="space-y-2">
          {[1, 2, 3].map((i) => (
            <Skeleton key={i} className="h-16 w-full" />
          ))}
        </div>
      ) : listQuery.data && listQuery.data.length > 0 ? (
        <div className="rounded-xl border border-border-muted overflow-hidden">
          {listQuery.data.map((n) => (
            <NotificationItem
              key={n.id}
              notification={n}
              onRead={() => !n.isRead && markReadMutation.mutate(n.id)}
              onDelete={() => deleteMutation.mutate(n.id)}
            />
          ))}
        </div>
      ) : (
        <EmptyState title="Aucune notification" subtitle="Reviens plus tard." />
      )}
    </div>
  )
}
```

- [ ] **Step 17.2: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): Notifications screen (list, mark read, mark all, delete)"
```

---

## Phase 18 — Profile (perso + public)

**Files:** Replace `src/routes/(app)/profile.tsx`, create `src/routes/(app)/user/[id].tsx`

- [ ] **Step 18.1: Update `root.tsx` to add `/user/:id` route**

In `Routes` block, inside the protected `AppLayout`:

```tsx
<Route path="/user/:id" element={<UserProfilePage />} />
```

Add import:

```tsx
import UserProfilePage from "./(app)/user/[id]"
```

- [ ] **Step 18.2: Create `src/routes/(app)/user/[id].tsx`**

```tsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { useParams } from "react-router-dom"
import { userService } from "@/services/user/userService"
import { followService } from "@/services/follow/followService"
import { queryKeys } from "@/hooks/queryKeys"
import { ProfileHeader, Skeleton, Toast } from "@/components"
import { useState } from "react"

export default function UserProfilePage() {
  const { id = "" } = useParams<{ id: string }>()
  const userId = Number(id)
  const queryClient = useQueryClient()
  const [toast, setToast] = useState("")

  const userQuery = useQuery({
    queryKey: queryKeys.user.byId(userId),
    queryFn: async () => {
      const r = await userService.getById(id)
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: !!id,
  })

  const countsQuery = useQuery({
    queryKey: queryKeys.follow.counts(userId),
    queryFn: async () => {
      const r = await followService.getCounts(userId)
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: !!userId,
  })

  const isFollowingQuery = useQuery({
    queryKey: queryKeys.follow.isFollowing(userId),
    queryFn: async () => {
      const r = await followService.isFollowing(userId)
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: !!userId,
  })

  const followMutation = useMutation({
    mutationFn: () => followService.toggle(userId),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.follow.counts(userId) })
      queryClient.invalidateQueries({ queryKey: queryKeys.follow.isFollowing(userId) })
    },
    onError: () => setToast("Erreur de connexion"),
  })

  if (userQuery.isLoading) {
    return <Skeleton className="h-48 w-full" />
  }

  if (!userQuery.data) {
    return <p className="text-color3">Utilisateur introuvable.</p>
  }

  return (
    <div className="space-y-6">
      <ProfileHeader
        name={userQuery.data.name}
        bio={userQuery.data.bio}
        avatar={userQuery.data.avatar}
        followersCount={countsQuery.data?.followers}
        followingCount={countsQuery.data?.following}
        isFollowing={isFollowingQuery.data}
        onToggleFollow={() => followMutation.mutate()}
      />
      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </div>
  )
}
```

- [ ] **Step 18.3: Replace `src/routes/(app)/profile.tsx`**

```tsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { useNavigate } from "react-router-dom"
import { userService } from "@/services/user/userService"
import { followService } from "@/services/follow/followService"
import { avatarService } from "@/services/avatar/avatarService"
import { queryKeys } from "@/hooks/queryKeys"
import { useAuth } from "@/contexts/AuthContext"
import { ProfileHeader, Skeleton, Toast } from "@/components"
import { Button } from "@/components/ui/button"
import { useRef, useState } from "react"

export default function ProfilePage() {
  const { user, logout } = useAuth()
  const navigate = useNavigate()
  const queryClient = useQueryClient()
  const fileInputRef = useRef<HTMLInputElement>(null)
  const [toast, setToast] = useState("")

  const meQuery = useQuery({
    queryKey: queryKeys.user.me,
    queryFn: async () => {
      const r = await userService.getMe()
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
  })

  const countsQuery = useQuery({
    queryKey: queryKeys.follow.counts(Number(user?.id ?? 0)),
    queryFn: async () => {
      const r = await followService.getCounts(Number(user!.id))
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: !!user?.id,
  })

  const avatarMutation = useMutation({
    mutationFn: (file: File) => avatarService.upload(file),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.user.me })
      setToast("Avatar mis à jour")
    },
    onError: () => setToast("Échec de l'upload"),
  })

  const handleFileChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0]
    if (file) avatarMutation.mutate(file)
  }

  if (meQuery.isLoading || !meQuery.data) {
    return <Skeleton className="h-48 w-full" />
  }

  return (
    <div className="space-y-6">
      <ProfileHeader
        name={meQuery.data.name}
        email={meQuery.data.email}
        bio={meQuery.data.bio}
        avatar={meQuery.data.avatar}
        isCurrentUser
        followersCount={countsQuery.data?.followers}
        followingCount={countsQuery.data?.following}
        onEditProfile={() => navigate("/settings/account")}
      />

      <div className="flex flex-col items-center gap-2">
        <input
          ref={fileInputRef}
          type="file"
          accept="image/*"
          className="hidden"
          onChange={handleFileChange}
        />
        <Button variant="outline" onClick={() => fileInputRef.current?.click()}>
          Changer l'avatar
        </Button>
        <Button variant="ghost" onClick={() => void logout()}>
          Déconnexion
        </Button>
      </div>

      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </div>
  )
}
```

- [ ] **Step 18.4: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): profile (self) + user/[id] (public) + avatar upload"
```

---

## Phase 19 — Article detail + Comments + Trust sheet

**Files:** Create `src/routes/(app)/article/[id].tsx`

- [ ] **Step 19.1: Register route in `root.tsx`**

Add to `Routes`:

```tsx
<Route path="/article/:id" element={<ArticleDetailPage />} />
```

Add import:

```tsx
import ArticleDetailPage from "./(app)/article/[id]"
```

- [ ] **Step 19.2: Create `src/routes/(app)/article/[id].tsx`**

```tsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query"
import { useParams } from "react-router-dom"
import { Heart, MessageCircle } from "lucide-react"
import { useState } from "react"
import { feedService } from "@/services/feed/feedService"
import { commentService } from "@/services/comment/commentService"
import { likeService } from "@/services/like/likeService"
import { queryKeys } from "@/hooks/queryKeys"
import { useAuth } from "@/contexts/AuthContext"
import { AutoImage, CommentInput, CommentItem, Skeleton, Toast, TrustBadge } from "@/components"
import { timeAgo } from "@/utils/formatDate"
import { cn } from "@/lib/utils"

export default function ArticleDetailPage() {
  const { id = "" } = useParams<{ id: string }>()
  const itemId = Number(id)
  const queryClient = useQueryClient()
  const { user } = useAuth()
  const [toast, setToast] = useState("")

  const itemQuery = useQuery({
    queryKey: queryKeys.feed.item(itemId),
    queryFn: async () => {
      const r = await feedService.getItem(itemId)
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
    enabled: !!itemId,
  })

  const commentsQuery = useQuery({
    queryKey: queryKeys.comments.byItem(itemId),
    queryFn: async () => {
      const r = await commentService.getByItem(itemId)
      if (r.kind === "ok") return r.data.comments
      throw new Error(r.message)
    },
    enabled: !!itemId,
  })

  const likeMutation = useMutation({
    mutationFn: () => likeService.toggle(itemId),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.feed.item(itemId) }),
    onError: () => setToast("Erreur"),
  })

  const createCommentMutation = useMutation({
    mutationFn: (content: string) => commentService.create(itemId, content),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.comments.byItem(itemId) }),
    onError: () => setToast("Erreur"),
  })

  const deleteCommentMutation = useMutation({
    mutationFn: (commentId: number) => commentService.delete(commentId),
    onSuccess: () => queryClient.invalidateQueries({ queryKey: queryKeys.comments.byItem(itemId) }),
    onError: () => setToast("Erreur"),
  })

  if (itemQuery.isLoading || !itemQuery.data) return <Skeleton className="h-48 w-full" />
  const article = itemQuery.data

  return (
    <article className="mx-auto max-w-2xl space-y-6">
      <header className="space-y-3">
        <div className="flex items-center gap-3">
          <span className="text-sm font-bold text-color">{article.publisher.name}</span>
          <span className="text-xs text-color3">{timeAgo(article.publishedAt ?? article.createdAt)}</span>
          <TrustBadge score={article.trustScore} itemId={article.id} size="normal" />
        </div>
        <h1 className="text-3xl font-bold leading-tight text-color">{article.title}</h1>
        {article.author && <p className="text-sm text-color3">Par {article.author}</p>}
      </header>

      {article.imageUrl && (
        <AutoImage src={article.imageUrl} alt={article.title} className="h-64 w-full rounded-xl" />
      )}

      {article.summary && (
        <p className="rounded-xl bg-surface p-4 text-base text-color2 leading-relaxed">
          {article.summary.replace(/<[^>]*>/g, "")}
        </p>
      )}

      {article.content && (
        <div className="text-base text-color leading-relaxed whitespace-pre-wrap">
          {article.content}
        </div>
      )}

      {article.url && (
        <a
          href={article.url}
          target="_blank"
          rel="noreferrer noopener"
          className="inline-block rounded-md border border-border bg-surface px-4 py-2 text-sm text-color hover:bg-hover"
        >
          Lire la source originale →
        </a>
      )}

      <div className="flex items-center gap-6 border-y border-border-muted py-3">
        <button onClick={() => likeMutation.mutate()} className="flex items-center gap-2">
          <Heart size={18} className={cn(article.isLiked && "fill-danger text-danger")} />
          <span className="text-sm font-medium text-color2">{article.likesCount}</span>
        </button>
        <span className="flex items-center gap-2 text-color2">
          <MessageCircle size={18} />
          <span className="text-sm font-medium">{article.commentsCount}</span>
        </span>
      </div>

      <section className="space-y-4">
        <h2 className="text-lg font-bold text-color">Commentaires</h2>
        <CommentInput onSubmit={async (c) => createCommentMutation.mutate(c)} />
        {commentsQuery.isLoading ? (
          <Skeleton className="h-20 w-full" />
        ) : commentsQuery.data && commentsQuery.data.length > 0 ? (
          <div>
            {commentsQuery.data.map((c) => (
              <CommentItem
                key={c.id}
                comment={c}
                isOwner={user?.id === String(c.userId)}
                onDelete={() => deleteCommentMutation.mutate(c.id)}
              />
            ))}
          </div>
        ) : (
          <p className="text-sm text-color3">Aucun commentaire pour l'instant.</p>
        )}
      </section>

      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </article>
  )
}
```

- [ ] **Step 19.3: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): article detail + comments + trust sheet integration"
```

---

## Phase 20 — Settings (6 sub-routes)

**Files:**
- Create: `src/routes/(app)/settings/layout.tsx`, `src/routes/(app)/settings/index.tsx`, `account.tsx`, `privacy.tsx`, `appearance.tsx`, `notifications.tsx`, `security.tsx`, `data-privacy.tsx`

- [ ] **Step 20.1: Register settings routes in `root.tsx`**

Add inside protected `AppLayout` (replacing previous settings reference):

```tsx
<Route path="/settings" element={<SettingsLayout />}>
  <Route index element={<Navigate to="/settings/account" replace />} />
  <Route path="account" element={<AccountSettingsPage />} />
  <Route path="privacy" element={<PrivacySettingsPage />} />
  <Route path="appearance" element={<AppearanceSettingsPage />} />
  <Route path="notifications" element={<NotificationsSettingsPage />} />
  <Route path="security" element={<SecuritySettingsPage />} />
  <Route path="data-privacy" element={<DataPrivacySettingsPage />} />
</Route>
```

Add imports:

```tsx
import SettingsLayout from "./(app)/settings/layout"
import AccountSettingsPage from "./(app)/settings/account"
import PrivacySettingsPage from "./(app)/settings/privacy"
import AppearanceSettingsPage from "./(app)/settings/appearance"
import NotificationsSettingsPage from "./(app)/settings/notifications"
import SecuritySettingsPage from "./(app)/settings/security"
import DataPrivacySettingsPage from "./(app)/settings/data-privacy"
```

- [ ] **Step 20.2: Create `src/routes/(app)/settings/layout.tsx`**

```tsx
import { NavLink, Outlet } from "react-router-dom"
import { cn } from "@/lib/utils"

const TABS = [
  { to: "/settings/account", label: "Compte" },
  { to: "/settings/privacy", label: "Confidentialité" },
  { to: "/settings/appearance", label: "Apparence" },
  { to: "/settings/notifications", label: "Notifications" },
  { to: "/settings/security", label: "Sécurité" },
  { to: "/settings/data-privacy", label: "Données" },
]

export default function SettingsLayout() {
  return (
    <div className="space-y-4">
      <h1 className="text-2xl font-bold tracking-tight text-color">Paramètres</h1>
      <nav className="flex gap-1 overflow-x-auto rounded-lg bg-surface p-1">
        {TABS.map((t) => (
          <NavLink
            key={t.to}
            to={t.to}
            className={({ isActive }) =>
              cn(
                "whitespace-nowrap rounded-md px-3 py-1.5 text-sm font-medium transition-colors",
                isActive ? "bg-bg text-color shadow" : "text-color2 hover:bg-hover",
              )
            }
          >
            {t.label}
          </NavLink>
        ))}
      </nav>
      <div className="rounded-xl border border-border-muted">
        <Outlet />
      </div>
    </div>
  )
}
```

- [ ] **Step 20.3: Create `src/routes/(app)/settings/account.tsx`**

```tsx
import { useState, useEffect } from "react"
import { useQuery } from "@tanstack/react-query"
import { userService } from "@/services/user/userService"
import { authService } from "@/services/auth/authService"
import { queryKeys } from "@/hooks/queryKeys"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Toast } from "@/components"

export default function AccountSettingsPage() {
  const meQuery = useQuery({
    queryKey: queryKeys.user.me,
    queryFn: async () => {
      const r = await userService.getMe()
      if (r.kind === "ok") return r.data
      throw new Error(r.message)
    },
  })

  const [name, setName] = useState("")
  const [email, setEmail] = useState("")
  const [bio, setBio] = useState("")
  const [oldPassword, setOldPassword] = useState("")
  const [newPassword, setNewPassword] = useState("")
  const [toast, setToast] = useState<{ kind: "ok" | "error"; text: string } | null>(null)

  useEffect(() => {
    if (meQuery.data) {
      setName(meQuery.data.name)
      setEmail(meQuery.data.email)
      setBio(meQuery.data.bio ?? "")
    }
  }, [meQuery.data])

  const handleProfileSave = async () => {
    const res = await userService.updateMe({ name, email, bio })
    if (res.kind === "ok") setToast({ kind: "ok", text: "Profil mis à jour" })
    else setToast({ kind: "error", text: res.message })
  }

  const handlePasswordChange = async () => {
    // Note: backend endpoint is `/api/user/me/password` (PUT) — body { oldPassword, newPassword }
    const { api } = await import("@/services/api")
    const r = await api.apisauce.put("/api/user/me/password", { oldPassword, newPassword })
    if (r.ok) {
      setToast({ kind: "ok", text: "Mot de passe changé" })
      setOldPassword("")
      setNewPassword("")
    } else {
      setToast({ kind: "error", text: "Erreur lors du changement" })
    }
  }

  return (
    <div className="space-y-6 p-4">
      <section className="space-y-3">
        <h2 className="text-lg font-bold text-color">Profil</h2>
        <div className="space-y-2">
          <Label htmlFor="name">Nom</Label>
          <Input id="name" value={name} onChange={(e) => setName(e.target.value)} />
        </div>
        <div className="space-y-2">
          <Label htmlFor="email">Email</Label>
          <Input id="email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} />
        </div>
        <div className="space-y-2">
          <Label htmlFor="bio">Bio</Label>
          <Input id="bio" value={bio} onChange={(e) => setBio(e.target.value)} />
        </div>
        <Button onClick={handleProfileSave}>Enregistrer</Button>
      </section>

      <section className="space-y-3">
        <h2 className="text-lg font-bold text-color">Mot de passe</h2>
        <div className="space-y-2">
          <Label htmlFor="oldPassword">Ancien mot de passe</Label>
          <Input
            id="oldPassword"
            type="password"
            value={oldPassword}
            onChange={(e) => setOldPassword(e.target.value)}
          />
        </div>
        <div className="space-y-2">
          <Label htmlFor="newPassword">Nouveau mot de passe</Label>
          <Input
            id="newPassword"
            type="password"
            value={newPassword}
            onChange={(e) => setNewPassword(e.target.value)}
          />
        </div>
        <Button onClick={handlePasswordChange}>Changer le mot de passe</Button>
      </section>

      <Toast
        message={toast?.text ?? ""}
        type={toast?.kind === "ok" ? "success" : "error"}
        visible={!!toast}
        onDismiss={() => setToast(null)}
      />
    </div>
  )
}
```

- [ ] **Step 20.4: Create `src/routes/(app)/settings/privacy.tsx`**

```tsx
import usePreferencesStore from "@/stores/preferencesStore"
import { SettingsToggle } from "@/components"

export default function PrivacySettingsPage() {
  const prefs = usePreferencesStore((s) => s.preferences)
  const update = usePreferencesStore((s) => s.updatePreference)

  if (!prefs) return <p className="p-4 text-color3">Chargement…</p>

  return (
    <div className="p-2">
      <SettingsToggle
        label="Afficher mon email"
        checked={prefs.showEmail}
        onCheckedChange={(v) => void update("showEmail", v)}
      />
      <SettingsToggle
        label="Autoriser les messages"
        checked={prefs.allowMessages}
        onCheckedChange={(v) => void update("allowMessages", v)}
      />
      <SettingsToggle
        label="Afficher mon activité"
        checked={prefs.showActivity}
        onCheckedChange={(v) => void update("showActivity", v)}
      />
    </div>
  )
}
```

- [ ] **Step 20.5: Create `src/routes/(app)/settings/appearance.tsx`**

```tsx
import { useTheme } from "@/hooks/useTheme"
import { ThemeCard } from "@/components"

export default function AppearanceSettingsPage() {
  const { mode, setTheme } = useTheme()

  return (
    <div className="p-4">
      <p className="mb-4 text-sm text-color3">Choisissez votre thème préféré.</p>
      <div className="flex gap-4">
        <ThemeCard label="Clair" value="light" active={mode === "light"} onClick={() => setTheme("light")} />
        <ThemeCard label="Sombre" value="dark" active={mode === "dark"} onClick={() => setTheme("dark")} />
        <ThemeCard label="Système" value="system" active={mode === "system"} onClick={() => setTheme("system")} />
      </div>
    </div>
  )
}
```

- [ ] **Step 20.6: Create `src/routes/(app)/settings/notifications.tsx`**

```tsx
import usePreferencesStore from "@/stores/preferencesStore"
import { SettingsToggle } from "@/components"

export default function NotificationsSettingsPage() {
  const prefs = usePreferencesStore((s) => s.preferences)
  const update = usePreferencesStore((s) => s.updatePreference)

  if (!prefs) return <p className="p-4 text-color3">Chargement…</p>

  return (
    <div className="p-2">
      <SettingsToggle
        label="Notifications email"
        checked={prefs.emailNotifications}
        onCheckedChange={(v) => void update("emailNotifications", v)}
      />
      <SettingsToggle
        label="Nouvelles abonnés"
        checked={prefs.newFollowerNotifications}
        onCheckedChange={(v) => void update("newFollowerNotifications", v)}
      />
      <SettingsToggle
        label="Nouveaux commentaires"
        checked={prefs.commentNotifications}
        onCheckedChange={(v) => void update("commentNotifications", v)}
      />
      <SettingsToggle
        label="Likes"
        checked={prefs.likeNotifications}
        onCheckedChange={(v) => void update("likeNotifications", v)}
      />
      <SettingsToggle
        label="Nouveaux articles"
        checked={prefs.articleNotifications}
        onCheckedChange={(v) => void update("articleNotifications", v)}
      />
      <SettingsToggle
        label="Digest quotidien"
        checked={prefs.emailDigest}
        onCheckedChange={(v) => void update("emailDigest", v)}
      />
    </div>
  )
}
```

- [ ] **Step 20.7: Create `src/routes/(app)/settings/security.tsx`**

```tsx
import usePreferencesStore from "@/stores/preferencesStore"
import { SettingsToggle } from "@/components"

export default function SecuritySettingsPage() {
  const prefs = usePreferencesStore((s) => s.preferences)
  const update = usePreferencesStore((s) => s.updatePreference)

  if (!prefs) return <p className="p-4 text-color3">Chargement…</p>

  return (
    <div className="p-2">
      <SettingsToggle
        label="Authentification à deux facteurs"
        description="Activer le 2FA (non implémenté pour l'instant)"
        checked={prefs.twoFactorAuth}
        onCheckedChange={(v) => void update("twoFactorAuth", v)}
        disabled
      />
      <SettingsToggle
        label="Alertes de connexion"
        description="Email à chaque nouvelle connexion"
        checked={prefs.loginAlerts}
        onCheckedChange={(v) => void update("loginAlerts", v)}
      />
    </div>
  )
}
```

- [ ] **Step 20.8: Create `src/routes/(app)/settings/data-privacy.tsx`**

```tsx
import { useState } from "react"
import { useAuth } from "@/contexts/AuthContext"
import { userService } from "@/services/user/userService"
import { Button } from "@/components/ui/button"
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogFooter } from "@/components/ui/dialog"
import { Toast } from "@/components"
import { useNavigate } from "react-router-dom"

export default function DataPrivacySettingsPage() {
  const { user, logout } = useAuth()
  const navigate = useNavigate()
  const [confirmOpen, setConfirmOpen] = useState(false)
  const [toast, setToast] = useState("")

  const handleDelete = async () => {
    if (!user) return
    const res = await userService.delete(user.id)
    if (res.kind === "ok") {
      await logout()
      navigate("/login", { replace: true })
    } else {
      setToast(res.message)
    }
  }

  return (
    <div className="space-y-4 p-4">
      <section>
        <h2 className="mb-2 text-lg font-bold text-color">Supprimer le compte</h2>
        <p className="mb-3 text-sm text-color3">
          Cette action est définitive. Tous tes commentaires, likes et bookmarks seront supprimés.
        </p>
        <Button variant="destructive" onClick={() => setConfirmOpen(true)}>
          Supprimer mon compte
        </Button>
      </section>

      <Dialog open={confirmOpen} onOpenChange={setConfirmOpen}>
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Supprimer définitivement votre compte ?</DialogTitle>
          </DialogHeader>
          <p className="text-sm text-color3">Cette action est irréversible.</p>
          <DialogFooter>
            <Button variant="outline" onClick={() => setConfirmOpen(false)}>
              Annuler
            </Button>
            <Button variant="destructive" onClick={handleDelete}>
              Confirmer la suppression
            </Button>
          </DialogFooter>
        </DialogContent>
      </Dialog>

      <Toast message={toast} visible={!!toast} onDismiss={() => setToast("")} />
    </div>
  )
}
```

- [ ] **Step 20.9: Verify all settings sub-routes navigate**

```bash
pnpm dev
```

Manual: navigate `/settings/account`, `/settings/privacy`, etc. — all render without console errors.

- [ ] **Step 20.10: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): settings 6 sub-routes (account, privacy, appearance, notifs, security, data)"
```

---

## Phase 21 — Tests Vitest

**Files:** `src/**/*.test.{ts,tsx}`

- [ ] **Step 21.1: Add MSW-free mock for apisauce**

Create `src/test/mockApi.ts`:

```ts
import { vi } from "vitest"

export function mockApisauce(impl: {
  get?: ReturnType<typeof vi.fn>
  post?: ReturnType<typeof vi.fn>
  put?: ReturnType<typeof vi.fn>
  patch?: ReturnType<typeof vi.fn>
  delete?: ReturnType<typeof vi.fn>
}) {
  vi.mock("@/services/api", () => ({
    api: {
      apisauce: {
        get: impl.get ?? vi.fn(),
        post: impl.post ?? vi.fn(),
        put: impl.put ?? vi.fn(),
        patch: impl.patch ?? vi.fn(),
        delete: impl.delete ?? vi.fn(),
        setHeader: vi.fn(),
        deleteHeader: vi.fn(),
      },
      setAuthToken: vi.fn(),
      clearAuthToken: vi.fn(),
      setAuthFailureHandler: vi.fn(),
    },
  }))
}
```

- [ ] **Step 21.2: Write `src/services/auth/authService.test.ts`**

```ts
import { describe, it, expect, vi, beforeEach } from "vitest"

const postMock = vi.fn()

vi.mock("@/services/api", () => ({
  api: {
    apisauce: {
      get: vi.fn(),
      post: postMock,
      put: vi.fn(),
      patch: vi.fn(),
      delete: vi.fn(),
    },
  },
}))

import { authService } from "./authService"

describe("authService", () => {
  beforeEach(() => {
    postMock.mockReset()
  })

  it("login OK normalizes user", async () => {
    postMock.mockResolvedValueOnce({
      ok: true,
      data: {
        success: true,
        data: {
          token: "t",
          refreshToken: "rt",
          user: { id: 1, email: "a@b.c", name: "A", avatarUrl: "http://x/a.png" },
        },
      },
    })
    const result = await authService.login({ email: "a@b.c", password: "p" })
    expect(result.kind).toBe("ok")
    if (result.kind === "ok") {
      expect(result.token).toBe("t")
      expect(result.user.id).toBe("1")
      expect(result.user.avatar).toBe("http://x/a.png")
    }
  })

  it("login 401 returns error with message", async () => {
    postMock.mockResolvedValueOnce({
      ok: false,
      status: 401,
      problem: "CLIENT_ERROR",
      data: { success: false, error: { message: "Bad creds" } },
    })
    const result = await authService.login({ email: "a@b.c", password: "wrong" })
    expect(result.kind).toBe("error")
    if (result.kind === "error") expect(result.message).toBe("Bad creds")
  })

  it("register OK", async () => {
    postMock.mockResolvedValueOnce({
      ok: true,
      data: {
        success: true,
        data: { token: "t", user: { id: 2, email: "b@c.d", name: "B" } },
      },
    })
    const result = await authService.register({ name: "B", email: "b@c.d", password: "pwd12345" })
    expect(result.kind).toBe("ok")
  })

  it("googleAuth OK", async () => {
    postMock.mockResolvedValueOnce({
      ok: true,
      data: {
        success: true,
        data: { token: "g", user: { id: 3, email: "g@x.y", name: "G" } },
      },
    })
    const result = await authService.googleAuth("idtoken")
    expect(result.kind).toBe("ok")
  })
})
```

- [ ] **Step 21.3: Write `src/services/feed/feedService.test.ts`**

```ts
import { describe, it, expect, vi, beforeEach } from "vitest"

const getMock = vi.fn()

vi.mock("@/services/api", () => ({
  api: {
    apisauce: { get: getMock, post: vi.fn(), put: vi.fn(), patch: vi.fn(), delete: vi.fn() },
  },
}))

import { feedService } from "./feedService"

describe("feedService", () => {
  beforeEach(() => getMock.mockReset())

  it("getFeed OK returns items", async () => {
    getMock.mockResolvedValueOnce({
      ok: true,
      data: { success: true, data: { items: [], total: 0, page: 1, totalPages: 0 } },
    })
    const r = await feedService.getFeed({ page: 1 })
    expect(r.kind).toBe("ok")
    if (r.kind === "ok") expect(r.data.page).toBe(1)
  })

  it("getFeed forwards query params", async () => {
    getMock.mockResolvedValueOnce({
      ok: true,
      data: { success: true, data: { items: [], total: 0, page: 2, totalPages: 5 } },
    })
    await feedService.getFeed({ page: 2, limit: 10, sortBy: "trust" })
    expect(getMock).toHaveBeenCalledWith("/api/feed", { page: 2, limit: 10, sortBy: "trust" })
  })

  it("getFeed maps network error", async () => {
    getMock.mockResolvedValueOnce({ ok: false, problem: "NETWORK_ERROR", data: null })
    const r = await feedService.getFeed()
    expect(r.kind).toBe("error")
    if (r.kind === "error") expect(r.message).toBe("cannot-connect")
  })
})
```

- [ ] **Step 21.4: Write `src/services/like/likeService.test.ts`**

```ts
import { describe, it, expect, vi, beforeEach } from "vitest"

const postMock = vi.fn()

vi.mock("@/services/api", () => ({
  api: { apisauce: { get: vi.fn(), post: postMock, put: vi.fn(), patch: vi.fn(), delete: vi.fn() } },
}))

import { likeService } from "./likeService"

describe("likeService", () => {
  beforeEach(() => postMock.mockReset())

  it("toggle returns liked + count", async () => {
    postMock.mockResolvedValueOnce({
      ok: true,
      data: { success: true, data: { liked: true, count: 5 } },
    })
    const r = await likeService.toggle(42)
    expect(r.kind).toBe("ok")
    if (r.kind === "ok") {
      expect(r.data.liked).toBe(true)
      expect(r.data.count).toBe(5)
    }
  })
})
```

- [ ] **Step 21.5: Write `src/services/publisherSubscription/publisherSubscriptionService.test.ts`**

```ts
import { describe, it, expect, vi, beforeEach } from "vitest"

const postMock = vi.fn()
const getMock = vi.fn()

vi.mock("@/services/api", () => ({
  api: { apisauce: { get: getMock, post: postMock, put: vi.fn(), patch: vi.fn(), delete: vi.fn() } },
}))

import { publisherSubscriptionService } from "./publisherSubscriptionService"

describe("publisherSubscriptionService", () => {
  beforeEach(() => {
    postMock.mockReset()
    getMock.mockReset()
  })

  it("toggle OK", async () => {
    postMock.mockResolvedValueOnce({
      ok: true,
      data: { success: true, data: { subscribed: true } },
    })
    const r = await publisherSubscriptionService.toggle(3)
    expect(r.kind).toBe("ok")
    if (r.kind === "ok") expect(r.data.subscribed).toBe(true)
  })

  it("toggle 401 → error", async () => {
    postMock.mockResolvedValueOnce({ ok: false, status: 401, problem: "CLIENT_ERROR" })
    const r = await publisherSubscriptionService.toggle(3)
    expect(r.kind).toBe("error")
    if (r.kind === "error") expect(r.message).toBe("unauthorized")
  })

  it("getMine returns flat list", async () => {
    getMock.mockResolvedValueOnce({
      ok: true,
      data: {
        success: true,
        data: { subscriptions: [{ id: 1, userId: 10, publisherId: 3, createdAt: "x", publisher: { id: 3, name: "X", slug: "x" } }] },
      },
    })
    const r = await publisherSubscriptionService.getMine()
    expect(r.kind).toBe("ok")
    if (r.kind === "ok") expect(r.data).toHaveLength(1)
  })
})
```

- [ ] **Step 21.6: Write `src/stores/preferencesStore.test.ts`**

```ts
import { describe, it, expect, vi, beforeEach } from "vitest"

const getMock = vi.fn()
const patchMock = vi.fn()

vi.mock("@/services/preferences/preferencesService", () => ({
  preferencesService: {
    get: () => getMock(),
    update: (data: unknown) => patchMock(data),
  },
}))

import usePreferencesStore from "./preferencesStore"

describe("preferencesStore", () => {
  beforeEach(() => {
    getMock.mockReset()
    patchMock.mockReset()
    usePreferencesStore.setState({ preferences: null, loading: false, error: null })
  })

  it("fetchPreferences populates store on success", async () => {
    getMock.mockResolvedValueOnce({
      kind: "ok",
      data: { id: 1, userId: 1, theme: "dark", language: "fr" },
    })
    await usePreferencesStore.getState().fetchPreferences()
    expect(usePreferencesStore.getState().preferences?.theme).toBe("dark")
  })

  it("updatePreference optimistic + server confirm", async () => {
    usePreferencesStore.setState({
      preferences: { id: 1, userId: 1, theme: "light", language: "fr" } as never,
      loading: false,
      error: null,
    })
    patchMock.mockResolvedValueOnce({
      kind: "ok",
      data: { id: 1, userId: 1, theme: "dark", language: "fr" },
    })
    await usePreferencesStore.getState().updatePreference("theme", "dark")
    expect(usePreferencesStore.getState().preferences?.theme).toBe("dark")
  })

  it("updatePreference reverts on error", async () => {
    usePreferencesStore.setState({
      preferences: { id: 1, userId: 1, theme: "light", language: "fr" } as never,
      loading: false,
      error: null,
    })
    patchMock.mockResolvedValueOnce({ kind: "error", message: "failed" })
    await usePreferencesStore.getState().updatePreference("theme", "dark")
    expect(usePreferencesStore.getState().preferences?.theme).toBe("light")
    expect(usePreferencesStore.getState().error).toBe("failed")
  })
})
```

- [ ] **Step 21.7: Write `src/utils/formatDate.test.ts`**

```ts
import { describe, it, expect } from "vitest"
import { timeAgo } from "./formatDate"

describe("timeAgo", () => {
  it("returns '' on null", () => {
    expect(timeAgo(null)).toBe("")
  })

  it("returns 'à l'instant' for recent dates", () => {
    const now = new Date().toISOString()
    expect(timeAgo(now)).toBe("à l'instant")
  })

  it("returns minutes for < 1h old", () => {
    const date = new Date(Date.now() - 5 * 60 * 1000).toISOString()
    expect(timeAgo(date)).toBe("il y a 5 min")
  })

  it("returns hours for < 24h old", () => {
    const date = new Date(Date.now() - 3 * 60 * 60 * 1000).toISOString()
    expect(timeAgo(date)).toBe("il y a 3 h")
  })

  it("returns days for < 1 week old", () => {
    const date = new Date(Date.now() - 3 * 24 * 60 * 60 * 1000).toISOString()
    expect(timeAgo(date)).toBe("il y a 3 j")
  })
})
```

- [ ] **Step 21.8: Write `src/components/ArticleCard.test.tsx`**

```tsx
import { describe, it, expect, vi } from "vitest"
import { render, screen, fireEvent } from "@testing-library/react"
import { ArticleCard } from "./ArticleCard"
import type { FeedItem } from "@/services/feed/feedTypes"

const article: FeedItem = {
  id: 1,
  title: "Hello world",
  content: null,
  url: null,
  author: null,
  publishedAt: new Date(Date.now() - 60 * 60 * 1000).toISOString(),
  imageUrl: null,
  relevanceScore: null,
  trustScore: 80,
  publisher: { id: 1, name: "TechCrunch" },
  summary: "A short summary",
  likesCount: 10,
  commentsCount: 3,
  isLiked: false,
  createdAt: new Date().toISOString(),
}

describe("ArticleCard", () => {
  it("renders title and publisher", () => {
    render(<ArticleCard article={article} />)
    expect(screen.getByText("Hello world")).toBeInTheDocument()
    expect(screen.getByText("TechCrunch")).toBeInTheDocument()
  })

  it("calls onPress when card clicked", () => {
    const onPress = vi.fn()
    render(<ArticleCard article={article} onPress={onPress} />)
    fireEvent.click(screen.getByText("Hello world"))
    expect(onPress).toHaveBeenCalled()
  })

  it("calls onLike without triggering onPress (stopPropagation)", () => {
    const onPress = vi.fn()
    const onLike = vi.fn()
    render(<ArticleCard article={article} onPress={onPress} onLike={onLike} />)
    fireEvent.click(screen.getByText("10"))
    expect(onLike).toHaveBeenCalled()
    expect(onPress).not.toHaveBeenCalled()
  })
})
```

- [ ] **Step 21.9: Run all tests**

```bash
pnpm test
```

Expected: all green (15+ tests passing).

- [ ] **Step 21.10: Commit**

```bash
git add -A
git commit -m "test(rebuild-v2): vitest suite covering auth, feed, like, subscription, prefs, formatDate, ArticleCard"
```

---

## Phase 22 — Docker + CI/CD update

**Files:** Modify `Dockerfile`, `docker-compose.prod.yml`, `.github/workflows/*.yml`

- [ ] **Step 22.1: Replace `Dockerfile` with multi-stage pnpm + Vite + nginx**

```dockerfile
# syntax=docker/dockerfile:1

FROM node:20-alpine AS builder
WORKDIR /app

RUN corepack enable && corepack prepare pnpm@10.11.0 --activate

COPY package.json pnpm-lock.yaml .npmrc ./
RUN pnpm install --frozen-lockfile

COPY . .
ARG VITE_API_URL
ARG VITE_GOOGLE_WEB_CLIENT_ID
ENV VITE_API_URL=${VITE_API_URL}
ENV VITE_GOOGLE_WEB_CLIENT_ID=${VITE_GOOGLE_WEB_CLIENT_ID}

RUN pnpm build

FROM nginx:alpine AS runner
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
```

- [ ] **Step 22.2: Verify `docker-compose.prod.yml` still uses `${IMAGE_NAME}` (no changes needed; CI passes the image)**

Inspect:

```bash
cat docker-compose.prod.yml
```

Expected: services use `image: ${IMAGE_NAME}` (already correct from commit `6e88537`). If not, edit accordingly.

- [ ] **Step 22.3: Update CI workflow**

Open `.github/workflows/deploy.yml` (or whatever the existing workflow file is called) and replace the build steps so they use pnpm.

The build job step block should look like:

```yaml
      - name: Setup Node 20
        uses: actions/setup-node@v4
        with:
          node-version: "20"

      - name: Enable pnpm
        uses: pnpm/action-setup@v4
        with:
          version: 10.11.0

      - name: Install deps
        run: pnpm install --frozen-lockfile

      - name: Lint
        run: pnpm lint

      - name: Typecheck
        run: pnpm typecheck

      - name: Test
        run: pnpm test

      - name: Build Docker image
        run: |
          docker build \
            --build-arg VITE_API_URL="${{ vars.REACT_APP_API_URL }}" \
            --build-arg VITE_GOOGLE_WEB_CLIENT_ID="${{ secrets.VITE_GOOGLE_WEB_CLIENT_ID }}" \
            -t "${{ vars.IMAGE_NAME }}" .

      - name: Push to GHCR
        run: |
          echo "${{ secrets.CI_REGISTRY_PASSWORD }}" | docker login ghcr.io -u "${{ vars.CI_REGISTRY_USER }}" --password-stdin
          docker push "${{ vars.IMAGE_NAME }}"

      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ vars.SERVER_HOST }}
          username: ${{ vars.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_PRIVATE_KEY }}
          script: |
            cd /opt/syntheza-frontend
            docker compose pull
            docker compose up -d
```

(Adapt to the actual existing workflow file structure — preserve the secrets/vars referenced.)

- [ ] **Step 22.4: Add GitHub var `VITE_API_URL` and secret `VITE_GOOGLE_WEB_CLIENT_ID` (only if you intend to enable Google login in prod)**

Manual step on GitHub: Settings → Secrets and variables → Actions → add the var/secret. Note in the plan: this is one-time setup.

- [ ] **Step 22.5: Build Docker image locally to verify**

```bash
docker build \
  --build-arg VITE_API_URL="https://api.syntheza.ovh/" \
  -t syntheza-frontend-web:v2-local .
```

Expected: build succeeds, image created.

- [ ] **Step 22.6: Run the container locally and smoke-test**

```bash
docker run --rm -p 8080:80 syntheza-frontend-web:v2-local
```

Open http://localhost:8080 in a browser → should load v2 home page (redirects to `/login`).

Stop container with Ctrl-C.

- [ ] **Step 22.7: Commit**

```bash
git add -A
git commit -m "ci(rebuild-v2): Dockerfile pnpm+Vite multi-stage, CI workflow uses pnpm, lint/typecheck/test gates"
```

---

## Phase 23 — Smoke + final merge

**Files:** none new; verification only.

- [ ] **Step 23.1: Full local smoke test in Docker container**

```bash
docker build \
  --build-arg VITE_API_URL="https://api.syntheza.ovh/" \
  -t syntheza-frontend-web:v2-smoke .
docker run --rm -p 8080:80 syntheza-frontend-web:v2-smoke
```

Open http://localhost:8080. Run **all 7 smoke flows** below and ensure each passes:

1. **Login flow**: visit `/`, redirect to `/login` → login with `dev@syntheza.app` / `password123` → redirect `/home`.
2. **Feed flow**: `/home` displays articles. Scroll → infinite load triggers. Click heart → like count increments.
3. **Discover toggle subscription**: `/discover` → Éditeurs tab → click "S'abonner" on one publisher → button becomes "Abonné". Refresh page → subscription persists.
4. **Article detail**: click an article card → `/article/:id` → title + content + comments visible. Post a comment.
5. **Settings theme change**: `/settings/appearance` → click "Sombre" → background turns dark immediately. Reload → setting persists.
6. **Search**: `/search` → type "react" → debounced query → results render.
7. **Logout**: sidebar logout button → redirects `/login` → localStorage cleared (confirm via DevTools).

Stop container.

- [ ] **Step 23.2: Run final quality gates**

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

Expected:
- lint: 0 errors
- typecheck: 0 errors
- test: 15+ tests passing
- build: succeeds, bundle reported in console < 800 KB gzip (`dist/assets/*.js` total)

- [ ] **Step 23.3: Push branch and ask user to validate**

```bash
git status
git push -u origin feat/rebuild-v2
```

Send the branch URL to user. User pulls, runs `docker build && docker run`, walks through the 7 flows, gives green light.

- [ ] **Step 23.4: Final merge (user-authorized only)**

```bash
git checkout main
git pull --ff-only
git merge --ff-only feat/rebuild-v2
git push origin main
```

CI/CD GitHub Actions kicks in → deploy to prod.

- [ ] **Step 23.5: Verify prod**

Visit https://syntheza.ovh → check that v2 is live. Log in. Walk through 1 quick flow (login → home → like).

- [ ] **Step 23.6: Commit clean-up (optional)**

If any post-merge fixes are needed, branch off main with `fix/v2-postmerge-*` and merge ff after fix.

---

## Self-Review checklist

After the plan is fully executed:

1. **Spec coverage** — every periphery item below has a task:
   - [x] Backend `/api/*` prefix used everywhere — Step 6.6 (api/index.ts base), all services in Phase 7
   - [x] No `fetchWith404Fallback`, no `/user/*` without `/api/` — confirmed by absence of `apiUtils.js` and `withLegacyApiPrefix` (wiped in Phase 0)
   - [x] No `SOURCES.*` or `SUBSCRIPTIONS.*` — only `publishers/*` and `publisher-subscriptions/*` used
   - [x] Settings 6 sub-routes — Phase 20
   - [x] Article detail + Comments + Trust sheet — Phase 19
   - [x] Profile public via `/user/:id` — Phase 18.2
   - [x] Discover 3 tabs + PublisherCard + toggle subscribe — Phase 15
   - [x] Dark/Light/System mode — Phase 13.1 + 20.5
   - [x] i18n FR — Phase 9
   - [x] Tests Vitest — Phase 21 (7 test files, ~20 tests)
   - [x] Docker multi-stage pnpm + nginx — Phase 22.1
   - [x] CI pnpm + lint + typecheck + test gates — Phase 22.3
   - [x] Bearer + localStorage + refresh queue — Phase 6.6 + 8.6
   - [x] Google OAuth via @react-oauth/google → `/api/user/google` — Phase 12.2
   - [x] Bookmarks toggle — Phase 14.2 + service Phase 7.8
   - [x] Follow toggle — Phase 18.2
   - [x] Notifications mark read / mark all / delete — Phase 17.1
   - [x] Avatar upload — Phase 18.3
   - [x] Comments CRUD — Phase 19.2

2. **Placeholder scan** — no "TBD", "fill in", "etc." in any step. Every code block is complete.

3. **Type consistency** — `feedService` signature matches usage everywhere; `queryKeys` names match between definition (Phase 8.2) and consumption (Phases 14-19); `AuthUser.id` is `string` everywhere (since `normalizeUser` does `String(raw.id)`).

4. **Known limitations carried forward** (not bugs):
   - `GET /api/publisher-subscriptions/:publisherId/count` doesn't exist backend-side — we don't call it (mémoire feedback_port_iso.md).
   - `GET /api/follow/:userId/is-following` — endpoint may not be implemented; service call exists but UI degrades gracefully if it returns 404 (TanStack Query returns `undefined`, UI shows "Suivre").
   - Login may fail in local dev if backend CORS doesn't whitelist `http://localhost:3000` — backend already includes it in default `corsOrigins`.

---

## Execution Handoff

**Plan complete and saved to:**
- `Documentation/Documents/plans/2026-05-20-frontend-web-rebuild-v2.md` (Phases 0-6)
- `Documentation/Documents/plans/2026-05-20-frontend-web-rebuild-v2-part2.md` (Phases 7-13)
- `Documentation/Documents/plans/2026-05-20-frontend-web-rebuild-v2-part3.md` (Phases 14-23)

**Two execution options:**

1. **Subagent-Driven (recommended)** — fresh subagent per phase, review between phases, fast iteration. Best when you want to keep main session light.

2. **Inline Execution** — execute tasks in this session using executing-plans, batch execution with checkpoints. Best for quick momentum.

**Which approach do you want?**
