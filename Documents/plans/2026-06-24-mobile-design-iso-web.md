# Mobile ↔ Web Charte Iso — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Aligner visuellement l'app mobile (`frontend-mobile`, RN/Expo/Tamagui) sur la charte du web (`frontend-web`, React/Ant/Tailwind) — même palette, typo, radius, composants stylistiques et hiérarchie d'info — en gardant un layout adapté au tactile, hors back office (web-only).

**Architecture :** Travail en 3 couches dépendantes (tokens → composants atomiques → écrans), précédé d'un nettoyage git et de la clôture du chantier publisher/channel, suivi d'une validation visuelle unique sur simulateur Apple. La validation par couche est **technique** (`pnpm compile` + `pnpm test` verts) ; la validation **visuelle** est groupée à la fin (décision user). Le web est la référence ; on lit son code (JSX + classes Tailwind) pour reconstruire l'équivalent mobile.

**Tech Stack :** React Native 0.81, Expo SDK 54, Expo Router 6, Tamagui, Zustand, pnpm 10.11. Police Space Grotesk (self-hosted via `@expo-google-fonts/space-grotesk`, identique au web `@fontsource/space-grotesk`).

---

## Contexte & décisions actées (grill-me du 2026-06-24)

| Branche de décision | Décision |
|---|---|
| **Scope** | Parité visuelle complète : même charte + composants stylistiques + hiérarchie, **layout adapté tactile**. Back office (`admin/*`) exclu (web-only). |
| **Référence** | Lecture du code web (JSX + Tailwind), écran par écran. Pas de screenshots. |
| **Couleurs** | `primary` inversé web (dark = blanc `#f5f5f5` / light = noir `#262626`) ; `color4` + `link` (light) → valeurs web ; le reste déjà iso. |
| **Radius** | Échelle web : 6 / 8 / 12 / 16 / 22 px. |
| **Typo** | Space Grotesk déjà identique (même source, poids 300→700). **800 impossible** (la police n'existe pas en 800 ; le web rend son `font-weight:800` en ~700 synthétisé). Créer un style `wordmark` mobile (700 + letter-spacing serré). Auditer les `<Text>` RN natifs qui tombent en police système. |
| **Composants** | 22 fichiers homonymes des 2 côtés. **⚠️ Le nom ne garantit pas l'équivalence** (`Badge` web = label pill, `Badge` mobile = compteur notif). Audit sémantique web/mobile obligatoire par composant. |
| **Séquence** | Lot 0 git → Lot 1 tokens → Lot 2 composants → Lot 3 écrans → Lot 4 police/wordmark → Lot 5 validation+merge. Commits atomiques. |
| **Git** | Virer le dead code untracked → clôturer `feat/publisher-channel-mobile-adapt` → brancher `feat/mobile-design-iso-web` → tout valider à la fin → merge sur main + suppression des 2 branches. |
| **Validation** | Simulateur Apple puis iPhone, en une passe finale. Pas de validation device intermédiaire. |
| **Hors scope (statu quo)** | Écrans `followers`/`following` (mobile-only) : on aligne leur style, on ne les supprime pas. `reset-password` reste web-only. Pas de nouveaux tests unitaires visuels. |

### Commandes de vérification (mobile, à la racine `frontend-mobile/`)
- TypeScript : `pnpm compile` → attendu : `0 erreur`
- Tests : `pnpm test` → attendu : **82 tests verts** (baseline)
- Lint : `pnpm lint:check` → attendu : pas de **nouvelle** erreur vs baseline
- Simulateur (Lot 5) : `pnpm ios` (ou `pnpm start` puis `i`)

### Cartographie (établie pendant l'exploration)
- **Écrans communs (16)** : home/`(tabs)/index`, discover, search, notifications, profile, article/[id], user/[id], settings/{account, appearance, data-privacy, notifications, privacy, security, index}, auth/{login, register, forgot-password}.
- **Web-only** : tout `admin/*` (exclu), `reset-password`.
- **Mobile-only** : `user/[id]/followers`, `user/[id]/following`.
- **Composants atomiques homonymes (22)** : ArticleCard, AutoImage, Avatar, Badge, CommentInput, CommentItem, EmptyState, IconButton, NotificationItem, ProfileHeader, PublisherCard, SearchBar, SectionHeader, SettingsRow, SettingsToggle, Skeleton, TabSelector, ThemeCard, Toast, TrustBadge, TrustDetailSheet, UserRow.

---

## File Structure

**Couche tokens (source de vérité couleurs vivante = `colors.ts` via `useColors`, 40 consommateurs) :**
- `frontend-mobile/src/design/colors.ts` — MODIF : `primary*` (dark+light), `color4`+`link` (light).
- `frontend-mobile/src/design/tokens.ts` — MODIF : `color.primary*`, échelle `radius`.
- `frontend-mobile/src/design/typography.ts` — MODIF : ajout export `wordmark`.
- `frontend-web/src/design/colors.ts` — SUPPRESSION (dead code, importé nulle part ; source web réelle = `globals.css`).

**Couche composants :** les 22 fichiers `frontend-mobile/src/components/*.tsx` (référence = `frontend-web/src/components/*.tsx`).

**Couche écrans :** les 16 fichiers `frontend-mobile/src/app/**/*.tsx` (référence = `frontend-web/src/routes/(app|auth)/**/*.tsx`).

**Nettoyage :**
- `frontend-mobile/src/utils/useHeader.tsx` — SUPPRESSION (untracked, cassé : importe `@/components/Header` inexistant, utilisé nulle part).
- `frontend-mobile/src/components/ThemeCard.tsx` — voir Tâche 0.1 (untracked, non importé aujourd'hui mais requis pour l'écran `appearance` au Lot 3 → on le garde et on le câblera, on ne le supprime pas).

---

## Lot 0 — Préliminaires git

### Tâche 0.1 : Nettoyer le dead code untracked

**Files:**
- Delete: `frontend-mobile/src/utils/useHeader.tsx`

- [ ] **Step 1 : Confirmer que `useHeader` est mort et cassé**

Run: `cd frontend-mobile && grep -rln "useHeader" src --include="*.tsx" --include="*.ts" | grep -v "utils/useHeader.tsx"; ls src/components/Header.tsx 2>/dev/null || echo "Header ABSENT"`
Expected: aucune occurrence d'import + `Header ABSENT`.

- [ ] **Step 2 : Supprimer le fichier**

Run: `rm frontend-mobile/src/utils/useHeader.tsx`

- [ ] **Step 3 : Statuer sur `ThemeCard.tsx`**

`ThemeCard.tsx` est untracked et non importé aujourd'hui, MAIS le web a un `ThemeCard` utilisé par `settings/appearance`. On le **garde** (il sera câblé au Lot 3, Tâche écran `appearance`). Ne pas le supprimer. Le commit qui le réintègre se fera au moment où l'écran `appearance` l'utilise.

- [ ] **Step 4 : Vérifier compile + tests**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
Expected: 0 erreur TS, 82 tests verts.

### Tâche 0.2 : Clôturer publisher/channel et créer la branche de travail

> **Note :** la validation device de publisher/channel se fera dans la passe finale (Lot 5) en même temps que la charte. On ne merge donc PAS publisher/channel maintenant ; on branche par-dessus et on mergera l'ensemble à la fin (décision user : « nouvelle branche, on clôture publisher dans ce chantier, tout clean à la fin »).

**Files:** aucun fichier source.

- [ ] **Step 1 : Vérifier l'état de la branche actuelle**

Run: `cd frontend-mobile && git branch --show-current && git status -sb`
Expected: `feat/publisher-channel-mobile-adapt`, working tree propre après Tâche 0.1 (plus de `useHeader.tsx` ; `ThemeCard.tsx` peut rester untracked jusqu'au Lot 3).

- [ ] **Step 2 : Créer la branche charte depuis la branche actuelle**

Run: `cd frontend-mobile && git checkout -b feat/mobile-design-iso-web`
Expected: `Switched to a new branch 'feat/mobile-design-iso-web'`.

- [ ] **Step 3 : Commit du nettoyage**

```bash
cd frontend-mobile
git add -A
git commit -m "chore(mobile): remove dead useHeader util before design pass"
```

---

## Lot 1 — Tokens (couche de base, se propage aux 40 consommateurs de `useColors`)

### Tâche 1.1 : Aligner les couleurs `primary` + neutres light sur le web

**Files:**
- Modify: `frontend-mobile/src/design/colors.ts`

- [ ] **Step 1 : Mettre à jour `darkColors` (seul `primary*` change ; le reste est déjà iso)**

Dans `darkColors`, remplacer :
```ts
  primary: "#3a3a3a",
  primaryHover: "#4a4a4a",
  primaryText: "#ffffff",
```
par :
```ts
  primary: "#f5f5f5",
  primaryHover: "#ffffff",
  primaryText: "#000000",
```

- [ ] **Step 2 : Mettre à jour `lightColors` (`primary*`, `color4`, `link`)**

Dans `lightColors`, remplacer :
```ts
  color4: "#8a8a8a",
  placeholder: "#a8a8a8",
  link: "#737373",
  linkDark: "#525252",
  primary: "#e8e8e8",
  primaryHover: "#d4d4d4",
  primaryText: "#262626",
```
par :
```ts
  color4: "#a8a8a8",
  placeholder: "#a8a8a8",
  link: "#262626",
  linkDark: "#525252",
  primary: "#262626",
  primaryHover: "#404040",
  primaryText: "#ffffff",
```

> Référence web : `frontend-web/src/globals.css` — `.dark { --primary:#f5f5f5; --primary-hover:#ffffff; --primary-text:#000000 }` et `:root { --primary:#262626; --primary-hover:#404040; --primary-text:#ffffff; --color4:#a8a8a8; --link:#262626 }`.

- [ ] **Step 3 : Vérifier compile + tests**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
Expected: 0 erreur TS, 82 tests verts.

- [ ] **Step 4 : Commit**

```bash
cd frontend-mobile
git add src/design/colors.ts
git commit -m "feat(mobile): align primary + light neutrals on web charter"
```

### Tâche 1.2 : Aligner l'échelle `radius` et le `primary` Tamagui

**Files:**
- Modify: `frontend-mobile/src/design/tokens.ts`

> `tokens.ts`/`themes.ts` (Tamagui) sont quasi morts pour les couleurs (1 usage de thème vs 40 de `useColors`). On aligne `primary` + `radius` pour cohérence, **sans refondre** le système de thème Tamagui (dette connue, hors scope).

- [ ] **Step 1 : Aligner le `primary` Tamagui sur le web (valeur dark, mode par défaut)**

Dans `tokens.ts`, bloc `color`, remplacer :
```ts
    primary: "#3a3a3a",
    primaryHover: "#4a4a4a",
    primaryLight: "#e8e8e8",
    primaryLightHover: "#d4d4d4",
```
par :
```ts
    primary: "#f5f5f5",
    primaryHover: "#ffffff",
    primaryLight: "#262626",
    primaryLightHover: "#404040",
```

- [ ] **Step 2 : Remplacer l'échelle `radius` par celle du web (6/8/12/16/22)**

Remplacer le bloc `radius` :
```ts
  radius: {
    0: 0,
    1: 10,
    2: 14,
    3: 18,
    4: 22,
    5: 28,
    6: 32,
  },
```
par :
```ts
  radius: {
    0: 0,
    1: 6,   // sm
    2: 8,   // md
    3: 12,  // lg
    4: 16,  // xl
    5: 22,  // 2xl
  },
```

> Conséquence : tout composant utilisant `$radius.6` doit être ramené à `$radius.5` lors de son passage au Lot 2. Les `borderRadius` **en dur** (ex. `borderRadius: 14`) sont corrigés composant par composant au Lot 2 selon l'équivalent web (`rounded-lg`→12, `rounded-xl`→16, etc.).

- [ ] **Step 3 : Détecter les usages de `$radius.6` à corriger au Lot 2**

Run: `cd frontend-mobile && grep -rn "radius.6\|radius={6}\|\\\$radius\\.6" src --include="*.tsx" || echo "aucun usage de radius.6"`
Expected: liste (potentiellement vide) à reporter dans les tâches composants concernées.

- [ ] **Step 4 : Vérifier compile + tests**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
Expected: 0 erreur TS, 82 tests verts.

- [ ] **Step 5 : Commit**

```bash
cd frontend-mobile
git add src/design/tokens.ts
git commit -m "feat(mobile): align radius scale + tamagui primary on web"
```

### Tâche 1.3 : Ajouter le style `wordmark`

**Files:**
- Modify: `frontend-mobile/src/design/typography.ts`

> Web : utility `.wordmark { font-weight:800; letter-spacing:-0.04em }`, + eyebrows `uppercase tracking-[0.18em→0.22em]`. Le 800 réel n'existe pas → on plafonne à 700. Le `letter-spacing` web est en `em` (proportionnel) ; en RN c'est en points absolus → on expose un helper qui calcule à partir de la taille.

- [ ] **Step 1 : Ajouter l'export `wordmark` en bas de `typography.ts`**

```ts
/**
 * Réplique l'identité éditoriale "wordmark" du web (titres serrés extra-bold).
 * Space Grotesk n'existe pas en 800 → 700 est le max (= ce que le web rend réellement).
 * letterSpacing web -0.04em → en RN on passe par un point absolu proportionnel à la taille.
 */
export const wordmarkTitle = (fontSize: number) => ({
  fontFamily: "SpaceGrotesk_700Bold" as const,
  fontWeight: "700" as const,
  letterSpacing: Math.round(-0.02 * fontSize * 100) / 100,
})

export const wordmarkEyebrow = {
  fontFamily: "SpaceGrotesk_700Bold" as const,
  fontWeight: "700" as const,
  textTransform: "uppercase" as const,
  letterSpacing: 2,
}
```

- [ ] **Step 2 : Vérifier compile + tests**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
Expected: 0 erreur TS, 82 tests verts.

- [ ] **Step 3 : Commit**

```bash
cd frontend-mobile
git add src/design/typography.ts
git commit -m "feat(mobile): add wordmark title/eyebrow typography helpers"
```

---

## Lot 2 — Composants atomiques (recipe + worked example)

### Recipe par composant (à appliquer aux 22)

Pour **chaque** composant, dans cet ordre :

1. **Audit sémantique** : lire `frontend-web/src/components/<Name>.tsx` ET `frontend-mobile/src/components/<Name>.tsx`. Décider lequel des 3 cas :
   - **(a) Même rôle, style à aligner** → reconstruire la structure/hiérarchie web en Tamagui/RN, en gardant les props mobile existantes (ne pas casser les consommateurs).
   - **(b) Même nom, rôle différent** (ex. `Badge`) → **ne pas fusionner**. Garder le composant mobile tel quel, aligner uniquement ses couleurs/radius sur les tokens. Noter la divergence dans le commit.
   - **(c) Web-only / mobile-only** → laisser.
2. **Appliquer** : tokens via `useColors()` (jamais de hex en dur), radius via les valeurs web (`rounded-sm/md/lg/xl/2xl` → 6/8/12/16/22), typo via `wordmarkTitle/Eyebrow` pour les titres.
3. **Vérifier** : `pnpm compile && pnpm test` (verts).
4. **Commit atomique** : `style(mobile): align <Name> on web charter`.

> Les icônes restent **Ionicons** côté mobile (équivalents : lucide `Heart`→`heart`/`heart-outline`, `MessageCircle`→`chatbubble-outline`, `Share2`→`share-outline`, `Bookmark`→`bookmark`/`bookmark-outline`, `MoreHorizontal`→`ellipsis-horizontal`). On ne migre pas vers lucide.

### Ordre conseillé (atomes → composites)
Avatar, Skeleton, Badge, IconButton, SectionHeader, TabSelector, EmptyState, Toast, TrustBadge → SearchBar, SettingsRow, SettingsToggle, ThemeCard, UserRow, CommentInput, CommentItem, NotificationItem, PublisherCard, AutoImage, ProfileHeader, TrustDetailSheet → **ArticleCard** (le plus composite, en dernier).

### Audit déjà réalisé (à intégrer)
- **`Badge`** = cas (b). Web = label pill (`variant` default/danger/success/warning). Mobile = compteur de notif numérique. **Ne pas fusionner.** Aligner seulement : `backgroundColor: c.danger` (déjà OK), garder. Aucun changement requis sauf si un usage web-like apparaît.
- **`ArticleCard`** = cas (a), divergence forte → worked example ci-dessous.

### Tâche 2.X (worked example) : ArticleCard

**Files:**
- Modify: `frontend-mobile/src/components/ArticleCard.tsx`
- Test: `frontend-mobile/src/components/ArticleCard.test.tsx` (si présent — sinon s'appuyer sur la suite jest globale)

**Cible (lue dans `frontend-web/src/components/ArticleCard.tsx`)** : carte feed type réseau social —
header (avatar gradient 32, nom 14 semibold + TrustBadge, ligne 2 = timeAgo 11 + author, bouton `MoreHorizontal`) → image pleine largeur (si `imageUrl`) → titre 17 bold + summary 14 (2 lignes) → barre d'actions (Heart 22 / Comment 22 / Share 20 à gauche, Bookmark 22 à droite) → footer (`X j'aime` 13 semibold + `Voir les X commentaires` 13).

- [ ] **Step 1 : Vérifier la dépendance gradient**

Run: `cd frontend-mobile && grep -n "expo-linear-gradient" package.json || echo "ABSENT"`
Expected: présent → utiliser `LinearGradient`. Si `ABSENT` → fallback `backgroundColor: c.surface2` pour l'avatar (ne pas installer de dépendance sans accord ; noter dans le commit).

- [ ] **Step 2 : Réécrire `ArticleCard.tsx` aligné sur le web**

```tsx
import { Pressable, StyleSheet, View } from "react-native"
import { YStack, XStack, Text } from "tamagui"
import { Ionicons } from "@expo/vector-icons"
import { useColors } from "@/design/colors"
import { timeAgo } from "@/utils/formatDate"
import { AutoImage } from "./AutoImage"
import { TrustBadge } from "./TrustBadge"
import type { FeedItem } from "@/services/feed/feedTypes"

interface ArticleCardProps {
  article: FeedItem
  onPress?: () => void
  onLike?: () => void
  onBookmark?: () => void
  isBookmarked?: boolean
}

export function ArticleCard({
  article,
  onPress,
  onLike,
  onBookmark,
  isBookmarked = false,
}: ArticleCardProps) {
  const c = useColors()
  const sourceName = article.publisher?.name ?? "Source"
  const sourceInitials = sourceName.slice(0, 2).toUpperCase()
  const summary = article.summary?.replace(/<[^>]*>/g, "").trim()

  return (
    <View style={[styles.card, { backgroundColor: c.surface, borderColor: c.border }]}>
      {/* Header */}
      <XStack alignItems="center" justifyContent="space-between" paddingHorizontal={16} paddingTop={14} paddingBottom={8}>
        <XStack alignItems="center" gap={12} flex={1}>
          <XStack width={32} height={32} borderRadius={16} backgroundColor={c.surface2} alignItems="center" justifyContent="center">
            <Text color={c.color} fontSize={11} fontWeight="700">{sourceInitials}</Text>
          </XStack>
          <YStack flex={1}>
            <XStack alignItems="center" gap={6}>
              <Text color={c.color} fontSize={14} fontWeight="600" numberOfLines={1}>{sourceName}</Text>
              <TrustBadge score={article.trustScore} itemId={article.id} />
            </XStack>
            <Text color={c.color2} fontSize={11} numberOfLines={1}>
              {timeAgo(article.publishedAt ?? article.createdAt)}
              {article.author ? ` · ${article.author}` : ""}
            </Text>
          </YStack>
        </XStack>
        <Pressable hitSlop={8} style={styles.iconBtn}>
          <Ionicons name="ellipsis-horizontal" size={18} color={c.color2} />
        </Pressable>
      </XStack>

      {/* Image */}
      {article.imageUrl ? (
        <Pressable onPress={onPress}>
          <AutoImage uri={article.imageUrl} style={styles.image} />
        </Pressable>
      ) : null}

      {/* Corps */}
      <Pressable onPress={onPress} style={styles.body}>
        <Text color={c.color} fontSize={17} fontWeight="700" lineHeight={22}>{article.title}</Text>
        {summary ? (
          <Text color={c.color2} fontSize={14} lineHeight={20} numberOfLines={2} marginTop={6}>{summary}</Text>
        ) : null}
      </Pressable>

      {/* Actions */}
      <XStack alignItems="center" justifyContent="space-between" paddingHorizontal={12} paddingBottom={8}>
        <XStack alignItems="center">
          <Pressable onPress={onLike} hitSlop={8} style={styles.iconBtn}>
            <Ionicons name={article.isLiked ? "heart" : "heart-outline"} size={22} color={article.isLiked ? c.danger : c.color} />
          </Pressable>
          <Pressable onPress={onPress} hitSlop={8} style={styles.iconBtn}>
            <Ionicons name="chatbubble-outline" size={22} color={c.color} />
          </Pressable>
          <Pressable hitSlop={8} style={styles.iconBtn}>
            <Ionicons name="share-outline" size={20} color={c.color} />
          </Pressable>
        </XStack>
        <Pressable onPress={onBookmark} hitSlop={8} style={styles.iconBtn}>
          <Ionicons name={isBookmarked ? "bookmark" : "bookmark-outline"} size={22} color={c.color} />
        </Pressable>
      </XStack>

      {/* Footer */}
      <YStack paddingHorizontal={16} paddingBottom={16} gap={4}>
        {article.likesCount > 0 ? (
          <Text color={c.color} fontSize={13} fontWeight="600">
            {article.likesCount.toLocaleString("fr-FR")} j'aime
          </Text>
        ) : null}
        {article.commentsCount > 0 ? (
          <Pressable onPress={onPress}>
            <Text color={c.color2} fontSize={13}>
              Voir les {article.commentsCount} commentaire{article.commentsCount > 1 ? "s" : ""}
            </Text>
          </Pressable>
        ) : null}
      </YStack>
    </View>
  )
}

const styles = StyleSheet.create({
  card: { borderRadius: 12, borderWidth: 1, overflow: "hidden" },
  image: { width: "100%", height: 240, backgroundColor: "#000" },
  body: { paddingHorizontal: 16, paddingTop: 12, paddingBottom: 12 },
  iconBtn: { padding: 8 },
})
```

> **À valider à l'implémentation** (lecture du code, pas supposé) :
> - L'API exacte de `AutoImage` mobile (prop `uri` vs `src`/`source`) — l'aligner sur la signature réelle du composant.
> - Que `FeedItem` mobile expose bien `author`, `imageUrl`, `likesCount`, `commentsCount`, `isLiked` (sinon retirer la branche concernée — ne pas inventer de champ).
> - `borderRadius: 12` = `rounded-lg` web. `border` (pas `borderMuted`) comme le web.

- [ ] **Step 3 : Vérifier compile + tests**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
Expected: 0 erreur TS, tests verts (mettre à jour `ArticleCard.test.tsx` si la nouvelle structure casse une assertion de rendu, sans baisser la couverture).

- [ ] **Step 4 : Commit**

```bash
cd frontend-mobile
git add src/components/ArticleCard.tsx src/components/ArticleCard.test.tsx
git commit -m "style(mobile): rebuild ArticleCard as web-parity feed card"
```

### Tâches 2.1 → 2.21 (les autres composants)

Appliquer le **Recipe** ci-dessus à chacun, dans l'ordre conseillé. Une tâche = un composant = un commit. Checklist :

- [ ] Avatar — [ ] Skeleton — [ ] Badge (cas b, no-op probable) — [ ] IconButton — [ ] SectionHeader — [ ] TabSelector — [ ] EmptyState — [ ] Toast — [ ] TrustBadge
- [ ] SearchBar — [ ] SettingsRow — [ ] SettingsToggle — [ ] ThemeCard — [ ] UserRow — [ ] CommentInput — [ ] CommentItem — [ ] NotificationItem — [ ] PublisherCard — [ ] AutoImage — [ ] ProfileHeader — [ ] TrustDetailSheet

> Pour chaque : audit web/mobile → cas (a)/(b)/(c) → appliquer tokens+radius+typo → `pnpm compile && pnpm test` → commit. Ne jamais introduire de hex en dur ; toujours passer par `useColors()`.

---

## Lot 3 — Écrans (recipe + enumération)

### Recipe par écran

1. Lire `frontend-web/src/routes/(app|auth)/<screen>.tsx` : noter structure (header, sections, ordre des blocs, espacements `px-*`/`py-*`/`gap-*`, titres `wordmark`).
2. Adapter le layout mobile : même hiérarchie d'info et mêmes composants, **repensé tactile** (empilement vertical, zones de tap ≥ 44px, scroll). Pas de copie des grilles desktop.
3. Appliquer `wordmarkTitle(size)` aux gros titres et `wordmarkEyebrow` aux libellés majuscules.
4. `pnpm compile && pnpm test` verts → commit `style(mobile): align <screen> screen on web`.

### Tâches 3.1 → 3.16 (un écran = un commit)

- [ ] `(tabs)/index` (home) — réf `routes/(app)/home.tsx` (eyebrow date + titre 44 wordmark)
- [ ] `(tabs)/discover` — réf `routes/(app)/discover.tsx`
- [ ] `(tabs)/search` — réf `routes/(app)/search.tsx`
- [ ] `(tabs)/notifications` — réf `routes/(app)/notifications.tsx`
- [ ] `(tabs)/profile` — réf `routes/(app)/profile.tsx`
- [ ] `article/[id]` — réf `routes/(app)/article/[id].tsx`
- [ ] `user/[id]` — réf `routes/(app)/user/[id].tsx`
- [ ] `user/[id]/followers` + `user/[id]/following` (mobile-only) — aligner style sur la charte (pas de réf web dédiée ; s'inspirer de `UserRow` + en-tête de `user/[id]`)
- [ ] `settings/index` — réf `routes/(app)/settings/layout.tsx`
- [ ] `settings/account` — réf `routes/(app)/settings/account.tsx`
- [ ] `settings/appearance` — réf `routes/(app)/settings/appearance.tsx` (**câbler `ThemeCard` ici** → commit qui (re)track `ThemeCard.tsx`)
- [ ] `settings/data-privacy` — réf `routes/(app)/settings/data-privacy.tsx`
- [ ] `settings/notifications` — réf `routes/(app)/settings/notifications.tsx`
- [ ] `settings/privacy` — réf `routes/(app)/settings/privacy.tsx`
- [ ] `settings/security` — réf `routes/(app)/settings/security.tsx`
- [ ] `(auth)/login` + `(auth)/register` + `(auth)/forgot-password` — réf `routes/(auth)/*` (eyebrow wordmark + grand titre wordmark)

---

## Lot 4 — Audit police (Space Grotesk partout)

### Tâche 4.1 : Éliminer les fallbacks police système

> Cause probable du « le web rend mieux » : des `<Text>` RN natifs (pas Tamagui) sans `fontFamily` → San Francisco au lieu de Space Grotesk.

**Files:** balayage de `frontend-mobile/src/**`.

- [ ] **Step 1 : Lister les `<Text>` natifs RN (import depuis `react-native`)**

Run: `cd frontend-mobile && grep -rln "import.*Text.*from \"react-native\"" src --include="*.tsx"`
Expected: liste des fichiers à auditer.

- [ ] **Step 2 : Lister les `Text`/`TextInput` sans `fontFamily` explicite**

Run: `cd frontend-mobile && grep -rn "<TextInput" src --include="*.tsx" | wc -l`
Expected: chaque `TextInput` doit porter `fontFamily: "SpaceGrotesk_400Regular"` (les `TextInput` n'héritent jamais de la font Tamagui).

- [ ] **Step 3 : Corriger**

Pour chaque texte natif : soit migrer vers `Text` de `tamagui` (hérite `synthezaFont`), soit ajouter `style={{ fontFamily: "SpaceGrotesk_400Regular" }}`. Pour les `TextInput` : ajouter `fontFamily` explicite.

- [ ] **Step 4 : Vérifier compile + tests + commit**

Run: `cd frontend-mobile && pnpm compile && pnpm test`
```bash
git add -A && git commit -m "fix(mobile): ensure Space Grotesk on all native Text/TextInput"
```

---

## Lot 5 — Validation finale + intégration git

### Tâche 5.1 : Suppression du dead code web

**Files:**
- Delete: `frontend-web/src/design/colors.ts`

- [ ] **Step 1 : Confirmer que c'est mort**

Run: `cd frontend-web && grep -rn "design/colors\|darkColors\|lightColors" src --include="*.ts" --include="*.tsx" | grep -v "src/design/colors.ts" || echo "dead code confirmé"`
Expected: `dead code confirmé`.

- [ ] **Step 2 : Supprimer + vérifier le build web**

Run: `cd frontend-web && rm src/design/colors.ts && pnpm build`
Expected: build OK.

- [ ] **Step 3 : Commit (repo frontend-web, branche dédiée)**

```bash
cd frontend-web && git checkout -b chore/remove-dead-colors
git add -A && git commit -m "chore(web): remove dead design/colors.ts (source of truth is globals.css)"
```

### Tâche 5.2 : Validation visuelle simulateur (USER)

- [ ] **Step 1 : Vérif technique globale mobile**

Run: `cd frontend-mobile && pnpm compile && pnpm test && pnpm lint:check`
Expected: 0 erreur TS, 82 tests verts, lint sans nouvelle régression.

- [ ] **Step 2 : Lancer le simulateur**

Run: `cd frontend-mobile && pnpm ios`

- [ ] **Step 3 : Parcours de validation (USER, simulateur Apple)** — pour chaque écran : light + dark.
  - [ ] Boutons primaires : blancs en dark / noirs en light (primary inversé).
  - [ ] Cards : radius ~12px, bordure `border` (pas muted).
  - [ ] Titres de page : style wordmark serré.
  - [ ] **Tout** le texte en Space Grotesk (zéro San Francisco).
  - [ ] ArticleCard : header avatar+source+trust, image, titre/summary, actions, footer j'aime/commentaires.
  - [ ] Les 16 écrans + smoke publisher/channel (feed, discover, abonnements, profil).
- [ ] **Step 4 : Validation iPhone réel** (si OK simulateur).

### Tâche 5.3 : Merge + nettoyage des branches

> À faire **après** validation user. Confirmer la branche cible (`main`) au moment du merge.

- [ ] **Step 1 : Merge mobile sur main**

```bash
cd frontend-mobile && git checkout main
git merge --no-ff feat/mobile-design-iso-web -m "feat(mobile): web-parity charter + publisher/channel close-out"
```

- [ ] **Step 2 : Push + suppression des branches**

```bash
cd frontend-mobile && git push origin main
git branch -d feat/mobile-design-iso-web feat/publisher-channel-mobile-adapt
```

- [ ] **Step 3 : Merge web (suppression dead code)**

```bash
cd frontend-web && git checkout main
git merge --no-ff chore/remove-dead-colors -m "chore(web): remove dead colors.ts"
git push origin main && git branch -d chore/remove-dead-colors
```

---

## Self-Review (couverture spec)

- ✅ **Scope « parité visuelle, layout adapté »** → Lots 2 (composants) + 3 (écrans), recipe « repensé tactile ».
- ✅ **primary inversé** → Tâche 1.1 (colors.ts) + 1.2 (tokens.ts) + validation 5.2.
- ✅ **color4/link light** → Tâche 1.1.
- ✅ **radius web** → Tâche 1.2 + correction des `borderRadius` en dur au Lot 2.
- ✅ **wordmark** → Tâche 1.3 (helpers) + application Lot 3.
- ✅ **police identique partout** → Lot 4 (audit fallbacks système).
- ✅ **composants 1:1 + piège du nom** → Recipe Lot 2 (audit sémantique, cas a/b/c, `Badge` documenté).
- ✅ **back office exclu** → aucun fichier `admin/*` touché.
- ✅ **git : dead code, branche, clôture publisher, merge, suppression branches** → Lots 0 et 5.
- ✅ **validation à la fin sur simulateur** → Tâche 5.2.
- ✅ **followers/following gardés, reset-password web-only** → Lot 3.

**Limites assumées (honnêteté du plan) :** le code exact des 21 composants restants et des 16 écrans n'est pas inscrit ici — il dépend de la lecture fichier par fichier au moment de l'implémentation (l'inventer maintenant produirait du faux). Le plan fournit à la place : les tokens exacts (déterminés), un worked example complet (ArticleCard), un recipe reproductible, et l'énumération exhaustive. Chaque tâche composant/écran est self-contained (audit → applique → vérifie → commit).
