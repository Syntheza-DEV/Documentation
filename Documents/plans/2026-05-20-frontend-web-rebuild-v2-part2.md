# Frontend Web Rebuild v2 — Part 2 (Phases 7-13)

> Continuation of `2026-05-20-frontend-web-rebuild-v2.md`. Refer to the header and overview in Part 1.

---

## Phase 7 — Data services (12 services copied from mobile)

**Files:**
- Create: `src/services/{user,feed,publisher,publisherSubscription,like,bookmark,comment,follow,notification,trust,search,preferences,avatar}/*.ts`

All services are **verbatim copies from `frontend-mobile/src/services/`** with no modification. The import paths use `@/services/api` which resolves identically on web. Each step is one service.

- [ ] **Step 7.1: Create `src/services/user/userTypes.ts`**

```ts
import type { AuthUser } from "@/services/auth/authTypes"

export type User = AuthUser

export interface UpdateUserRequest {
  name?: string
  email?: string
  avatar?: string
  bio?: string
}

export interface UserApiResponse {
  success: boolean
  data: User
}

export interface UserUpdateApiResponse {
  success: boolean
  data: string
}

export type GetUserResult = { kind: "ok"; data: User } | { kind: "error"; message: string }
export type UpdateUserResult = { kind: "ok" } | { kind: "error"; message: string }
export type DeleteUserResult = { kind: "ok" } | { kind: "error"; message: string }
```

- [ ] **Step 7.2: Create `src/services/user/userService.ts`**

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { ApiErrorResponse } from "@/services/auth/authTypes"
import type {
  UpdateUserRequest,
  UserApiResponse,
  UserUpdateApiResponse,
  GetUserResult,
  UpdateUserResult,
  DeleteUserResult,
} from "./userTypes"

