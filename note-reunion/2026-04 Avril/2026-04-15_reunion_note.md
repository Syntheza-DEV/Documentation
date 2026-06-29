# [Weekly] – Avancement sprint : pnpm, tests, fin des mocks

**Date** : 15/04/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Raphael

---

## Contexte

Point d'avancement du sprint d'industrialisation. La migration pnpm est faite,
la couverture de tests monte fortement, et les écrans mobile sont en cours de
branchement sur l'API réelle. Il reste à activer l'IA en prod.

## Revue des objectifs

* 🔄 Migration pnpm + tests : **Avancé** (pnpm migré, ~300 tests backend)
* 🔄 Mobile connecté à l'API réelle : **En cours** (phases fondation + services + écrans)
* ⏳ Activation IA Gemini en prod : **À finaliser**

## Ordre du jour

1. Avancement migration pnpm + tests
2. Mobile : suppression des mocks, connexion API
3. Nouvelles fonctionnalités sociales backend
4. Sécurité des nouvelles routes

## Discussions & Décisions

### Migration pnpm & tests
- **Discussion** : Migration pnpm effectuée sur les 3 repos. La suite de tests
  backend dépasse les 300 tests (Vitest), services mobile couverts par Jest.
- **Décision** : Continuer la montée en charge, viser une base testée avant le
  prochain gros chantier.
- **Responsable** : @Ilhan

### Mobile — fin des mocks
- **Discussion** : Les données mock sont supprimées côté mobile. Les écrans sont
  branchés sur les vrais endpoints (feed, like, comment, bookmark, follow,
  notifications, trust). ArticleCard interactive, CommentInput, badge notif.
- **Décision** : Poursuivre la connexion des derniers écrans + tests.
- **Responsable** : @Ilhan

### Fonctionnalités sociales backend
- **Discussion** : Ajout des bookmarks, follow/unfollow, subscriptions, upload
  d'avatar, recherche utilisateur et endpoint is-following.
- **Décision** : Raphael intègre ces fonctionnalités côté web au fil de l'eau.
- **Responsable** : @Ilhan (backend), @Raphael (web)

### Sécurité
- **Discussion** : Durcissement des nouvelles features (transactions, contrôle
  path traversal, validation des entrées).
- **Décision** : Passe sécurité systématique sur chaque nouvelle route.
- **Responsable** : @Ilhan

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Finaliser connexion API mobile (dernières phases) | @Ilhan | 18/04/2026 | 🔄 En cours |
| Activer Gemini en prod + trust scoring | @Ilhan | 18/04/2026 | ⏳ À faire |
| Intégrer les features sociales côté web | @Raphael | 22/04/2026 | 🔄 En cours |
| Compléter la suite de tests (objectif ~320) | @Ilhan | 22/04/2026 | 🔄 En cours |

## Points en suspens

- Activation IA en prod à confirmer
- Twitter : API v2 payante, stub en place

## Objectifs prochaine réunion

1. Sprint bouclé : pnpm + tests + IA active
2. Mobile entièrement connecté à l'API réelle
3. Bilan de la couverture de tests

## Prochaine réunion

**Date prévue** : 22/04/2026
**Sujets** : Bilan sprint industrialisation, démo IA en prod, point tests

---

## 📝 Notes brutes

- pnpm migré sur les 3 repos, +300 tests backend, services mobile testés (jest)
- mobile : mocks virés, écrans branchés sur le vrai backend
- backend : bookmarks, follow, subscriptions, avatar upload, user search
- passe sécu sur les nouvelles routes (transactions, path traversal, validation)
- reste à activer gemini en prod
