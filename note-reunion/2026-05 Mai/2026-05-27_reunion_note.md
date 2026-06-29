# [Weekly] – Déploiements prod & Admin Panel V1

**Date** : 27/05/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Raphael

---

## Contexte

Les chantiers de la semaine précédente (refacto data model + rebuild v2) sont
déployés et stables en prod. La réunion acte le démarrage de l'outillage interne
avec un premier Admin Panel (dashboard, gestion utilisateurs et publishers).

## Revue des objectifs

* ✅ Refacto + rebuild v2 déployés en prod : **OK** (prod stable)
* ✅ Admin Panel V1 : **Livré** (dashboard, users, publishers)
* 🔄 Notifications SYSTEM admin : **En place**

## Ordre du jour

1. Validation des déploiements prod
2. Admin Panel V1 — backend (stats) et web
3. Notifications SYSTEM sur les crons
4. Point bundle web

## Discussions & Décisions

### Déploiements prod
- **Discussion** : Refacto Publisher/Channel et rebuild v2 web tournent en prod,
  feed alimenté par les vraies sources, prod stable.
- **Décision** : Déploiements validés, on capitalise dessus.
- **Responsable** : @Ilhan

### Admin Panel V1
- **Discussion** : Backend — endpoints stats (`GET /admin/stats/overview` et
  `/stats/analytics` avec timeseries, distributions, tops, Zod period schema,
  tests d'intégration). Web — dashboard (KPIs, sparklines, distribution des
  rôles, tops, activity), users CRUD (table, change role, set password, delete,
  page détail), publishers CRUD, skeletons, guard `AdminRoute` + `useIsAdmin`,
  `AuthUser` étendu (role + id numérique, normalisation depuis l'API).
- **Décision** : Admin Panel V1 livré et déployé.
- **Responsable** : @Ilhan

### Notifications SYSTEM
- **Discussion** : Les crons émettent des notifications SYSTEM vers les admins à
  la complétion des jobs (ingestion, summarize, trust, ranking, digest).
- **Décision** : Section activité système ajoutée à la page notifications (admins).
- **Responsable** : @Ilhan

### Bundle web
- **Discussion** : L'ajout de recharts alourdit le bundle web.
- **Décision** : Acceptable pour l'instant, code-split à envisager plus tard.

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Préparer la page Système admin (crons + budget IA) | @Ilhan | 03/06/2026 | ⏳ À faire |
| Surveiller la conso IA en prod | @Ilhan | Continu | 🔄 En cours |
| Continuer les corrections web post-rebuild | @Raphael | 03/06/2026 | 🔄 En cours |

## Points en suspens

- Bundle web alourdi par recharts (code-split éventuel)
- Migration jamais validée sur un gros volume de vraie donnée

## Objectifs prochaine réunion

1. Page Système admin livrée (toggles crons + jauge budget IA)
2. Pilotage de la consommation Gemini

## Prochaine réunion

**Date prévue** : 03/06/2026
**Sujets** : Admin V2 système, suivi conso IA

---

## 📝 Notes brutes

- refacto + rebuild v2 en prod, stable
- admin panel v1 : backend stats (overview + analytics) + web dashboard/users/publishers
- crons -> notifs SYSTEM aux admins, section activité sur la page notifs
- AuthUser étendu avec role + id number
- recharts alourdit le bundle, code-split plus tard
