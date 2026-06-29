# [Weekly] – Refacto Publisher/Channel & Rebuild v2 du frontend web

**Date** : 20/05/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Grosse semaine technique : le refacto du modèle de données est livré et le
frontend web a été entièrement reconstruit (rebuild v2). Deux pivots structurants
pour la suite du projet.

## Revue des objectifs

* ✅ Refacto Publisher/Channel/Item : **Livré** (backend + mobile adaptés)
* ✅ Rebuild v2 frontend web : **Livré** (Vite + React 19 + TS strict)
* 🔄 Migration en prod : **À déployer**

## Ordre du jour

1. Refonte data model Publisher/Channel/Item
2. Rebuild v2 du frontend web
3. Sécurité (bind Postgres)
4. Prochaines étapes : déploiement + outillage admin

## Discussions & Décisions

### Refacto Publisher/Channel/Item
- **Discussion** : Nouveaux modèles `Publisher` / `Channel` / `Item`, moteur de
  déduplication (canonicalisation URL + fingerprint SHA-256). CRUD publishers /
  channels / subscriptions, tests d'intégration, Swagger à jour, script de
  migration one-shot. Anciens modèles `Source` / `IngestedItem` /
  `SourceSubscription` supprimés. Mobile adapté (services publisher, shape
  FeedItem, queryKeys, discover/profile), dead code retiré (SourceCard,
  `getSubscriberCount` dont la route backend n'a jamais existé).
- **Décision** : Refacto acté. Endpoints `/api/sources` et `/api/subscriptions`
  marqués dépréciés puis retirés.
- **Responsable** : @Ilhan

### Rebuild v2 frontend web
- **Discussion** : Reconstruction complète : wipe du scaffold CRA, base
  Vite + React 19 + TS strict + pnpm, Tailwind v4 + design tokens iso mobile,
  22 composants portés, 13 services, apisauce + file de refresh, auth + Google
  OAuth, layout (sidebar / bottom tabs / dark mode), Home/feed infinite scroll,
  Discover, Search debounced, Notifications, profil + avatar, article + comments
  + trust sheet, settings 6 sous-routes, Space Grotesk + logo officiel, suite
  Vitest (27 tests), CI lint/typecheck/test.
- **Décision** : Rebuild v2 adopté comme nouvelle base web. Raphael en appui sur
  les corrections de bugs.
- **Responsable** : @Ilhan (base), @Raphael (fixes)

### Sécurité
- **Discussion** : Le port Postgres était exposé publiquement.
- **Décision** : Bind du port Postgres sur `127.0.0.1` uniquement.
- **Responsable** : @Ilhan

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Déployer refacto + rebuild v2 en prod | @Ilhan | 27/05/2026 | ⏳ À faire |
| Valider la migration sur données de prod | @Ilhan | 27/05/2026 | ⏳ À faire |
| Corrections de bugs web post-rebuild | @Raphael | 27/05/2026 | 🔄 En cours |
| Démarrer l'outillage admin (dashboard) | @Ilhan | 27/05/2026 | ⏳ À faire |

## Points en suspens

- Migration à valider sur la vraie donnée
- Bundle web alourdi (à surveiller pour la suite)

## Objectifs prochaine réunion

1. Refacto + rebuild v2 déployés et stables en prod
2. Admin Panel V1 démarré (dashboard, users, publishers)

## Prochaine réunion

**Date prévue** : 27/05/2026
**Sujets** : Validation déploiements, démo Admin Panel V1

---

## 📝 Notes brutes

- refacto livré : Publisher/Channel/Item + dedup, CRUD + tests + swagger + migration script
- anciens modèles Source/IngestedItem droppés, /api/sources et /api/subscriptions dépréciés
- mobile adapté (publisher services, feeditem shape, querykeys), dead code viré
- rebuild v2 web complet : vite + react 19 + ts strict, 22 composants, 13 services, 27 tests
- sécu : bind postgres sur 127.0.0.1
- raph en appui sur les fixes web
