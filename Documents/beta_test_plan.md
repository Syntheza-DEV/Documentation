---
title:          Plan de Test Bêta — Syntheza
subtitle:       MVP / Portée Bêta — Web + Mobile
author:         Équipe Syntheza
module:         G-EIP-700
version:        2.1
document:       beta_test_plan.md
repository:     https://github.com/Syntheza-DEV
---

## 0. Accès à la bêta (où récupérer l’application et comment y accéder)

Cette section répond explicitement à : **“Quel site ? En ligne ou en local ? Où je récupère l’application ou le client web ?”**

### 0.1 Client Web


**Option A — En ligne (recommandé pour le jury)**

- URL (staging ou prod) : [https://syntheza.app](https://syntheza.app)
- Identifiants jury (compte de test) :
   - Utilisateur : `jury_beta@mail.com` / Mot de passe : `demojury2026`
   - Operator (Back-office) : `ops_beta@mail.com` / Mot de passe : `opsdemo2026`

**Option B — En local (si l’environnement en ligne est indisponible)**

- Prérequis : Docker + Docker Compose
- Commande :

   ```sh
   docker compose up
   ```
- URL locale : [http://localhost:3000](http://localhost:3000)
- Identifiants : mêmes comptes que ci-dessus ou comptes créés via le script de seed (voir 0.3)

### 0.2 Client Mobile (Expo Go)

L’application mobile est accessible via **Expo Go** (aucun APK/TestFlight requis pour la démo jury).

- Prérequis : installer **Expo Go** sur un smartphone (iOS ou Android)
- Accès à l’app :
   - Scanner le QR Code Expo (fourni lors de la démo)
   - Ou ouvrir le lien Expo : [https://expo.dev/@syntheza/beta](https://expo.dev/@syntheza/beta)
- Identifiants jury : mêmes comptes que Web

### 0.3 Pack “preuve & démo” (recommandé)

Pour garantir une démonstration stable et reproductible, nous fournissons :

- Un **script de seed/dataset** pour générer des sources et des contenus de test :
   ```sh
   npm run seed:demo
   ```
- Un **back-office minimal (Operator only)** pour :
   - déclencher manuellement la **synthèse quotidienne**
   - déclencher une **notification in-app de test**
   - visualiser l’état de la collecte (dernière exécution, erreurs, volume)
   - accéder à des informations d’observabilité (logs / métriques de base)

---

## 1. Contexte du projet, objectifs et fonctionnement

### 1.1 Contexte

Syntheza est une plateforme de veille stratégique assistée par IA. Elle centralise la collecte (RSS + Twitter en mode stub/placeholder au MVP), normalise les contenus, propose un feed personnalisé (matching + ranking), permet la recherche, et enrichit la lecture via des synthèses IA et un indicateur de fiabilité appelé **Trust Factor**.

Le MVP/Bêta vise à démontrer une valeur claire : **trouver rapidement l’essentiel** et le consommer dans une interface simple (web + mobile).

### 1.2 Objectifs du Beta Test Plan (exigences G-EIP)

Ce Beta Test Plan sert à :

* **Lister les fonctionnalités du scope bêta** qui seront **montrées pendant la Greenlight defense**
* **Organiser les fonctionnalités par parcours utilisateur** (user flow)
* Définir des **critères de succès simples, quantifiables et vérifiables** pour mesurer la maturité de la bêta

> Note : les “63 tasks MVP” correspondent à un **découpage d’implémentation**. Dans ce plan, elles sont regroupées en **features** (actions utilisateur) testables et démontrables. Une matrice de traçabilité relie tasks → features (Appendix A).

### 1.3 Fonctionnement (vue “utilisateur externe”)

1. L’utilisateur crée un compte ou se connecte (web ou mobile).
2. Il va dans **Settings → Sources** et ajoute une source (RSS ou Twitter stub).
3. Le système collecte et normalise les items (titre, date, source, url).
4. L’utilisateur consulte un **feed unifié** : contenus triés (ranking) et personnalisés (matching).
5. Il peut **rechercher** un sujet précis.
6. Lorsqu’il ouvre un contenu, il voit :

   * une **synthèse IA** (résumé)
   * un **Trust Factor** (indicateur de fiabilité/qualité V1)
7. Il peut interagir (like/commentaire/bookmark/follow).
8. Un Operator (interne) peut déclencher certains traitements pour la démo (synthèse quotidienne, notification test) et surveiller l’état système via un back-office minimal.

---

## 2. Rôles utilisateurs

| Role name                   | Description                                                                                                                                                                                                                             |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Utilisateur Beta (Pro)      | Utilisateur final. Configure ses sources, consulte le feed, recherche, lit des synthèses, comprend le Trust Factor, interagit (like/comment/bookmark/follow).                                                                           |
| Operator (Back-office / QA) | Rôle interne. Peut : (1) déclencher manuellement la synthèse quotidienne, (2) déclencher une notification de test, (3) vérifier l’état de collecte/ingestion, (4) consulter logs/observabilité, (5) stabiliser l’environnement de démo. |

---

## 3. Feature table (organisée par parcours utilisateur)

Toutes les fonctionnalités listées ci-dessous seront démontrées pendant la présentation bêta.

| Feature ID | User role        | Feature name                                  | Short description                                                                                                                                    |
| ---------- | ---------------- | --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| F1         | Utilisateur Beta | S’inscrire                                    | Créer un compte (web ou mobile) et obtenir une session valide (JWT).                                                                                 |
| F2         | Utilisateur Beta | Se connecter                                  | Se connecter sur web et mobile (en ligne ou local).                                                                                                  |
| F3         | Utilisateur Beta | Réinitialiser un mot de passe                 | Déclencher “mot de passe oublié”, recevoir un reset, définir un nouveau mot de passe, se reconnecter.                                                |
| F4         | Utilisateur Beta | Accéder à des pages sécurisées                | Vérifier que les pages privées sont inaccessibles sans login et que la session se maintient via refresh token.                                       |
| F5         | Utilisateur Beta | Se déconnecter                                | Logout complet, suppression de la session et interdiction d’accès aux pages privées après logout.                                                    |
| F6         | Utilisateur Beta | Ajouter une source (parcours guidé)           | Depuis Settings → Sources : ajouter une source RSS (coller URL) ou Twitter stub (choisir un topic), activer, sauvegarder.                            |
| F7         | Utilisateur Beta | Gérer ses sources                             | Modifier/supprimer une source, et subscribe/unsubscribe pour influencer le feed.                                                                     |
| F8         | Utilisateur Beta | Vérifier la collecte normalisée               | Voir des items normalisés (titre/date/source/url) et un aperçu de collecte (UI/endpoint Operator).                                                   |
| F9         | Utilisateur Beta | Consulter un feed personnalisé                | Consulter le feed unifié basé sur matching + ranking V1 (web + mobile).                                                                              |
| F10        | Utilisateur Beta | Charger plus de contenus                      | Infinite scroll / pagination sur Feed et Discover (web + mobile).                                                                                    |
| F11        | Utilisateur Beta | Rechercher des contenus                       | Rechercher des articles/posts via Search/Discover (web + mobile).                                                                                    |
| F12        | Utilisateur Beta | Lire une synthèse d’article                   | Ouvrir un contenu et afficher une synthèse IA non vide et cohérente.                                                                                 |
| F13        | Operator         | Générer une synthèse quotidienne à la demande | Déclencher manuellement le batch “daily summary” depuis le back-office afin de prouver le résultat au jury.                                          |
| F14        | Utilisateur Beta | Comprendre et utiliser le Trust Factor        | Voir un indicateur simple (score/label) aidant à estimer la fiabilité/qualité du contenu, expliqué en termes compréhensibles.                        |
| F15        | Utilisateur Beta | Interagir avec un contenu                     | Like + commentaire + bookmark (persistants) sur web + mobile.                                                                                        |
| F16        | Utilisateur Beta | Gérer son profil et son réseau                | Accéder au profil, upload avatar, follow/unfollow (web + mobile).                                                                                    |
| F17        | Operator         | Déclencher une notification in-app            | Envoyer une notification de test depuis back-office et la voir apparaître côté utilisateur.                                                          |
| F18        | Operator         | Utiliser le back-office (ops/QA)              | Visualiser état ingestion/collecte, lancer les jobs manuels, consulter logs, vérifier stabilité, et valider le comportement des erreurs UI globales. |

---

## 4. Critères de succès (simples, quantifiables et vérifiables)

> Résultats à renseigner pendant la campagne bêta : **Achieved / Partially achieved / Not achieved** (+ métrique ou commentaire).

| Feature ID | Key success criteria                                             | Indicator/metric                                                                  | Result achieved |
| ---------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------- | --------------- |
| F1         | L’inscription crée un compte et ouvre une session valide         | 20 inscriptions, ≥ 95% succès, 0 blocage critique                                 | TBD             |
| F2         | La connexion fonctionne sur web + Expo Go et reste fluide        | 30 connexions, p95 < 2s, ≥ 95% succès                                             | TBD             |
| F3         | Le reset permet de changer le mot de passe puis se reconnecter   | 10 resets, ≥ 90% succès, 0 lien cassé                                             | TBD             |
| F4         | Preuve en démo : routes privées bloquées + refresh token visible | 10 essais incognito: 10/10 redirections + refresh observé (voir scénarios)        | TBD             |
| F5         | Logout supprime la session et interdit l’accès ensuite           | 15 logouts, 0 accès résiduel                                                      | TBD             |
| F6         | Un utilisateur “lambda” comprend comment ajouter une source      | 10 testeurs, ≥ 80% ajoutent une RSS et voient son effet sans aide                 | TBD             |
| F7         | CRUD + subscribe/unsubscribe sont persistants                    | 30 opérations, ≥ 95% succès, cohérence feed ≥ 90%                                 | TBD             |
| F8         | Collecte produit des items normalisés exploitables               | 10 RSS, ≥ 90% items valides (titre/date/source/url), 0 format cassé               | TBD             |
| F9         | Feed charge vite et reste stable                                 | 20 ouvertures, p95 < 2s, ≥ 95% succès, 0 écran vide inattendu                     | TBD             |
| F10        | Infinite scroll sans freeze, crash ni doublons massifs           | 30 scrolls, 0 crash, doublons < 2% sur 200 items                                  | TBD             |
| F11        | Recherche rapide et pertinente                                   | 20 requêtes, ≥ 90% => ≥ 1 résultat attendu, p95 < 2s                              | TBD             |
| F12        | Synthèse IA non vide et cohérente                                | 20 articles, 0 résumé vide, p95 génération < 5s                                   | TBD             |
| F13        | Daily summary prouvable grâce au déclenchement manuel            | 5 déclenchements manuels: 5/5 générés + affichés                                  | TBD             |
| F14        | Trust Factor compréhensible et affiché sans NA                   | 30 affichages, 0 NA, p95 affichage < 1s                                           | TBD             |
| F15        | Like/comment/bookmark persistent après refresh/restart           | 100 actions, ≥ 98% persistance après refresh                                      | TBD             |
| F16        | Profil/avatar/follow cohérents sur web + mobile                  | 30 follow/unfollow, cohérence ≥ 95%; 10 uploads avatar ≥ 90%                      | TBD             |
| F17        | Notification in-app prouvable via trigger Operator               | 20 triggers, ≥ 90% visibles, duplicats critiques = 0                              | TBD             |
| F18        | Back-office utilisable en démo et en test                        | 0 crash critique; jobs manuels utilisables; logs visibles; erreurs UI globales OK | TBD             |

---

## 5. Scénarios de démonstration (orientés preuve)

Ces scénarios sont conçus pour **prouver** les points critiques demandés par les évaluateurs (F4, F6, F13, F17, back-office).

### Scenario A — Preuve F4 : routes protégées + refresh token

**Objectif :** prouver la sécurisation et le maintien de session en démo.

1. Ouvrir une fenêtre **navigation privée/incognito**
2. Accéder directement à une route privée, ex : `/app/feed`
3. **Attendu :** redirection vers `/login` (preuve “route protégée”)
4. Se connecter avec le compte jury
5. Déclencher un refresh token **de manière démontrable** :

   * Option 1 (recommandé) : environnement “demo” avec **TTL court** (ex : 60s)
   * Option 2 : bouton Operator **“Simulate token expiry”**
6. Continuer à naviguer sans être déconnecté
   **Preuve attendue :** 401 géré → refresh → requête rejouée → utilisateur reste connecté

### Scenario B — Preuve F6 : ajout de source compréhensible (utilisateur externe)

**Objectif :** démontrer un parcours “lambda”.

1. Aller dans **Settings → Sources**
2. Cliquer **Add source**
3. Choisir le type :

   * **RSS** : coller URL RSS, nommer, activer
   * **Twitter stub** : sélectionner un topic/placeholder, activer
4. Cliquer **Save**
5. Revenir au Feed
6. Vérifier qu’au moins une carte affiche la source (nom/source visible)
7. (Optionnel) unsubscribe et constater un changement sur refresh
   **Preuve attendue :** l’utilisateur comprend quoi faire et voit l’impact sur le feed

### Scenario C — Preuve F13 : synthèse quotidienne déclenchable manuellement

**Objectif :** rendre “batch daily summary” prouvable au jury.

1. Operator ouvre back-office
2. Cliquer **Run daily summary now**
3. Attendre le statut “Completed”
4. Afficher la synthèse générée (UI/endpoint)
   **Preuve attendue :** 5/5 runs manuels OK

### Scenario D — Preuve F17 : notifications in-app déclenchables

**Objectif :** prouver la fonctionnalité notifications sans dépendre d’événements externes.

1. Operator ouvre back-office
2. Cliquer **Send test notification**
3. Utilisateur voit la notification dans l’app (banner/center/badge)
   **Preuve attendue :** notification visible et non dupliquée

---

## 6. Trust Factor (explication vulgarisée et utilisable)

Le **Trust Factor** est un indicateur V1 qui aide l’utilisateur à répondre à la question :
**“Puis-je faire confiance à cette information / est-elle suffisamment propre et exploitable ?”**

### Ce que l’utilisateur voit

* Un **score** (ex : 0–100) ou un **label** (Low/Medium/High) affiché sur un contenu.
* L’objectif n’est pas de “dire la vérité”, mais de donner un **signal** pour prioriser la lecture.

### À quoi ça sert dans le MVP

* Aider à trier et décider rapidement quels contenus lire.
* Donner un repère quand une source est peu claire ou quand l’item est incomplet.

### Comment c’est calculé (V1, simple et vérifiable)

La V1 utilise des signaux basiques et objectivables, par exemple :

* **Qualité des métadonnées** : présence titre/date/source/url (item incomplet → score baisse)
* **Cohérence / duplication** : item dupliqué ou incohérent → score baisse
* **Type de source** : RSS reconnu vs source non renseignée (signal léger)

> Note : V1 = validation UX + disponibilité data. Les versions suivantes pourront intégrer plus d’analyses.

---

## 7. Back-office / QA / Ops (clair et démontrable)

Le **back-office minimal (Operator only)** est un écran interne (ou un ensemble d’endpoints sécurisés) utilisé pour :

* **Voir l’état système** : dernière collecte, nombre d’items, erreurs éventuelles
* **Déclencher manuellement** des jobs asynchrones :

  * **Run daily summary now**
  * **Send test notification**
* **Accéder à des éléments d’observabilité** :

  * logs applicatifs
  * indicateurs de base (succès/échec jobs, latences clés si disponibles)
* **Sécuriser la démo** : s’assurer qu’il y a des données, que les jobs sont prouvables, et que les erreurs sont visibles côté UI.

Ce back-office est un élément essentiel de la bêta car il rend le système **testable et démontrable** sans dépendre du hasard (batch nocturne, notifications aléatoires, etc.).

---

## Annexe A — Matrice de traçabilité (tâches MVP → fonctionnalités bêta)

> Objectif : prouver que les **63 tâches** sont couvertes, sans les présenter comme 63 “fonctionnalités” distinctes.
> Format : `Tâche # — Titre → ID(s) fonctionnalité`

1 — Activer connexion Login au Backend Prod (web) → F2
2 — Modèle de données initial (PostgreSQL) → F8, F18
3 — Auth backend (Inscription/Connexion + JWT) → F1, F2
4 — CRUD Sources (RSS + Twitter placeholder) → F6
5 — Ingestion RSS + normalisation items → F8
6 — Endpoint aperçu données collectées → F8
7 — Service Résumé IA simple → F12
8 — Résumé quotidien batch → F13
9 — Pipeline tri articles (Ranking V1) → F9
10 — Matching utilisateur ↔ articles → F9
11 — Résumé automatique articles → F12
12 — Trust Factor V1 → F14
13 — Exposition Trust Factor API → F14
14 — Benchmark solutions LLM → F12, F18
15 — Endpoint Feed unifié → F9
16 — Like backend → F15
17 — Commentaires backend → F15
18 — Password Lost Reset API → F3
19 — Recherche articles V1 → F11
20 — Configuration sources backend → F7
21 — Stabilisation backend → F18
22 — Page Password Lost complète (backend) → F3
23 — Composant Trust Factor (web) → F14
24 — Configuration sources Settings (web) → F7
25 — Pagination infinite scroll Feed/Discover (web) → F10
26 — Bookmark posts (web) → F15
27 — Recherche Search onglet manquant (web) → F11
28 — Follow/Unfollow (web) → F16
29 — Upload photo profil (web) → F16
30 — Changement mot de passe Settings (web) → F3
31 — Navigation mobile UI (web responsive) → F18
32 — Responsive mobile global (web) → F18
33 — API Password Reset (web) → F3
34 — API Trust Factor (web) → F14
35 — API Sources (web) → F6, F7
36 — Protection routes auth (web) → F4
37 — Gestion token refresh (web) → F4
38 — Gestion erreurs globales UI (web) → F18
39 — Logout complet (web) → F5
40 — Auth mobile → F1, F2, F4
41 — Password Lost mobile → F3
42 — Feed mobile → F9
43 — Infinite scroll mobile → F10
44 — Discover mobile → F10
45 — Search mobile → F11
46 — Settings mobile → F7, F3, F16
47 — Sources mobile → F6, F7
48 — Like mobile → F15
49 — Commentaires mobile → F15
50 — Trust Factor mobile → F14
51 — Bookmark mobile → F15
52 — Follow mobile → F16
53 — Subscribe mobile → F7
54 — Upload avatar mobile → F16
55 — Password change mobile → F3
56 — Profile mobile → F16
57 — Connexion prod mobile → F2
58 — UX/UI mobile → F18
59 — Stabilisation Beta mobile → F18
60 — Ingestion Twitter stub → F8
61 — Notifications in-app → F17
62 — Observabilité logs → F18
63 — Documentation technique MVP → F18