export const userService = {
  getById: async (id: string): Promise<GetUserResult> => {
    const response = await api.apisauce.get<UserApiResponse | ApiErrorResponse>(`/api/user/${id}`)
    if (
      response.ok &&
      response.data &&
      "data" in response.data &&
      typeof response.data.data === "object"
    ) {
      return { kind: "ok", data: response.data.data }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getByEmail: async (email: string): Promise<GetUserResult> => {
    const response = await api.apisauce.get<UserApiResponse | ApiErrorResponse>(
      `/api/user/email/${email}`,
    )
    if (
      response.ok &&
      response.data &&
      "data" in response.data &&
      typeof response.data.data === "object"
    ) {
      return { kind: "ok", data: response.data.data }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getMe: async (): Promise<GetUserResult> => {
    const response = await api.apisauce.get<UserApiResponse | ApiErrorResponse>("/api/user/me")
    if (
      response.ok &&
      response.data &&
      "data" in response.data &&
      typeof response.data.data === "object"
    ) {
      return { kind: "ok", data: response.data.data }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  updateMe: async (data: UpdateUserRequest): Promise<UpdateUserResult> => {
    const response = await api.apisauce.put<UserApiResponse | ApiErrorResponse>(
      "/api/user/me",
      data,
    )
    if (response.ok) return { kind: "ok" }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  update: async (id: string, data: UpdateUserRequest): Promise<UpdateUserResult> => {
    const response = await api.apisauce.put<UserUpdateApiResponse | ApiErrorResponse>(
      `/api/user/${id}`,
      data,
    )
    if (response.ok) return { kind: "ok" }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  delete: async (id: string): Promise<DeleteUserResult> => {
    const response = await api.apisauce.delete(`/api/user/${id}`)
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.3: Create `src/services/feed/feedTypes.ts`**

```ts
export interface FeedPublisher {
  id: number
  name: string
  slug?: string
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

export interface FeedResponse {
  items: FeedItem[]
  total: number
  page: number
  totalPages: number
}

export interface FeedQuery {
  page?: number
  limit?: number
  sortBy?: "date" | "relevance" | "trust" | "engagement"
  sourceType?: string
  sourceId?: number
}

export interface DigestResponse {
  id: number
  content: string
  createdAt: string
}

export interface OverviewResponse {
  totalItems: number
  totalSources: number
  activeSources: number
  totalSummaries: number
  itemsLast24h: number
  sourceBreakdown: Array<{ type: string; count: number }>
}

export type GetFeedResult =
  | { kind: "ok"; data: FeedResponse }
  | { kind: "error"; message: string }

export type GetItemResult = { kind: "ok"; data: FeedItem } | { kind: "error"; message: string }

export type GetDigestResult =
  | { kind: "ok"; data: DigestResponse | null }
  | { kind: "error"; message: string }

export type GetOverviewResult =
  | { kind: "ok"; data: OverviewResponse }
  | { kind: "error"; message: string }
```

- [ ] **Step 7.4: Create `src/services/feed/feedService.ts`**

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { ApiErrorResponse } from "@/services/auth/authTypes"
import type {
  FeedQuery,
  FeedResponse,
  FeedItem,
  DigestResponse,
  OverviewResponse,
  GetFeedResult,
  GetItemResult,
  GetDigestResult,
  GetOverviewResult,
} from "./feedTypes"

interface ApiResponse<T> {
  success: boolean
  data: T
}

export const feedService = {
  getFeed: async (query: FeedQuery = {}): Promise<GetFeedResult> => {
    const params: Record<string, string | number> = {}
    if (query.page) params.page = query.page
    if (query.limit) params.limit = query.limit
    if (query.sortBy) params.sortBy = query.sortBy
    if (query.sourceType) params.sourceType = query.sourceType
    if (query.sourceId) params.sourceId = query.sourceId

    const response = await api.apisauce.get<ApiResponse<FeedResponse> | ApiErrorResponse>(
      "/api/feed",
      params,
    )
    if (response.ok && response.data && "data" in response.data && response.data.success) {
      return { kind: "ok", data: response.data.data as FeedResponse }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getItem: async (itemId: number): Promise<GetItemResult> => {
    const response = await api.apisauce.get<ApiResponse<FeedItem> | ApiErrorResponse>(
      `/api/feed/item/${itemId}`,
    )
    if (response.ok && response.data && "data" in response.data && response.data.success) {
      return { kind: "ok", data: response.data.data as FeedItem }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getDigest: async (): Promise<GetDigestResult> => {
    const response = await api.apisauce.get<
      ApiResponse<DigestResponse | null> | ApiErrorResponse
    >("/api/feed/digest")
    if (response.ok && response.data && "data" in response.data && response.data.success) {
      return { kind: "ok", data: response.data.data as DigestResponse | null }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getOverview: async (): Promise<GetOverviewResult> => {
    const response = await api.apisauce.get<ApiResponse<OverviewResponse> | ApiErrorResponse>(
      "/api/feed/overview",
    )
    if (response.ok && response.data && "data" in response.data && response.data.success) {
      return { kind: "ok", data: response.data.data as OverviewResponse }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.5: Create `src/services/publisher/publisherTypes.ts` + `publisherService.ts`**

`publisherTypes.ts`:

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

`publisherService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { GetPublishersResult, GetPublisherResult } from "./publisherTypes"

export const publisherService = {
  getAll: async (): Promise<GetPublishersResult> => {
    const response = await api.apisauce.get("/api/publishers")
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      const payload = (response.data as { data: unknown }).data as
        | { publishers?: unknown[] }
        | unknown[]
      const list = Array.isArray(payload) ? payload : (payload.publishers ?? [])
      return { kind: "ok", data: list as GetPublishersResult extends { kind: "ok"; data: infer T } ? T : never }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getById: async (id: number): Promise<GetPublisherResult> => {
    const response = await api.apisauce.get(`/api/publishers/${id}`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      const payload = (response.data as { data: unknown }).data as
        | { publisher?: unknown }
        | unknown
      const publisher = (payload as { publisher?: unknown }).publisher ?? payload
      return { kind: "ok", data: publisher as GetPublisherResult extends { kind: "ok"; data: infer T } ? T : never }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.6: Create `src/services/publisherSubscription/publisherSubscriptionTypes.ts` + `publisherSubscriptionService.ts`**

`publisherSubscriptionTypes.ts`:

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
```

`publisherSubscriptionService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  TogglePublisherSubscriptionResult,
  GetMyPublisherSubscriptionsResult,
  PublisherSubscription,
} from "./publisherSubscriptionTypes"

export const publisherSubscriptionService = {
  toggle: async (publisherId: number): Promise<TogglePublisherSubscriptionResult> => {
    const response = await api.apisauce.post(`/api/publisher-subscriptions/${publisherId}/toggle`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { subscribed: boolean } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getMine: async (): Promise<GetMyPublisherSubscriptionsResult> => {
    const response = await api.apisauce.get("/api/publisher-subscriptions")
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      const payload = (response.data as { data: unknown }).data as
        | { subscriptions?: PublisherSubscription[] }
        | PublisherSubscription[]
      const list = Array.isArray(payload) ? payload : (payload.subscriptions ?? [])
      return { kind: "ok", data: list }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.7: Create `src/services/like/{likeTypes.ts, likeService.ts}`**

`likeTypes.ts`:

```ts
export type ToggleLikeResult =
  | { kind: "ok"; data: { liked: boolean; count: number } }
  | { kind: "error"; message: string }

export type GetLikeCountResult =
  | { kind: "ok"; data: { count: number } }
  | { kind: "error"; message: string }
```

`likeService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { ToggleLikeResult, GetLikeCountResult } from "./likeTypes"

export const likeService = {
  toggle: async (itemId: number): Promise<ToggleLikeResult> => {
    const response = await api.apisauce.post(`/api/likes/${itemId}/toggle`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { liked: boolean; count: number } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getCount: async (itemId: number): Promise<GetLikeCountResult> => {
    const response = await api.apisauce.get(`/api/likes/${itemId}/count`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { count: number } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.8: Create `src/services/bookmark/{bookmarkTypes.ts, bookmarkService.ts}`**

`bookmarkTypes.ts`:

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

export interface BookmarkListResponse {
  items: BookmarkItem[]
  total: number
  page: number
  totalPages: number
}

export type ToggleBookmarkResult =
  | { kind: "ok"; data: { bookmarked: boolean; count: number } }
  | { kind: "error"; message: string }

export type GetBookmarksResult =
  | { kind: "ok"; data: BookmarkListResponse }
  | { kind: "error"; message: string }
```

`bookmarkService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { ToggleBookmarkResult, GetBookmarksResult, BookmarkListResponse } from "./bookmarkTypes"

export const bookmarkService = {
  toggle: async (itemId: number): Promise<ToggleBookmarkResult> => {
    const response = await api.apisauce.post(`/api/bookmarks/${itemId}/toggle`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { bookmarked: boolean; count: number } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getMyBookmarks: async (page = 1, limit = 20): Promise<GetBookmarksResult> => {
    const response = await api.apisauce.get("/api/bookmarks", { page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: BookmarkListResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.9: Create `src/services/comment/{commentTypes.ts, commentService.ts}`**

`commentTypes.ts`:

```ts
export interface Comment {
  id: number
  userId: number
  itemId: number
  content: string
  createdAt: string
  updatedAt: string
  user: { id: number; name: string; avatarUrl: string | null }
}

export interface CommentListResponse {
  comments: Comment[]
  total: number
  page: number
  totalPages: number
}

export type GetCommentsResult =
  | { kind: "ok"; data: CommentListResponse }
  | { kind: "error"; message: string }

export type CreateCommentResult = { kind: "ok"; data: Comment } | { kind: "error"; message: string }
export type UpdateCommentResult = { kind: "ok"; data: Comment } | { kind: "error"; message: string }
export type DeleteCommentResult = { kind: "ok" } | { kind: "error"; message: string }
```

`commentService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  GetCommentsResult,
  CreateCommentResult,
  UpdateCommentResult,
  DeleteCommentResult,
  Comment,
  CommentListResponse,
} from "./commentTypes"

export const commentService = {
  getByItem: async (itemId: number, page = 1, limit = 20): Promise<GetCommentsResult> => {
    const response = await api.apisauce.get(`/api/comments/item/${itemId}`, { page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: CommentListResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  create: async (itemId: number, content: string): Promise<CreateCommentResult> => {
    const response = await api.apisauce.post(`/api/comments/item/${itemId}`, { content })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: Comment }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  update: async (commentId: number, content: string): Promise<UpdateCommentResult> => {
    const response = await api.apisauce.put(`/api/comments/${commentId}`, { content })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: Comment }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  delete: async (commentId: number): Promise<DeleteCommentResult> => {
    const response = await api.apisauce.delete(`/api/comments/${commentId}`)
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.10: Create `src/services/follow/{followTypes.ts, followService.ts}`**

`followTypes.ts`:

```ts
export interface FollowUser {
  id: number
  name: string
  avatarUrl: string | null
  bio: string | null
}

export interface FollowListResponse {
  users: FollowUser[]
  total: number
  page: number
  totalPages: number
}

export interface FollowCounts {
  followers: number
  following: number
}

export type ToggleFollowResult =
  | { kind: "ok"; data: { following: boolean } }
  | { kind: "error"; message: string }

export type GetFollowListResult =
  | { kind: "ok"; data: FollowListResponse }
  | { kind: "error"; message: string }

export type GetFollowCountsResult =
  | { kind: "ok"; data: FollowCounts }
  | { kind: "error"; message: string }

export type IsFollowingResult = { kind: "ok"; data: boolean } | { kind: "error"; message: string }
```

`followService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  ToggleFollowResult,
  GetFollowListResult,
  GetFollowCountsResult,
  IsFollowingResult,
  FollowListResponse,
  FollowCounts,
} from "./followTypes"

export const followService = {
  toggle: async (userId: number): Promise<ToggleFollowResult> => {
    const response = await api.apisauce.post(`/api/follow/${userId}/toggle`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { following: boolean } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getFollowers: async (userId: number, page = 1, limit = 20): Promise<GetFollowListResult> => {
    const response = await api.apisauce.get(`/api/follow/${userId}/followers`, { page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: FollowListResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getFollowing: async (userId: number, page = 1, limit = 20): Promise<GetFollowListResult> => {
    const response = await api.apisauce.get(`/api/follow/${userId}/following`, { page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: FollowListResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getCounts: async (userId: number): Promise<GetFollowCountsResult> => {
    const response = await api.apisauce.get(`/api/follow/${userId}/counts`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: FollowCounts }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  isFollowing: async (userId: number): Promise<IsFollowingResult> => {
    const response = await api.apisauce.get(`/api/follow/${userId}/is-following`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: boolean }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.11: Create `src/services/notification/{notificationTypes.ts, notificationService.ts}`**

`notificationTypes.ts`:

```ts
export interface Notification {
  id: number
  userId: number
  type: string
  title: string
  message: string
  isRead: boolean
  relatedId: number | null
  createdAt: string
}

export interface NotificationListResponse {
  notifications: Notification[]
  total: number
  page: number
  totalPages: number
}

export type GetNotificationsResult =
  | { kind: "ok"; data: NotificationListResponse }
  | { kind: "error"; message: string }

export type GetUnreadCountResult =
  | { kind: "ok"; data: { count: number } }
  | { kind: "error"; message: string }

export type NotificationActionResult = { kind: "ok" } | { kind: "error"; message: string }
```

`notificationService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type {
  GetNotificationsResult,
  GetUnreadCountResult,
  NotificationActionResult,
  NotificationListResponse,
} from "./notificationTypes"

interface ApiSuccess<T> {
  success: boolean
  data: T
}

export const notificationService = {
  getAll: async (page = 1, limit = 20): Promise<GetNotificationsResult> => {
    const response = await api.apisauce.get<ApiSuccess<NotificationListResponse>>(
      "/api/notifications",
      { page, limit },
    )
    if (response.ok && response.data?.success && response.data.data) {
      return { kind: "ok", data: response.data.data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  getUnreadCount: async (): Promise<GetUnreadCountResult> => {
    const response = await api.apisauce.get<ApiSuccess<{ count: number }>>(
      "/api/notifications/unread-count",
    )
    if (response.ok && response.data?.success && response.data.data) {
      return { kind: "ok", data: response.data.data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  markRead: async (id: number): Promise<NotificationActionResult> => {
    const response = await api.apisauce.patch(`/api/notifications/${id}/read`)
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  markAllRead: async (): Promise<NotificationActionResult> => {
    const response = await api.apisauce.patch("/api/notifications/read-all")
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  delete: async (id: number): Promise<NotificationActionResult> => {
    const response = await api.apisauce.delete(`/api/notifications/${id}`)
    if (response.ok) return { kind: "ok" }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.12: Create `src/services/trust/{trustTypes.ts, trustService.ts}`**

`trustTypes.ts`:

```ts
export interface TrustAnalysis {
  trustScore: number
  analysis?: string
}

export type GetTrustResult =
  | { kind: "ok"; data: { trustScore: number | null } }
  | { kind: "error"; message: string }

export type AnalyzeTrustResult =
  | { kind: "ok"; data: TrustAnalysis }
  | { kind: "error"; message: string }
```

`trustService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { GetTrustResult, AnalyzeTrustResult, TrustAnalysis } from "./trustTypes"

export const trustService = {
  get: async (itemId: number): Promise<GetTrustResult> => {
    const response = await api.apisauce.get(`/api/trust/${itemId}`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { trustScore: number | null } }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  analyze: async (itemId: number): Promise<AnalyzeTrustResult> => {
    const response = await api.apisauce.post(`/api/trust/${itemId}/analyze`)
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: TrustAnalysis }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.13: Create `src/services/search/{searchTypes.ts, searchService.ts}`**

`searchTypes.ts`:

```ts
import type { FeedResponse } from "@/services/feed/feedTypes"

export interface SearchUser {
  id: number
  name: string
  avatarUrl: string | null
  bio: string | null
}

export interface SearchUsersResponse {
  users: SearchUser[]
  total: number
  page: number
  totalPages: number
}

export type SearchArticlesResult =
  | { kind: "ok"; data: FeedResponse }
  | { kind: "error"; message: string }

export type SearchUsersResult =
  | { kind: "ok"; data: SearchUsersResponse }
  | { kind: "error"; message: string }
```

`searchService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { FeedResponse } from "@/services/feed/feedTypes"
import type { SearchArticlesResult, SearchUsersResult, SearchUsersResponse } from "./searchTypes"

export const searchService = {
  searchArticles: async (
    query: string,
    page = 1,
    limit = 20,
  ): Promise<SearchArticlesResult> => {
    const response = await api.apisauce.get("/api/search", { q: query, page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: FeedResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  searchUsers: async (query: string, page = 1, limit = 20): Promise<SearchUsersResult> => {
    const response = await api.apisauce.get("/api/user/search", { q: query, page, limit })
    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: SearchUsersResponse }).data }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.14: Create `src/services/preferences/{preferencesTypes.ts, preferencesService.ts}`**

`preferencesTypes.ts`:

```ts
export interface UserPreferences {
  id: number
  userId: number
  theme: string
  language: string
  textSize: string
  timezone: string
  profileVisibility: string
  showEmail: boolean
  allowMessages: boolean
  showActivity: boolean
  emailNotifications: boolean
  pushNotifications: boolean
  newFollowerNotifications: boolean
  commentNotifications: boolean
  likeNotifications: boolean
  articleNotifications: boolean
  emailDigest: boolean
  digestFrequency: string
  twoFactorAuth: boolean
  loginAlerts: boolean
  createdAt: string
  updatedAt: string
}

export interface PreferencesApiResponse {
  success: boolean
  data: UserPreferences
}

export type GetPreferencesResult =
  | { kind: "ok"; data: UserPreferences }
  | { kind: "error"; message: string }

export type UpdatePreferencesResult =
  | { kind: "ok"; data: UserPreferences }
  | { kind: "error"; message: string }
```

`preferencesService.ts`:

```ts
import { api } from "@/services/api"
import { getGeneralApiProblem } from "@/services/api/apiProblem"
import type { ApiErrorResponse } from "@/services/auth/authTypes"
import type {
  PreferencesApiResponse,
  GetPreferencesResult,
  UpdatePreferencesResult,
} from "./preferencesTypes"

export const preferencesService = {
  get: async (): Promise<GetPreferencesResult> => {
    const response = await api.apisauce.get<PreferencesApiResponse | ApiErrorResponse>(
      "/api/preferences",
    )
    if (response.ok && response.data && "data" in response.data) {
      return { kind: "ok", data: response.data.data }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },

  update: async (data: Record<string, unknown>): Promise<UpdatePreferencesResult> => {
    const response = await api.apisauce.patch<PreferencesApiResponse | ApiErrorResponse>(
      "/api/preferences",
      data,
    )
    if (response.ok && response.data && "data" in response.data) {
      return { kind: "ok", data: response.data.data }
    }
    if (response.data && "error" in response.data) {
      return { kind: "error", message: response.data.error.message }
    }
    const problem = getGeneralApiProblem(response)
    return { kind: "error", message: problem?.kind ?? "unknown-error" }
  },
}
```

- [ ] **Step 7.15: Create `src/services/avatar/avatarService.ts` (web variant — File instead of imageUri)**

```ts
import { api } from "@/services/api"

export type UploadAvatarResult =
  | { kind: "ok"; data: { avatarUrl: string } }
  | { kind: "error"; message: string }

export const avatarService = {
  upload: async (file: File): Promise<UploadAvatarResult> => {
    const formData = new FormData()
    formData.append("avatar", file)

    const response = await api.apisauce.post("/api/user/me/avatar", formData, {
      headers: { "Content-Type": "multipart/form-data" },
    })

    if (response.ok && response.data && (response.data as { success?: boolean }).success) {
      return { kind: "ok", data: (response.data as { data: { avatarUrl: string } }).data }
    }
    return { kind: "error", message: "Upload failed" }
  },
}
```

- [ ] **Step 7.16: Run typecheck on all services**

```bash
pnpm typecheck
```

Expected: 0 errors.

- [ ] **Step 7.17: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): 12 data services copied from mobile (feed, publisher, like, bookmark, comment, follow, notification, trust, search, preferences, user, avatar)"
```

---

## Phase 8 — QueryClient + AuthContext + preferencesStore

**Files:**
- Install: `@tanstack/react-query`, `@tanstack/react-query-devtools`, `zustand`
- Create: `src/hooks/queryKeys.ts`, `src/hooks/useDebounce.ts`, `src/lib/queryClient.ts`, `src/stores/preferencesStore.ts`, `src/contexts/AuthContext.tsx`

- [ ] **Step 8.1: Install deps**

```bash
pnpm add @tanstack/react-query@^5.90 @tanstack/react-query-devtools@^5.90 zustand@^5
```

- [ ] **Step 8.2: Create `src/hooks/queryKeys.ts` (copied from mobile)**

```ts
export const queryKeys = {
  feed: {
    all: ["feed"] as const,
    list: (page: number) => ["feed", "list", page] as const,
    item: (id: number) => ["feed", "item", id] as const,
    digest: ["feed", "digest"] as const,
    overview: ["feed", "overview"] as const,
  },
  search: {
    articles: (q: string) => ["search", "articles", q] as const,
    users: (q: string) => ["search", "users", q] as const,
  },
  likes: {
    item: (id: number) => ["likes", id] as const,
  },
  comments: {
    byItem: (id: number) => ["comments", id] as const,
  },
  bookmarks: {
    all: ["bookmarks"] as const,
  },
  follow: {
    counts: (userId: number) => ["follow", userId, "counts"] as const,
    followers: (userId: number) => ["follow", userId, "followers"] as const,
    following: (userId: number) => ["follow", userId, "following"] as const,
    isFollowing: (userId: number) => ["follow", "isFollowing", userId] as const,
  },
  publisherSubscriptions: {
    mine: ["publisherSubscriptions", "mine"] as const,
  },
  notifications: {
    all: ["notifications"] as const,
    unreadCount: ["notifications", "unread-count"] as const,
  },
  trust: {
    item: (id: number) => ["trust", id] as const,
  },
  publishers: {
    all: ["publishers"] as const,
  },
  user: {
    me: ["user", "me"] as const,
    byId: (id: number) => ["user", id] as const,
  },
} as const
```

- [ ] **Step 8.3: Create `src/hooks/useDebounce.ts`**

```ts
import { useEffect, useState } from "react"

export function useDebounce<T>(value: T, delay = 300): T {
  const [debounced, setDebounced] = useState(value)
  useEffect(() => {
    const t = window.setTimeout(() => setDebounced(value), delay)
    return () => window.clearTimeout(t)
  }, [value, delay])
  return debounced
}
```

- [ ] **Step 8.4: Create `src/lib/queryClient.ts`**

```ts
import { QueryClient } from "@tanstack/react-query"

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,
      gcTime: 10 * 60 * 1000,
      refetchOnWindowFocus: false,
      retry: 1,
    },
    mutations: {
      retry: 0,
    },
  },
})
```

- [ ] **Step 8.5: Create `src/stores/preferencesStore.ts` (copied verbatim from mobile)**

```ts
import { create } from "zustand"
import { preferencesService } from "@/services/preferences/preferencesService"
import type { UserPreferences } from "@/services/preferences/preferencesTypes"

interface PreferencesState {
  preferences: UserPreferences | null
  loading: boolean
  error: string | null
  fetchPreferences: () => Promise<void>
  updatePreference: (key: string, value: unknown) => Promise<void>
}

const usePreferencesStore = create<PreferencesState>((set, get) => ({
  preferences: null,
  loading: false,
  error: null,

  fetchPreferences: async () => {
    set({ loading: true, error: null })
    const result = await preferencesService.get()
    if (result.kind === "ok") {
      set({ preferences: result.data, loading: false })
    } else {
      set({ error: result.message, loading: false })
    }
  },

  updatePreference: async (key: string, value: unknown) => {
    const prevSnapshot = get().preferences
    set({ error: null })
    if (prevSnapshot) {
      set({ preferences: { ...prevSnapshot, [key]: value } })
    }
    const result = await preferencesService.update({ [key]: value })
    if (result.kind === "ok") {
      set({ preferences: result.data })
    } else {
      const current = get().preferences
      if (current && prevSnapshot) {
        set({
          preferences: {
            ...current,
            [key]: prevSnapshot[key as keyof UserPreferences],
          },
          error: result.message,
        })
      } else {
        set({ error: result.message })
      }
    }
  },
}))

export default usePreferencesStore
```

- [ ] **Step 8.6: Create `src/contexts/AuthContext.tsx` (web variant — react-router navigate instead of expo-router)**

```tsx
import { createContext, useCallback, useContext, useEffect, useState } from "react"
import { useNavigate } from "react-router-dom"
import { authService } from "@/services/auth/authService"
import { tokenService } from "@/services/auth/tokenService"
import { api } from "@/services/api"
import type {
  AuthUser,
  LoginRequest,
  RegisterRequest,
  ForgotPasswordRequest,
} from "@/services/auth/authTypes"

interface AuthContextType {
  user: AuthUser | null
  isAuthenticated: boolean
  isLoading: boolean
  login: (credentials: LoginRequest) => Promise<{ error?: string }>
  register: (data: RegisterRequest) => Promise<{ error?: string }>
  forgotPassword: (data: ForgotPasswordRequest) => Promise<{ error?: string; success?: boolean }>
  loginWithGoogle: (idToken: string) => Promise<{ error?: string }>
  logout: () => Promise<void>
}

const AuthContext = createContext<AuthContextType | null>(null)

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<AuthUser | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  const navigate = useNavigate()

  const logout = useCallback(async () => {
    await authService.logout().catch(() => {})
    await tokenService.clearAll()
    api.clearAuthToken()
    setUser(null)
    navigate("/login", { replace: true })
  }, [navigate])

  useEffect(() => {
    api.setAuthFailureHandler(async () => {
      setUser(null)
      navigate("/login", { replace: true })
    })
    return () => api.setAuthFailureHandler(null)
  }, [navigate])

  useEffect(() => {
    const restore = async () => {
      const storedUser = tokenService.getUser()
      const token = await tokenService.getToken()
      if (storedUser && token) {
        setUser(storedUser)
        api.setAuthToken(token)
      }
      setIsLoading(false)
    }
    restore()
  }, [])

  const login = useCallback(async (credentials: LoginRequest) => {
    const result = await authService.login(credentials)
    if (result.kind === "ok") {
      await tokenService.saveToken(result.token)
      if (result.refreshToken) await tokenService.saveRefreshToken(result.refreshToken)
      tokenService.saveUser(result.user)
      api.setAuthToken(result.token)
      setUser(result.user)
      return {}
    }
    return { error: result.message }
  }, [])

  const register = useCallback(async (data: RegisterRequest) => {
    const result = await authService.register(data)
    if (result.kind === "ok") {
      await tokenService.saveToken(result.token)
      if (result.refreshToken) await tokenService.saveRefreshToken(result.refreshToken)
      tokenService.saveUser(result.user)
      api.setAuthToken(result.token)
      setUser(result.user)
      return {}
    }
    return { error: result.message }
  }, [])

  const forgotPassword = useCallback(async (data: ForgotPasswordRequest) => {
    const result = await authService.forgotPassword(data)
    if (result.kind === "ok") return { success: true }
    return { error: result.message }
  }, [])

  const loginWithGoogle = useCallback(async (idToken: string) => {
    const result = await authService.googleAuth(idToken)
    if (result.kind === "ok") {
      await tokenService.saveToken(result.token)
      if (result.refreshToken) await tokenService.saveRefreshToken(result.refreshToken)
      tokenService.saveUser(result.user)
      api.setAuthToken(result.token)
      setUser(result.user)
      return {}
    }
    return { error: result.message }
  }, [])

  return (
    <AuthContext.Provider
      value={{
        user,
        isAuthenticated: !!user,
        isLoading,
        login,
        register,
        forgotPassword,
        loginWithGoogle,
        logout,
      }}
    >
      {children}
    </AuthContext.Provider>
  )
}

export function useAuth() {
  const ctx = useContext(AuthContext)
  if (!ctx) throw new Error("useAuth must be used within AuthProvider")
  return ctx
}
```

- [ ] **Step 8.7: Run typecheck**

```bash
pnpm typecheck
```

Expected: 0 errors. (Note: `react-router-dom` import will fail until Phase 10 installs it. Install it now to allow typecheck to pass.)

```bash
pnpm add react-router-dom@^7.9.5 react-router@^7.9.5
```

- [ ] **Step 8.8: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): QueryClient + AuthContext + preferencesStore"
```

---

## Phase 9 — i18n FR setup

**Files:**
- Install: `i18next`, `react-i18next`
- Create: `src/i18n/fr.ts`, `src/i18n/index.ts`, `src/i18n/types.ts`

- [ ] **Step 9.1: Install i18next**

```bash
pnpm add i18next@^23 react-i18next@^15
```

- [ ] **Step 9.2: Create `src/i18n/fr.ts` (minimal FR translations, structure ready for more)**

```ts
export const fr = {
  common: {
    cancel: "Annuler",
    save: "Enregistrer",
    delete: "Supprimer",
    edit: "Modifier",
    confirm: "Confirmer",
    loading: "Chargement…",
    error: "Erreur",
    retry: "Réessayer",
    close: "Fermer",
    search: "Rechercher",
    seeMore: "Voir plus",
  },
  auth: {
    login: "Se connecter",
    register: "Créer un compte",
    forgotPassword: "Mot de passe oublié ?",
    resetPassword: "Réinitialiser le mot de passe",
    email: "Email",
    password: "Mot de passe",
    name: "Nom",
    googleSignIn: "Continuer avec Google",
    logout: "Déconnexion",
    emailSent: "Email envoyé. Vérifie ta boîte de réception.",
  },
  feed: {
    title: "Votre fil d'actualité",
    empty: "Aucun article. Abonnez-vous à des éditeurs pour remplir votre feed.",
    networkError: "Erreur de connexion",
  },
  discover: {
    trending: "Tendances",
    recent: "Récents",
    publishers: "Éditeurs",
    subscribed: "Abonné",
    subscribe: "S'abonner",
  },
  notifications: {
    title: "Notifications",
    markAllRead: "Tout marquer comme lu",
    empty: "Aucune notification",
  },
  settings: {
    title: "Paramètres",
    account: "Compte",
    privacy: "Confidentialité",
    appearance: "Apparence",
    notifications: "Notifications",
    security: "Sécurité",
    dataPrivacy: "Données et vie privée",
    theme: {
      light: "Clair",
      dark: "Sombre",
      system: "Système",
    },
  },
  trust: {
    fiable: "Fiable",
    incertain: "Incertain",
    nonVerifie: "Non vérifié",
    analyzing: "Analyse en cours…",
  },
} as const
```

- [ ] **Step 9.3: Create `src/i18n/types.ts`**

```ts
import type { fr } from "./fr"

export type TranslationKeys = typeof fr
```

- [ ] **Step 9.4: Create `src/i18n/index.ts`**

```ts
import i18n from "i18next"
import { initReactI18next } from "react-i18next"
import { fr } from "./fr"

void i18n.use(initReactI18next).init({
  resources: {
    fr: { translation: fr },
  },
  lng: "fr",
  fallbackLng: "fr",
  interpolation: { escapeValue: false },
})

export default i18n
```

- [ ] **Step 9.5: Update `src/main.tsx` to import i18n early**

Modify `src/main.tsx`:

```tsx
import { StrictMode } from "react"
import { createRoot } from "react-dom/client"
import "./i18n"
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

- [ ] **Step 9.6: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): i18next FR-only with structure ready for more locales"
```

---

## Phase 10 — Router skeleton + ProtectedRoute

**Files:**
- Create: `src/components/ProtectedRoute.tsx`, `src/routes/root.tsx`, `src/routes/(auth)/layout.tsx` (placeholder), `src/routes/(app)/layout.tsx` (placeholder)
- Modify: `src/App.tsx`

- [ ] **Step 10.1: Create `src/components/ProtectedRoute.tsx`**

```tsx
import { Navigate, useLocation } from "react-router-dom"
import { useAuth } from "@/contexts/AuthContext"

export function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { isAuthenticated, isLoading } = useAuth()
  const location = useLocation()

  if (isLoading) {
    return (
      <div className="flex min-h-screen items-center justify-center">
        <p className="text-color3 text-sm">Chargement…</p>
      </div>
    )
  }

  if (!isAuthenticated) {
    return <Navigate to="/login" replace state={{ from: location }} />
  }

  return <>{children}</>
}

export function GuestRoute({ children }: { children: React.ReactNode }) {
  const { isAuthenticated, isLoading } = useAuth()
  if (isLoading) return null
  if (isAuthenticated) return <Navigate to="/home" replace />
  return <>{children}</>
}
```

- [ ] **Step 10.2: Create placeholder `src/routes/(auth)/layout.tsx`**

```tsx
import { Outlet } from "react-router-dom"

export default function AuthLayout() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-bg px-4">
      <Outlet />
    </div>
  )
}
```

- [ ] **Step 10.3: Create placeholder `src/routes/(app)/layout.tsx` (will be expanded in Phase 13)**

```tsx
import { Outlet } from "react-router-dom"

export default function AppLayout() {
  return (
    <div className="min-h-screen bg-bg">
      <main className="mx-auto max-w-3xl px-4 py-6">
        <Outlet />
      </main>
    </div>
  )
}
```

- [ ] **Step 10.4: Create placeholder pages so router compiles**

`src/routes/(auth)/login.tsx`:

```tsx
export default function LoginPage() {
  return <p>Login (placeholder — Phase 12)</p>
}
```

`src/routes/(auth)/register.tsx`:

```tsx
export default function RegisterPage() {
  return <p>Register (placeholder — Phase 12)</p>
}
```

`src/routes/(auth)/forgot-password.tsx`:

```tsx
export default function ForgotPasswordPage() {
  return <p>Forgot password (placeholder — Phase 12)</p>
}
```

`src/routes/(auth)/reset-password.tsx`:

```tsx
export default function ResetPasswordPage() {
  return <p>Reset password (placeholder — Phase 12)</p>
}
```

`src/routes/(app)/home.tsx`:

```tsx
export default function HomePage() {
  return <p>Home (placeholder — Phase 14)</p>
}
```

- [ ] **Step 10.5: Create `src/routes/root.tsx` (top-level providers + router)**

```tsx
import { Suspense } from "react"
import { BrowserRouter, Routes, Route, Navigate } from "react-router-dom"
import { QueryClientProvider } from "@tanstack/react-query"
import { ReactQueryDevtools } from "@tanstack/react-query-devtools"
import { AuthProvider } from "@/contexts/AuthContext"
import { ProtectedRoute, GuestRoute } from "@/components/ProtectedRoute"
import { queryClient } from "@/lib/queryClient"
import AuthLayout from "./(auth)/layout"
import AppLayout from "./(app)/layout"
import LoginPage from "./(auth)/login"
import RegisterPage from "./(auth)/register"
import ForgotPasswordPage from "./(auth)/forgot-password"
import ResetPasswordPage from "./(auth)/reset-password"
import HomePage from "./(app)/home"

export function Root() {
  return (
    <QueryClientProvider client={queryClient}>
      <BrowserRouter>
        <AuthProvider>
          <Suspense fallback={<div className="p-8 text-color3">Chargement…</div>}>
            <Routes>
              <Route element={<GuestRoute><AuthLayout /></GuestRoute>}>
                <Route path="/login" element={<LoginPage />} />
                <Route path="/register" element={<RegisterPage />} />
                <Route path="/forgot-password" element={<ForgotPasswordPage />} />
                <Route path="/reset-password" element={<ResetPasswordPage />} />
              </Route>

              <Route element={<ProtectedRoute><AppLayout /></ProtectedRoute>}>
                <Route path="/home" element={<HomePage />} />
              </Route>

              <Route path="/" element={<Navigate to="/home" replace />} />
              <Route path="*" element={<Navigate to="/home" replace />} />
            </Routes>
          </Suspense>
        </AuthProvider>
      </BrowserRouter>
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}
```

- [ ] **Step 10.6: Replace `src/App.tsx`**

```tsx
import { Root } from "@/routes/root"

export default function App() {
  return <Root />
}
```

- [ ] **Step 10.7: Verify dev boot + redirect to /login**

```bash
pnpm dev
```

Expected: `localhost:3000` redirects to `/login`, "Login (placeholder)" displays. Stop with Ctrl-C.

- [ ] **Step 10.8: Verify build**

```bash
pnpm build
```

Expected: build OK.

- [ ] **Step 10.9: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): router skeleton + ProtectedRoute + provider tree"
```

---

## Phase 11 — Component library port (22 components, shadcn/Tailwind)

Each step ports one component from `frontend-mobile/src/components/`. Mobile uses Tamagui + RN primitives + Ionicons; web uses Tailwind + lucide-react + Radix. Component names and prop APIs stay identical.

**Files:** `src/components/{ComponentName}.tsx` and `src/components/ui/{primitive}.tsx` as needed.

- [ ] **Step 11.1: Install missing icons + animation deps**

```bash
pnpm add framer-motion@^12
```

(lucide-react was installed in Phase 3.)

- [ ] **Step 11.2: Create `src/components/SectionHeader.tsx`**

```tsx
interface SectionHeaderProps {
  title: string
  action?: React.ReactNode
}

export function SectionHeader({ title, action }: SectionHeaderProps) {
  return (
    <div className="flex items-center justify-between py-2">
      <h2 className="text-base font-semibold text-color2 uppercase tracking-wide">{title}</h2>
      {action}
    </div>
  )
}
```

- [ ] **Step 11.3: Create `src/components/EmptyState.tsx`**

```tsx
import { Inbox } from "lucide-react"
import type { LucideIcon } from "lucide-react"

interface EmptyStateProps {
  icon?: LucideIcon
  title: string
  subtitle?: string
}

export function EmptyState({ icon: Icon = Inbox, title, subtitle }: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center gap-3 py-12">
      <Icon size={48} className="text-color4" />
      <p className="text-center text-base font-semibold text-color2">{title}</p>
      {subtitle && <p className="px-8 text-center text-sm text-color3">{subtitle}</p>}
    </div>
  )
}
```

- [ ] **Step 11.4: Create `src/components/Skeleton.tsx`**

```tsx
import { cn } from "@/lib/utils"

interface SkeletonProps {
  className?: string
}

export function Skeleton({ className }: SkeletonProps) {
  return <div className={cn("animate-pulse rounded-md bg-surface2", className)} />
}

export function ArticleCardSkeleton() {
  return (
    <div className="rounded-xl border border-border-muted bg-surface p-4">
      <div className="flex items-center gap-2">
        <Skeleton className="h-7 w-7 rounded-full" />
        <Skeleton className="h-3 w-24" />
      </div>
      <Skeleton className="mt-3 h-5 w-full" />
      <Skeleton className="mt-2 h-4 w-full" />
      <Skeleton className="mt-1 h-4 w-2/3" />
      <div className="mt-3 flex items-center gap-4">
        <Skeleton className="h-3 w-10" />
        <Skeleton className="h-3 w-10" />
        <Skeleton className="h-3 w-10" />
      </div>
    </div>
  )
}
```

- [ ] **Step 11.5: Create `src/components/Toast.tsx`**

```tsx
import { useEffect } from "react"
import { AnimatePresence, motion } from "framer-motion"
import { AlertCircle, CheckCircle2, Info, X } from "lucide-react"
import { cn } from "@/lib/utils"

interface ToastProps {
  message: string
  type?: "error" | "success" | "info"
  visible: boolean
  onDismiss: () => void
  duration?: number
}

export function Toast({ message, type = "error", visible, onDismiss, duration = 3000 }: ToastProps) {
  useEffect(() => {
    if (!visible) return
    const t = window.setTimeout(onDismiss, duration)
    return () => window.clearTimeout(t)
  }, [visible, duration, onDismiss])

  const bg =
    type === "error" ? "bg-danger" : type === "success" ? "bg-success" : "bg-surface2"
  const Icon = type === "error" ? AlertCircle : type === "success" ? CheckCircle2 : Info

  return (
    <AnimatePresence>
      {visible && (
        <motion.div
          initial={{ y: 30, opacity: 0 }}
          animate={{ y: 0, opacity: 1 }}
          exit={{ y: 30, opacity: 0 }}
          transition={{ duration: 0.2 }}
          className={cn(
            "fixed bottom-6 left-1/2 z-[9999] flex w-[min(420px,90vw)] -translate-x-1/2 items-center gap-3 rounded-xl px-4 py-3 shadow-lg",
            bg,
          )}
        >
          <Icon size={18} className="text-white" />
          <p className="flex-1 text-sm font-medium text-white">{message}</p>
          <button onClick={onDismiss} className="text-white/80 hover:text-white">
            <X size={16} />
          </button>
        </motion.div>
      )}
    </AnimatePresence>
  )
}
```

- [ ] **Step 11.6: Create `src/components/Avatar.tsx`**

```tsx
import * as AvatarPrimitive from "@radix-ui/react-avatar"
import { cn } from "@/lib/utils"

interface AvatarProps {
  src?: string | null
  name?: string
  size?: number
  className?: string
}

export function Avatar({ src, name, size = 40, className }: AvatarProps) {
  const initials = (name ?? "?").slice(0, 2).toUpperCase()
  return (
    <AvatarPrimitive.Root
      className={cn("inline-flex items-center justify-center overflow-hidden rounded-full bg-surface2", className)}
      style={{ width: size, height: size }}
    >
      {src && <AvatarPrimitive.Image src={src} alt={name ?? "avatar"} className="h-full w-full object-cover" />}
      <AvatarPrimitive.Fallback className="text-color2 text-xs font-bold">{initials}</AvatarPrimitive.Fallback>
    </AvatarPrimitive.Root>
  )
}
```

- [ ] **Step 11.7: Create `src/components/Badge.tsx`**

```tsx
import { cn } from "@/lib/utils"

interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement> {
  variant?: "default" | "danger" | "success" | "warning"
}

export function Badge({ variant = "default", className, ...props }: BadgeProps) {
  const colors = {
    default: "bg-surface2 text-color2",
    danger: "bg-danger text-white",
    success: "bg-success text-white",
    warning: "bg-warning text-white",
  }
  return (
    <span
      className={cn("inline-flex items-center rounded-full px-2 py-0.5 text-xs font-semibold", colors[variant], className)}
      {...props}
    />
  )
}
```

- [ ] **Step 11.8: Create `src/components/IconButton.tsx`**

```tsx
import { Button, type ButtonProps } from "@/components/ui/button"

export function IconButton({ children, ...props }: ButtonProps) {
  return (
    <Button variant="ghost" size="icon" {...props}>
      {children}
    </Button>
  )
}
```

- [ ] **Step 11.9: Create `src/components/SearchBar.tsx`**

```tsx
import { Search, X } from "lucide-react"
import { Input } from "@/components/ui/input"
import { cn } from "@/lib/utils"

interface SearchBarProps {
  value: string
  onChange: (v: string) => void
  placeholder?: string
  className?: string
}

export function SearchBar({ value, onChange, placeholder = "Rechercher…", className }: SearchBarProps) {
  return (
    <div className={cn("relative", className)}>
      <Search size={16} className="absolute left-3 top-1/2 -translate-y-1/2 text-color3" />
      <Input
        value={value}
        onChange={(e) => onChange(e.target.value)}
        placeholder={placeholder}
        className="pl-9 pr-9"
      />
      {value && (
        <button
          onClick={() => onChange("")}
          className="absolute right-3 top-1/2 -translate-y-1/2 text-color3 hover:text-color"
          aria-label="Effacer"
        >
          <X size={16} />
        </button>
      )}
    </div>
  )
}
```

- [ ] **Step 11.10: Create `src/components/TabSelector.tsx`**

```tsx
import { Tabs, TabsList, TabsTrigger } from "@/components/ui/tabs"

interface TabItem {
  key: string
  label: string
}

interface TabSelectorProps {
  tabs: TabItem[]
  active: string
  onChange: (key: string) => void
}

export function TabSelector({ tabs, active, onChange }: TabSelectorProps) {
  return (
    <Tabs value={active} onValueChange={onChange}>
      <TabsList>
        {tabs.map((t) => (
          <TabsTrigger key={t.key} value={t.key}>
            {t.label}
          </TabsTrigger>
        ))}
      </TabsList>
    </Tabs>
  )
}
```

- [ ] **Step 11.11: Create `src/components/AutoImage.tsx` (smart `<img>` with fallback)**

```tsx
import { useState } from "react"
import { cn } from "@/lib/utils"

interface AutoImageProps extends Omit<React.ImgHTMLAttributes<HTMLImageElement>, "src"> {
  src: string | null | undefined
  fallback?: React.ReactNode
}

export function AutoImage({ src, alt, fallback, className, ...props }: AutoImageProps) {
  const [errored, setErrored] = useState(false)
  if (!src || errored) {
    return (
      <div
        className={cn("flex items-center justify-center bg-surface2 text-color3", className)}
        aria-label={alt}
      >
        {fallback ?? "🖼"}
      </div>
    )
  }
  return (
    <img
      src={src}
      alt={alt}
      onError={() => setErrored(true)}
      className={cn("object-cover", className)}
      {...props}
    />
  )
}
```

- [ ] **Step 11.12: Create `src/utils/formatDate.ts`**

```ts
export function timeAgo(date: string | null | undefined): string {
  if (!date) return ""
  const d = new Date(date)
  const seconds = Math.floor((Date.now() - d.getTime()) / 1000)
  if (seconds < 60) return "à l'instant"
  const minutes = Math.floor(seconds / 60)
  if (minutes < 60) return `il y a ${minutes} min`
  const hours = Math.floor(minutes / 60)
  if (hours < 24) return `il y a ${hours} h`
  const days = Math.floor(hours / 24)
  if (days < 7) return `il y a ${days} j`
  const weeks = Math.floor(days / 7)
  if (weeks < 5) return `il y a ${weeks} sem`
  const months = Math.floor(days / 30)
  if (months < 12) return `il y a ${months} mois`
  const years = Math.floor(days / 365)
  return `il y a ${years} an${years > 1 ? "s" : ""}`
}
```

- [ ] **Step 11.13: Create `src/components/TrustBadge.tsx`**

```tsx
import { useState } from "react"
import { ShieldCheck } from "lucide-react"
import { TrustDetailSheet } from "./TrustDetailSheet"
import { cn } from "@/lib/utils"

interface TrustBadgeProps {
  score: number | null
  size?: "small" | "normal"
  itemId?: number
}

export function TrustBadge({ score, size = "small", itemId }: TrustBadgeProps) {
  const [open, setOpen] = useState(false)
  if (score === null || score === undefined) return null

  const colorClass =
    score >= 70 ? "text-success" : score >= 40 ? "text-warning" : "text-danger"
  const label = score >= 70 ? "Fiable" : score >= 40 ? "Incertain" : "Non vérifié"
  const iconSize = size === "small" ? 12 : 16
  const textSize = size === "small" ? "text-xs" : "text-sm"

  const content = (
    <span className={cn("inline-flex items-center gap-1 font-bold", colorClass, textSize)}>
      <ShieldCheck size={iconSize} />
      {Math.round(score)}
      {size === "normal" && <span className="font-medium">· {label}</span>}
    </span>
  )

  if (!itemId) return content

  return (
    <>
      <button type="button" onClick={() => setOpen(true)} className="cursor-pointer">
        {content}
      </button>
      <TrustDetailSheet open={open} onOpenChange={setOpen} itemId={itemId} score={score} />
    </>
  )
}
```

- [ ] **Step 11.14: Create `src/components/TrustDetailSheet.tsx`**

```tsx
import { useEffect, useState } from "react"
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog"
import { trustService } from "@/services/trust/trustService"
import { Button } from "@/components/ui/button"
import { ShieldCheck } from "lucide-react"

interface TrustDetailSheetProps {
  open: boolean
  onOpenChange: (v: boolean) => void
  itemId: number
  score: number | null
}

export function TrustDetailSheet({ open, onOpenChange, itemId, score }: TrustDetailSheetProps) {
  const [analysis, setAnalysis] = useState<string | null>(null)
  const [loading, setLoading] = useState(false)

  useEffect(() => {
    if (!open) {
      setAnalysis(null)
      return
    }
  }, [open])

  const handleAnalyze = async () => {
    setLoading(true)
    const result = await trustService.analyze(itemId)
    setLoading(false)
    if (result.kind === "ok") setAnalysis(result.data.analysis ?? "Score régénéré.")
    else setAnalysis(`Erreur: ${result.message}`)
  }

  const colorClass = !score ? "text-color3" : score >= 70 ? "text-success" : score >= 40 ? "text-warning" : "text-danger"

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent>
        <DialogHeader>
          <DialogTitle className="flex items-center gap-2">
            <ShieldCheck className={colorClass} />
            Analyse de fiabilité
          </DialogTitle>
        </DialogHeader>
        <div className="space-y-4">
          <div className="rounded-lg bg-surface2 p-4">
            <p className="text-color3 text-sm">Score</p>
            <p className={`text-3xl font-bold ${colorClass}`}>{score ?? "—"}</p>
          </div>
          {analysis ? (
            <p className="text-sm text-color leading-relaxed whitespace-pre-wrap">{analysis}</p>
          ) : (
            <p className="text-sm text-color3">
              Aucune analyse détaillée pour l'instant. Lance une nouvelle analyse IA si tu veux plus de contexte.
            </p>
          )}
          <Button onClick={handleAnalyze} disabled={loading} className="w-full">
            {loading ? "Analyse…" : "Analyser avec l'IA"}
          </Button>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

- [ ] **Step 11.15: Create `src/components/ArticleCard.tsx`**

```tsx
import { Bookmark, Heart, MessageCircle } from "lucide-react"
import { motion } from "framer-motion"
import type { FeedItem } from "@/services/feed/feedTypes"
import { timeAgo } from "@/utils/formatDate"
import { TrustBadge } from "./TrustBadge"
import { cn } from "@/lib/utils"

interface ArticleCardProps {
  article: FeedItem
  onPress?: () => void
  onLike?: () => void
  onBookmark?: () => void
  isBookmarked?: boolean
}

export function ArticleCard({ article, onPress, onLike, onBookmark, isBookmarked = false }: ArticleCardProps) {
  const sourceName = article.publisher?.name ?? "Source"
  const sourceInitials = sourceName.slice(0, 2).toUpperCase()

  return (
    <motion.article
      whileHover={{ y: -2 }}
      transition={{ duration: 0.15 }}
      onClick={onPress}
      className="cursor-pointer rounded-xl border border-border-muted bg-surface p-4 hover:bg-hover"
    >
      <div className="flex flex-col gap-2.5">
        <div className="flex items-center gap-2">
          <span className="flex h-7 w-7 items-center justify-center rounded-full bg-surface2 text-[11px] font-bold text-color2">
            {sourceInitials}
          </span>
          <span className="flex-1 truncate text-sm font-medium text-color2">{sourceName}</span>
          <span className="text-xs text-color3">{timeAgo(article.publishedAt ?? article.createdAt)}</span>
          <TrustBadge score={article.trustScore} itemId={article.id} />
        </div>

        <h3 className="text-base font-bold leading-snug text-color">{article.title}</h3>
        {article.summary && (
          <p className="line-clamp-2 text-sm text-color3 leading-relaxed">
            {article.summary.replace(/<[^>]*>/g, "")}
          </p>
        )}

        <div className="flex items-center gap-5 pt-1">
          <button
            onClick={(e) => {
              e.stopPropagation()
              onLike?.()
            }}
            className="flex items-center gap-1.5 text-color3 hover:text-danger"
          >
            <Heart size={16} className={cn(article.isLiked && "fill-danger text-danger")} />
            <span className="text-xs">{article.likesCount}</span>
          </button>
          <span className="flex items-center gap-1.5 text-color3">
            <MessageCircle size={15} />
            <span className="text-xs">{article.commentsCount}</span>
          </span>
          <button
            onClick={(e) => {
              e.stopPropagation()
              onBookmark?.()
            }}
            className="ml-auto text-color3 hover:text-color"
          >
            <Bookmark size={16} className={cn(isBookmarked && "fill-color text-color")} />
          </button>
        </div>
      </div>
    </motion.article>
  )
}
```

- [ ] **Step 11.16: Create `src/components/PublisherCard.tsx`**

```tsx
import { Newspaper } from "lucide-react"
import { Button } from "@/components/ui/button"

interface PublisherCardProps {
  publisher: { id: number; name: string; slug?: string; subscriberCount?: number }
  isSubscribed: boolean
  onToggleSubscribe: () => void
}

export function PublisherCard({ publisher, isSubscribed, onToggleSubscribe }: PublisherCardProps) {
  return (
    <div className="flex items-center gap-3 border-b border-border-muted px-1 py-3">
      <div className="flex h-11 w-11 items-center justify-center rounded-xl bg-surface2">
        <Newspaper size={20} className="text-color2" />
      </div>
      <div className="flex-1">
        <p className="text-sm font-bold text-color">{publisher.name}</p>
        {publisher.subscriberCount !== undefined && (
          <p className="text-xs text-color3">{publisher.subscriberCount} abonnés</p>
        )}
      </div>
      <Button variant={isSubscribed ? "outline" : "default"} size="sm" onClick={onToggleSubscribe}>
        {isSubscribed ? "Abonné" : "S'abonner"}
      </Button>
    </div>
  )
}
```

- [ ] **Step 11.17: Create `src/components/SettingsRow.tsx`**

```tsx
import { ChevronRight } from "lucide-react"
import { cn } from "@/lib/utils"

interface SettingsRowProps {
  label: string
  description?: string
  value?: React.ReactNode
  onClick?: () => void
  className?: string
  rightAccessory?: React.ReactNode
}

export function SettingsRow({ label, description, value, onClick, className, rightAccessory }: SettingsRowProps) {
  const Tag = onClick ? "button" : "div"
  return (
    <Tag
      onClick={onClick}
      className={cn(
        "flex w-full items-center justify-between gap-3 border-b border-border-muted px-4 py-3 text-left",
        onClick && "hover:bg-hover cursor-pointer",
        className,
      )}
    >
      <div className="flex-1">
        <p className="text-sm font-medium text-color">{label}</p>
        {description && <p className="mt-0.5 text-xs text-color3">{description}</p>}
      </div>
      {value && <span className="text-sm text-color2">{value}</span>}
      {rightAccessory ?? (onClick && <ChevronRight size={16} className="text-color3" />)}
    </Tag>
  )
}
```

- [ ] **Step 11.18: Create `src/components/SettingsToggle.tsx`**

```tsx
import { Switch } from "@/components/ui/switch"

interface SettingsToggleProps {
  label: string
  description?: string
  checked: boolean
  onCheckedChange: (v: boolean) => void
  disabled?: boolean
}

export function SettingsToggle({ label, description, checked, onCheckedChange, disabled }: SettingsToggleProps) {
  return (
    <div className="flex items-center justify-between gap-3 border-b border-border-muted px-4 py-3">
      <div className="flex-1">
        <p className="text-sm font-medium text-color">{label}</p>
        {description && <p className="mt-0.5 text-xs text-color3">{description}</p>}
      </div>
      <Switch checked={checked} onCheckedChange={onCheckedChange} disabled={disabled} />
    </div>
  )
}
```

- [ ] **Step 11.19: Create `src/components/NotificationItem.tsx`**

```tsx
import { Trash2 } from "lucide-react"
import type { Notification } from "@/services/notification/notificationTypes"
import { timeAgo } from "@/utils/formatDate"
import { cn } from "@/lib/utils"

interface NotificationItemProps {
  notification: Notification
  onRead?: () => void
  onDelete?: () => void
}

export function NotificationItem({ notification, onRead, onDelete }: NotificationItemProps) {
  return (
    <div
      className={cn(
        "flex items-start gap-3 border-b border-border-muted px-4 py-3",
        !notification.isRead && "bg-surface",
      )}
    >
      <div className="flex-1 cursor-pointer" onClick={onRead}>
        <p className={cn("text-sm font-medium", notification.isRead ? "text-color2" : "text-color")}>
          {notification.title}
        </p>
        <p className="mt-0.5 text-sm text-color3 line-clamp-2">{notification.message}</p>
        <p className="mt-1 text-xs text-color4">{timeAgo(notification.createdAt)}</p>
      </div>
      <button onClick={onDelete} className="text-color3 hover:text-danger" aria-label="Supprimer">
        <Trash2 size={16} />
      </button>
    </div>
  )
}
```

- [ ] **Step 11.20: Create `src/components/ProfileHeader.tsx`**

```tsx
import { Avatar } from "./Avatar"
import { Button } from "@/components/ui/button"

interface ProfileHeaderProps {
  name: string
  email?: string
  bio?: string | null
  avatar?: string | null
  isCurrentUser?: boolean
  isFollowing?: boolean
  followersCount?: number
  followingCount?: number
  onEditProfile?: () => void
  onToggleFollow?: () => void
}

export function ProfileHeader({
  name,
  email,
  bio,
  avatar,
  isCurrentUser,
  isFollowing,
  followersCount,
  followingCount,
  onEditProfile,
  onToggleFollow,
}: ProfileHeaderProps) {
  return (
    <div className="flex flex-col items-center gap-4 py-6">
      <Avatar src={avatar} name={name} size={96} />
      <div className="flex flex-col items-center gap-1">
        <h1 className="text-xl font-bold text-color">{name}</h1>
        {email && <p className="text-sm text-color3">{email}</p>}
        {bio && <p className="mt-2 max-w-md text-center text-sm text-color2">{bio}</p>}
      </div>
      {(followersCount !== undefined || followingCount !== undefined) && (
        <div className="flex items-center gap-6">
          <div className="text-center">
            <p className="text-lg font-bold text-color">{followersCount ?? 0}</p>
            <p className="text-xs text-color3">Abonnés</p>
          </div>
          <div className="text-center">
            <p className="text-lg font-bold text-color">{followingCount ?? 0}</p>
            <p className="text-xs text-color3">Abonnements</p>
          </div>
        </div>
      )}
      {isCurrentUser ? (
        <Button variant="outline" onClick={onEditProfile}>
          Modifier le profil
        </Button>
      ) : (
        <Button variant={isFollowing ? "outline" : "default"} onClick={onToggleFollow}>
          {isFollowing ? "Abonné" : "Suivre"}
        </Button>
      )}
    </div>
  )
}
```

- [ ] **Step 11.21: Create `src/components/UserRow.tsx`**

```tsx
import { Avatar } from "./Avatar"

interface UserRowProps {
  user: { id: number; name: string; avatarUrl: string | null; bio: string | null }
  onPress?: () => void
  right?: React.ReactNode
}

export function UserRow({ user, onPress, right }: UserRowProps) {
  return (
    <button onClick={onPress} className="flex w-full items-center gap-3 border-b border-border-muted px-1 py-3 hover:bg-hover">
      <Avatar src={user.avatarUrl} name={user.name} size={40} />
      <div className="flex-1 text-left">
        <p className="text-sm font-bold text-color">{user.name}</p>
        {user.bio && <p className="line-clamp-1 text-xs text-color3">{user.bio}</p>}
      </div>
      {right}
    </button>
  )
}
```

- [ ] **Step 11.22: Create `src/components/CommentItem.tsx`**

```tsx
import { Edit2, Trash2 } from "lucide-react"
import { Avatar } from "./Avatar"
import { timeAgo } from "@/utils/formatDate"
import type { Comment } from "@/services/comment/commentTypes"

interface CommentItemProps {
  comment: Comment
  isOwner?: boolean
  onEdit?: () => void
  onDelete?: () => void
}

export function CommentItem({ comment, isOwner, onEdit, onDelete }: CommentItemProps) {
  return (
    <div className="flex gap-3 border-b border-border-muted py-3">
      <Avatar src={comment.user.avatarUrl} name={comment.user.name} size={32} />
      <div className="flex-1">
        <div className="flex items-center gap-2">
          <p className="text-sm font-bold text-color">{comment.user.name}</p>
          <span className="text-xs text-color3">{timeAgo(comment.createdAt)}</span>
        </div>
        <p className="mt-1 text-sm text-color leading-relaxed whitespace-pre-wrap">{comment.content}</p>
        {isOwner && (
          <div className="mt-1 flex gap-3">
            <button onClick={onEdit} className="text-xs text-color3 hover:text-color">
              <Edit2 size={12} className="inline mr-1" />
              Modifier
            </button>
            <button onClick={onDelete} className="text-xs text-color3 hover:text-danger">
              <Trash2 size={12} className="inline mr-1" />
              Supprimer
            </button>
          </div>
        )}
      </div>
    </div>
  )
}
```

- [ ] **Step 11.23: Create `src/components/CommentInput.tsx`**

```tsx
import { useState } from "react"
import { Send } from "lucide-react"
import { Input } from "@/components/ui/input"
import { Button } from "@/components/ui/button"

interface CommentInputProps {
  onSubmit: (content: string) => void | Promise<void>
  placeholder?: string
}

export function CommentInput({ onSubmit, placeholder = "Écrire un commentaire…" }: CommentInputProps) {
  const [value, setValue] = useState("")
  const [submitting, setSubmitting] = useState(false)

  const handleSubmit = async () => {
    const trimmed = value.trim()
    if (!trimmed || submitting) return
    setSubmitting(true)
    try {
      await onSubmit(trimmed)
      setValue("")
    } finally {
      setSubmitting(false)
    }
  }

  return (
    <div className="flex items-center gap-2">
      <Input
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder={placeholder}
        onKeyDown={(e) => {
          if (e.key === "Enter" && !e.shiftKey) {
            e.preventDefault()
            void handleSubmit()
          }
        }}
      />
      <Button onClick={() => void handleSubmit()} disabled={!value.trim() || submitting} size="icon">
        <Send size={16} />
      </Button>
    </div>
  )
}
```

- [ ] **Step 11.24: Create `src/components/ThemeCard.tsx`**

```tsx
import { Check } from "lucide-react"
import { cn } from "@/lib/utils"

interface ThemeCardProps {
  label: string
  value: "light" | "dark" | "system"
  active: boolean
  onClick: () => void
}

export function ThemeCard({ label, value, active, onClick }: ThemeCardProps) {
  const preview =
    value === "light"
      ? "bg-white border-neutral-200"
      : value === "dark"
        ? "bg-black border-neutral-800"
        : "bg-gradient-to-br from-white to-black border-neutral-400"

  return (
    <button
      onClick={onClick}
      className={cn(
        "flex flex-col items-center gap-2 rounded-lg border-2 p-3 transition-colors",
        active ? "border-color" : "border-border bg-surface hover:bg-hover",
      )}
    >
      <div className={cn("h-16 w-24 rounded-md border", preview)} />
      <span className="flex items-center gap-1 text-sm font-medium text-color">
        {active && <Check size={14} className="text-success" />}
        {label}
      </span>
    </button>
  )
}
```

- [ ] **Step 11.25: Create `src/components/index.ts` for re-exports**

```ts
export { ArticleCard } from "./ArticleCard"
export { Avatar } from "./Avatar"
export { AutoImage } from "./AutoImage"
export { Badge } from "./Badge"
export { CommentInput } from "./CommentInput"
export { CommentItem } from "./CommentItem"
export { EmptyState } from "./EmptyState"
export { IconButton } from "./IconButton"
export { NotificationItem } from "./NotificationItem"
export { ProfileHeader } from "./ProfileHeader"
export { ProtectedRoute, GuestRoute } from "./ProtectedRoute"
export { PublisherCard } from "./PublisherCard"
export { SearchBar } from "./SearchBar"
export { SectionHeader } from "./SectionHeader"
export { SettingsRow } from "./SettingsRow"
export { SettingsToggle } from "./SettingsToggle"
export { Skeleton, ArticleCardSkeleton } from "./Skeleton"
export { TabSelector } from "./TabSelector"
export { ThemeCard } from "./ThemeCard"
export { Toast } from "./Toast"
export { TrustBadge } from "./TrustBadge"
export { TrustDetailSheet } from "./TrustDetailSheet"
export { UserRow } from "./UserRow"
```

- [ ] **Step 11.26: Typecheck + lint**

```bash
pnpm typecheck && pnpm lint
```

Expected: 0 errors.

- [ ] **Step 11.27: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): 22 components ported from mobile (shadcn/Tailwind render)"
```

---

## Phase 12 — Auth screens (4)

**Files:** `src/routes/(auth)/{login,register,forgot-password,reset-password}.tsx`

- [ ] **Step 12.1: Install Google OAuth**

```bash
pnpm add @react-oauth/google@^0.13
```

- [ ] **Step 12.2: Replace `src/routes/(auth)/login.tsx`**

```tsx
import { useState } from "react"
import { Link, useNavigate, useLocation } from "react-router-dom"
import { useTranslation } from "react-i18next"
import { GoogleOAuthProvider, GoogleLogin } from "@react-oauth/google"
import { useAuth } from "@/contexts/AuthContext"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Toast } from "@/components"
import { GOOGLE_CONFIG } from "@/config/google"

export default function LoginPage() {
  const { t } = useTranslation()
  const { login, loginWithGoogle } = useAuth()
  const navigate = useNavigate()
  const location = useLocation()
  const [email, setEmail] = useState("")
  const [password, setPassword] = useState("")
  const [submitting, setSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)
  const redirectTo = (location.state as { from?: { pathname?: string } } | null)?.from?.pathname ?? "/home"

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setSubmitting(true)
    const res = await login({ email, password })
    setSubmitting(false)
    if (res.error) setError(res.error)
    else navigate(redirectTo, { replace: true })
  }

  return (
    <div className="w-full max-w-sm space-y-6">
      <div className="text-center">
        <h1 className="text-2xl font-bold text-color">{t("auth.login")}</h1>
        <p className="mt-1 text-sm text-color3">Syntheza — votre veille intelligente</p>
      </div>
      <form onSubmit={handleSubmit} className="space-y-4">
        <div className="space-y-2">
          <Label htmlFor="email">{t("auth.email")}</Label>
          <Input
            id="email"
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            autoComplete="email"
            required
          />
        </div>
        <div className="space-y-2">
          <Label htmlFor="password">{t("auth.password")}</Label>
          <Input
            id="password"
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            autoComplete="current-password"
            required
          />
        </div>
        <Button type="submit" className="w-full" disabled={submitting}>
          {submitting ? t("common.loading") : t("auth.login")}
        </Button>
      </form>

      {GOOGLE_CONFIG.webClientId && (
        <GoogleOAuthProvider clientId={GOOGLE_CONFIG.webClientId}>
          <div className="flex justify-center">
            <GoogleLogin
              onSuccess={async (cred) => {
                if (cred.credential) {
                  const res = await loginWithGoogle(cred.credential)
                  if (res.error) setError(res.error)
                  else navigate(redirectTo, { replace: true })
                }
              }}
              onError={() => setError("Google login failed")}
            />
          </div>
        </GoogleOAuthProvider>
      )}

      <div className="flex flex-col items-center gap-2 text-sm">
        <Link to="/forgot-password" className="text-link hover:text-color">
          {t("auth.forgotPassword")}
        </Link>
        <Link to="/register" className="text-link hover:text-color">
          {t("auth.register")}
        </Link>
      </div>

      <Toast message={error ?? ""} visible={!!error} onDismiss={() => setError(null)} />
    </div>
  )
}
```

- [ ] **Step 12.3: Replace `src/routes/(auth)/register.tsx`**

```tsx
import { useState } from "react"
import { Link, useNavigate } from "react-router-dom"
import { useTranslation } from "react-i18next"
import { useAuth } from "@/contexts/AuthContext"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Toast } from "@/components"

export default function RegisterPage() {
  const { t } = useTranslation()
  const { register } = useAuth()
  const navigate = useNavigate()
  const [name, setName] = useState("")
  const [email, setEmail] = useState("")
  const [password, setPassword] = useState("")
  const [submitting, setSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setSubmitting(true)
    const res = await register({ name, email, password })
    setSubmitting(false)
    if (res.error) setError(res.error)
    else navigate("/home", { replace: true })
  }

  return (
    <div className="w-full max-w-sm space-y-6">
      <div className="text-center">
        <h1 className="text-2xl font-bold text-color">{t("auth.register")}</h1>
      </div>
      <form onSubmit={handleSubmit} className="space-y-4">
        <div className="space-y-2">
          <Label htmlFor="name">{t("auth.name")}</Label>
          <Input id="name" value={name} onChange={(e) => setName(e.target.value)} required />
        </div>
        <div className="space-y-2">
          <Label htmlFor="email">{t("auth.email")}</Label>
          <Input
            id="email"
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            autoComplete="email"
            required
          />
        </div>
        <div className="space-y-2">
          <Label htmlFor="password">{t("auth.password")}</Label>
          <Input
            id="password"
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            autoComplete="new-password"
            required
            minLength={8}
          />
        </div>
        <Button type="submit" className="w-full" disabled={submitting}>
          {submitting ? t("common.loading") : t("auth.register")}
        </Button>
      </form>
      <p className="text-center text-sm">
        <Link to="/login" className="text-link hover:text-color">
          {t("auth.login")}
        </Link>
      </p>
      <Toast message={error ?? ""} visible={!!error} onDismiss={() => setError(null)} />
    </div>
  )
}
```

- [ ] **Step 12.4: Replace `src/routes/(auth)/forgot-password.tsx`**

```tsx
import { useState } from "react"
import { Link } from "react-router-dom"
import { useTranslation } from "react-i18next"
import { useAuth } from "@/contexts/AuthContext"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Toast } from "@/components"

export default function ForgotPasswordPage() {
  const { t } = useTranslation()
  const { forgotPassword } = useAuth()
  const [email, setEmail] = useState("")
  const [submitting, setSubmitting] = useState(false)
  const [msg, setMsg] = useState<{ kind: "ok" | "error"; text: string } | null>(null)

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setSubmitting(true)
    const res = await forgotPassword({ email })
    setSubmitting(false)
    if (res.error) setMsg({ kind: "error", text: res.error })
    else setMsg({ kind: "ok", text: t("auth.emailSent") })
  }

  return (
    <div className="w-full max-w-sm space-y-6">
      <h1 className="text-center text-2xl font-bold text-color">{t("auth.forgotPassword")}</h1>
      <form onSubmit={handleSubmit} className="space-y-4">
        <div className="space-y-2">
          <Label htmlFor="email">{t("auth.email")}</Label>
          <Input id="email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} required />
        </div>
        <Button type="submit" className="w-full" disabled={submitting}>
          {submitting ? t("common.loading") : t("common.confirm")}
        </Button>
      </form>
      <p className="text-center text-sm">
        <Link to="/login" className="text-link hover:text-color">
          ← {t("auth.login")}
        </Link>
      </p>
      <Toast
        message={msg?.text ?? ""}
        type={msg?.kind === "ok" ? "success" : "error"}
        visible={!!msg}
        onDismiss={() => setMsg(null)}
      />
    </div>
  )
}
```

- [ ] **Step 12.5: Replace `src/routes/(auth)/reset-password.tsx`**

```tsx
import { useState } from "react"
import { useNavigate, useSearchParams } from "react-router-dom"
import { useTranslation } from "react-i18next"
import { authService } from "@/services/auth/authService"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Toast } from "@/components"

export default function ResetPasswordPage() {
  const { t } = useTranslation()
  const navigate = useNavigate()
  const [search] = useSearchParams()
  const token = search.get("token") ?? ""
  const [password, setPassword] = useState("")
  const [submitting, setSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    if (!token) {
      setError("Token manquant dans l'URL")
      return
    }
    setSubmitting(true)
    const res = await authService.resetPassword({ token, password })
    setSubmitting(false)
    if (res.kind === "error") setError(res.message)
    else navigate("/login", { replace: true })
  }

  return (
    <div className="w-full max-w-sm space-y-6">
      <h1 className="text-center text-2xl font-bold text-color">{t("auth.resetPassword")}</h1>
      <form onSubmit={handleSubmit} className="space-y-4">
        <div className="space-y-2">
          <Label htmlFor="password">{t("auth.password")}</Label>
          <Input
            id="password"
            type="password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            autoComplete="new-password"
            minLength={8}
            required
          />
        </div>
        <Button type="submit" className="w-full" disabled={submitting}>
          {submitting ? t("common.loading") : t("common.confirm")}
        </Button>
      </form>
      <Toast message={error ?? ""} visible={!!error} onDismiss={() => setError(null)} />
    </div>
  )
}
```

- [ ] **Step 12.6: Verify auth flow in browser**

```bash
pnpm dev
```

Manual test:
1. Visit `/login` → form renders
2. Submit with `dev@syntheza.app` / `password123` (from seed) → should redirect to `/home`
3. Logout via tokenService clearing in DevTools → reload → back to `/login`

Expected: login works against live API.

- [ ] **Step 12.7: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): auth screens (login, register, forgot-password, reset-password) + Google OAuth"
```

---

## Phase 13 — App layout + sidebar/tabs + dark mode toggle

**Files:**
- Create: `src/hooks/useTheme.ts`
- Replace: `src/routes/(app)/layout.tsx`

- [ ] **Step 13.1: Create `src/hooks/useTheme.ts`**

```ts
import { useEffect, useState } from "react"
import usePreferencesStore from "@/stores/preferencesStore"

export type ThemeMode = "light" | "dark" | "system"

function resolveTheme(mode: ThemeMode): "light" | "dark" {
  if (mode === "system") {
    return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light"
  }
  return mode
}

export function useTheme() {
  const prefs = usePreferencesStore((s) => s.preferences)
  const updatePref = usePreferencesStore((s) => s.updatePreference)
  const [resolved, setResolved] = useState<"light" | "dark">("light")
  const mode = (prefs?.theme as ThemeMode | undefined) ?? "system"

  useEffect(() => {
    const apply = () => {
      const r = resolveTheme(mode)
      setResolved(r)
      document.documentElement.classList.toggle("dark", r === "dark")
    }
    apply()
    if (mode === "system") {
      const mq = window.matchMedia("(prefers-color-scheme: dark)")
      mq.addEventListener("change", apply)
      return () => mq.removeEventListener("change", apply)
    }
  }, [mode])

  const setTheme = (newMode: ThemeMode) => updatePref("theme", newMode)

  return { mode, resolved, setTheme }
}
```

- [ ] **Step 13.2: Replace `src/routes/(app)/layout.tsx` with sidebar + topbar + dark mode**

```tsx
import { useEffect } from "react"
import { Link, NavLink, Outlet, useNavigate } from "react-router-dom"
import { useQuery } from "@tanstack/react-query"
import {
  Home,
  Compass,
  Search,
  Bell,
  User,
  Settings as SettingsIcon,
  LogOut,
  Moon,
  Sun,
} from "lucide-react"
import { useAuth } from "@/contexts/AuthContext"
import { useTheme } from "@/hooks/useTheme"
import { notificationService } from "@/services/notification/notificationService"
import usePreferencesStore from "@/stores/preferencesStore"
import { queryKeys } from "@/hooks/queryKeys"
import { Avatar } from "@/components/Avatar"
import { IconButton } from "@/components/IconButton"
import { cn } from "@/lib/utils"

const NAV_ITEMS = [
  { to: "/home", label: "Accueil", icon: Home },
  { to: "/discover", label: "Découvrir", icon: Compass },
  { to: "/search", label: "Recherche", icon: Search },
  { to: "/notifications", label: "Notifications", icon: Bell },
  { to: "/profile", label: "Profil", icon: User },
] as const

export default function AppLayout() {
  const { user, logout, isAuthenticated } = useAuth()
  const { resolved, setTheme } = useTheme()
  const fetchPrefs = usePreferencesStore((s) => s.fetchPreferences)
  const navigate = useNavigate()

  useEffect(() => {
    if (isAuthenticated) void fetchPrefs()
  }, [isAuthenticated, fetchPrefs])

  const unreadQuery = useQuery({
    queryKey: queryKeys.notifications.unreadCount,
    queryFn: async () => {
      const r = await notificationService.getUnreadCount()
      if (r.kind === "ok") return r.data.count
      return 0
    },
    refetchInterval: 60_000,
  })
  const unread = unreadQuery.data ?? 0

  return (
    <div className="min-h-screen bg-bg">
      <div className="mx-auto flex max-w-6xl">
        <aside className="sticky top-0 hidden h-screen w-60 shrink-0 flex-col border-r border-border-muted px-3 py-6 md:flex">
          <Link to="/home" className="mb-6 px-3 text-xl font-bold tracking-tight text-color">
            Syntheza
          </Link>
          <nav className="flex flex-col gap-1">
            {NAV_ITEMS.map(({ to, label, icon: Icon }) => (
              <NavLink
                key={to}
                to={to}
                className={({ isActive }) =>
                  cn(
                    "flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium",
                    isActive ? "bg-surface text-color" : "text-color2 hover:bg-hover hover:text-color",
                  )
                }
              >
                <Icon size={18} />
                <span className="flex-1">{label}</span>
                {to === "/notifications" && unread > 0 && (
                  <span className="rounded-full bg-danger px-2 py-0.5 text-xs font-bold text-white">
                    {unread}
                  </span>
                )}
              </NavLink>
            ))}
          </nav>

          <div className="mt-auto flex flex-col gap-1">
            <NavLink
              to="/settings/account"
              className={({ isActive }) =>
                cn(
                  "flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium",
                  isActive ? "bg-surface text-color" : "text-color2 hover:bg-hover hover:text-color",
                )
              }
            >
              <SettingsIcon size={18} />
              Paramètres
            </NavLink>

            <button
              onClick={() => setTheme(resolved === "dark" ? "light" : "dark")}
              className="flex items-center gap-3 rounded-md px-3 py-2 text-sm font-medium text-color2 hover:bg-hover hover:text-color"
              aria-label="Changer de thème"
            >
              {resolved === "dark" ? <Sun size={18} /> : <Moon size={18} />}
              {resolved === "dark" ? "Mode clair" : "Mode sombre"}
            </button>

            {user && (
              <div className="mt-2 flex items-center gap-3 rounded-md px-3 py-2">
                <Avatar src={user.avatar} name={user.name} size={32} />
                <div className="flex-1 overflow-hidden">
                  <p className="truncate text-sm font-medium text-color">{user.name}</p>
                  <p className="truncate text-xs text-color3">{user.email}</p>
                </div>
                <IconButton onClick={() => void logout().then(() => navigate("/login"))} aria-label="Déconnexion">
                  <LogOut size={16} />
                </IconButton>
              </div>
            )}
          </div>
        </aside>

        <main className="min-h-screen flex-1 px-4 pb-24 pt-6 md:px-8">
          <Outlet />
        </main>
      </div>

      {/* mobile bottom tab bar */}
      <nav className="fixed bottom-0 left-0 right-0 z-40 flex border-t border-border-muted bg-bg md:hidden">
        {NAV_ITEMS.map(({ to, label, icon: Icon }) => (
          <NavLink
            key={to}
            to={to}
            className={({ isActive }) =>
              cn(
                "flex flex-1 flex-col items-center gap-0.5 py-2 text-xs",
                isActive ? "text-color" : "text-color3",
              )
            }
          >
            <Icon size={20} />
            {label}
          </NavLink>
        ))}
      </nav>
    </div>
  )
}
```

- [ ] **Step 13.3: Add remaining route placeholders so layout renders all NavLinks**

`src/routes/(app)/discover.tsx`:

```tsx
export default function DiscoverPage() {
  return <p>Discover (Phase 15)</p>
}
```

`src/routes/(app)/search.tsx`:

```tsx
export default function SearchPage() {
  return <p>Search (Phase 16)</p>
}
```

`src/routes/(app)/notifications.tsx`:

```tsx
export default function NotificationsPage() {
  return <p>Notifications (Phase 17)</p>
}
```

`src/routes/(app)/profile.tsx`:

```tsx
export default function ProfilePage() {
  return <p>Profile (Phase 18)</p>
}
```

- [ ] **Step 13.4: Update `src/routes/root.tsx` to register the new routes**

Replace the `Routes` block inside `Root`:

```tsx
<Routes>
  <Route element={<GuestRoute><AuthLayout /></GuestRoute>}>
    <Route path="/login" element={<LoginPage />} />
    <Route path="/register" element={<RegisterPage />} />
    <Route path="/forgot-password" element={<ForgotPasswordPage />} />
    <Route path="/reset-password" element={<ResetPasswordPage />} />
  </Route>

  <Route element={<ProtectedRoute><AppLayout /></ProtectedRoute>}>
    <Route path="/home" element={<HomePage />} />
    <Route path="/discover" element={<DiscoverPage />} />
    <Route path="/search" element={<SearchPage />} />
    <Route path="/notifications" element={<NotificationsPage />} />
    <Route path="/profile" element={<ProfilePage />} />
  </Route>

  <Route path="/" element={<Navigate to="/home" replace />} />
  <Route path="*" element={<Navigate to="/home" replace />} />
</Routes>
```

And add imports for the new pages at the top of `root.tsx`:

```tsx
import DiscoverPage from "./(app)/discover"
import SearchPage from "./(app)/search"
import NotificationsPage from "./(app)/notifications"
import ProfilePage from "./(app)/profile"
```

- [ ] **Step 13.5: Verify layout**

```bash
pnpm dev
```

Manual: login, verify sidebar nav, dark mode toggle works, notif unread badge fetches.

- [ ] **Step 13.6: Commit**

```bash
git add -A
git commit -m "feat(rebuild-v2): app layout + sidebar + bottom tabs + dark mode toggle + notif badge"
```

---

(continued in Part 3 — Phases 14-23)

See: `2026-05-20-frontend-web-rebuild-v2-part3.md`
