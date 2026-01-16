---
title:          Beta Test Plan — Syntheza
subtitle:       MVP / Beta Scope — Web + Mobile
author:         Team Syntheza
module:         G-EIP-700
version:        2.0
document:       beta_test_plan.md
repository:     https://github.com/Syntheza-DEV
---

## 1. Project context, objectives & how it works

### 1.1 Context
Syntheza est une plateforme SaaS de veille stratégique qui vise à réduire la surcharge d’informations en centralisant la collecte multi-sources et en synthétisant automatiquement l’essentiel. La cible prioritaire est constituée de professionnels (investisseurs, entrepreneurs, cadres) qui ont besoin d’une information pertinente, triée et résumée rapidement. :contentReference[oaicite:3]{index=3}

Le MVP/Bêta se concentre sur un parcours complet : **authentification → configuration des sources → collecte → feed personnalisé → recherche → synthèse IA + Trust Factor → interactions (like/comment/bookmark/follow) → stabilité/observabilité**.

### 1.2 Objectives (Beta Test Plan)
Ce Beta Test Plan a pour objectifs de :
- Lister les **fonctionnalités du scope bêta** qui seront **montrées pendant la Greenlight defense**. :contentReference[oaicite:4]{index=4}
- Organiser ces fonctionnalités selon un **parcours utilisateur typique**. :contentReference[oaicite:5]{index=5}
- Définir des **critères de succès simples, quantifiables et vérifiables** pour mesurer la maturité de la bêta. :contentReference[oaicite:6]{index=6}

> Note de cadrage : les “63 tasks MVP” représentent un **découpage d’implémentation**. Dans ce plan, elles sont **regroupées en features testables** (actions utilisateur) et reliées en annexe via une matrice de traçabilité.

### 1.3 How it works (simplified)
1) **Auth & session** : inscription/connexion, JWT, routes protégées, refresh token, logout.
2) **Sources** : CRUD des sources + configuration utilisateur + subscribe/unsubscribe.
3) **Collecte** : ingestion RSS + stub Twitter → normalisation → stockage (PostgreSQL) → aperçu collecte.
4) **Personnalisation** : ranking V1 + matching utilisateur↔articles.
5) **Restitution** : feed unifié, infinite scroll, recherche V1.
6) **IA** : résumé d’article + synthèse quotidienne (batch).
7) **Fiabilité** : Trust Factor V1 exposé via API et affiché.
8) **Engagement** : like, commentaires, bookmark, follow/unfollow.
9) **Ops** : stabilisation, observabilité logs, gestion d’erreurs UI, documentation technique.

---

## 2. User roles
| Role name | Description |
|---|---|
| Utilisateur Beta (Pro) | Utilisateur final (investisseur/entrepreneur/cadre). Configure ses sources, consulte le feed, recherche, lit des synthèses, utilise Trust Factor, interagit (like/comment/bookmark/follow). |
| Équipe Syntheza (QA/Support) | Utilisateurs internes. Reproduisent bugs, surveillent logs, valident stabilité/performance, assurent la cohérence UX et la préparation de la démo. |

---

## 3. Feature table (organized by user flow)
Toutes les fonctionnalités listées ci-dessous seront démontrées pendant la présentation beta.

| Feature ID | User role | Feature name | Short description |
|---|---|---|---|
| F1 | Utilisateur Beta | S’inscrire | Créer un compte (web ou mobile) et obtenir une session valide (JWT). |
| F2 | Utilisateur Beta | Se connecter | Se connecter sur web et mobile sur l’environnement cible (backend prod). |
| F3 | Utilisateur Beta | Réinitialiser un mot de passe | Mot de passe oublié + reset (API + écrans web/mobile), puis reconnexion. |
| F4 | Utilisateur Beta | Sécuriser l’accès | Routes protégées + refresh token automatique (pas de déconnexion intempestive). |
| F5 | Utilisateur Beta | Se déconnecter | Logout complet + nettoyage session côté client. |
| F6 | Utilisateur Beta | Gérer ses sources | CRUD des sources (RSS + Twitter placeholder/stub) via API + UI (web/mobile). |
| F7 | Utilisateur Beta | Personnaliser ses sources | Configuration des sources dans Settings + subscribe/unsubscribe (impact sur feed). |
| F8 | Utilisateur Beta | Collecter des contenus | Ingestion RSS + normalisation + ingestion Twitter stub + endpoint d’aperçu collecte. |
| F9 | Utilisateur Beta | Consulter un feed personnalisé | Feed unifié basé sur matching + ranking V1, affichage web/mobile. |
| F10 | Utilisateur Beta | Charger plus de contenus | Infinite scroll / pagination (web + mobile) sur Feed/Discover. |
| F11 | Utilisateur Beta | Rechercher des contenus | Recherche V1 (web + mobile) sur posts/articles. |
| F12 | Utilisateur Beta | Générer une synthèse d’article | Résumé IA simple/auto accessible depuis un contenu du feed. |
| F13 | Utilisateur Beta | Recevoir une synthèse quotidienne | Batch quotidien généré et consultable (UI/endpoint). |
| F14 | Utilisateur Beta | Afficher un Trust Factor | Trust Factor V1 calculé côté backend, exposé via API, affiché web/mobile. |
| F15 | Utilisateur Beta | Interagir avec un contenu | Like + commentaire + bookmark (backend + web + mobile). |
| F16 | Utilisateur Beta | Gérer son profil et son réseau | Profil utilisateur, upload avatar, follow/unfollow (web/mobile). |
| F17 | Utilisateur Beta | Recevoir une notification in-app | Notifications in-app (MVP) visibles côté utilisateur (événement/retour UI). |
| F18 | Équipe Syntheza | Surveiller et stabiliser l’application | Stabilisation backend/mobile, observabilité logs, gestion erreurs globales UI, doc technique. |

---

## 4. Success criteria (quantifiable & verifiable)
> Convention : les résultats seront renseignés pendant la campagne bêta : **Achieved / Partially achieved / Not achieved** (+ métrique).

| Feature ID | Key success criteria | Indicator/metric | Result achieved |
|---|---|---|---|
| F1 | Inscription réussie, session active et stable | 20 inscriptions, ≥ 95% succès, 0 blocage critique | TBD |
| F2 | Connexion web+mobile sur backend prod | 30 connexions, p95 < 2s, ≥ 95% succès | TBD |
| F3 | Reset permet de changer le MDP et de se reconnecter | 10 resets, ≥ 90% succès, 0 lien/flow cassé | TBD |
| F4 | Routes protégées + refresh token sans friction | 20 accès non-auth => 20 redirections; 45 min usage => 0 logout involontaire | TBD |
| F5 | Logout purge session et empêche l’accès | 15 logouts, 0 accès résiduel, 0 token réutilisable | TBD |
| F6 | CRUD Sources fonctionne (RSS + Twitter stub) | 30 opérations CRUD, ≥ 95% succès, persistance OK | TBD |
| F7 | Subscribe/config impacte réellement le feed | 20 actions subscribe/unsub, ≥ 90% cohérence feed sur 10 refresh | TBD |
| F8 | Collecte normalise des items exploitables | 10 RSS, ≥ 90% items valides (titre/date/source), 0 format cassé | TBD |
| F9 | Feed unifié charge et affiche des contenus pertinents | 20 ouvertures, p95 < 2s, ≥ 95% succès, 0 écran vide inattendu | TBD |
| F10 | Infinite scroll sans doublons ni freeze | 30 scrolls, 0 crash, doublons < 2% sur 200 items | TBD |
| F11 | Recherche retourne des résultats pertinents | 20 requêtes, ≥ 90% => ≥ 1 résultat attendu, p95 < 2s | TBD |
| F12 | Synthèse d’article non vide et cohérente | 20 articles, 0 résumé vide, p95 génération < 5s | TBD |
| F13 | Synthèse quotidienne générée et consultable | 5 exécutions batch, 5 synthèses accessibles, 0 échec batch | TBD |
| F14 | Trust Factor affiché sans valeur manquante | 30 affichages, 0 NA, p95 affichage < 1s après data | TBD |
| F15 | Like/comment/bookmark persistants et synchronisés | 100 actions, ≥ 98% persistance après refresh, 0 désynchro critique | TBD |
| F16 | Profil/avatar/follow-unfollow cohérents | 30 actions follow/unfollow, cohérence ≥ 95%; 10 uploads avatar ≥ 90% succès | TBD |
| F17 | Notification visible côté utilisateur | 20 événements, ≥ 90% notifications visibles, 0 spam/duplicat critique | TBD |
| F18 | Stabilité/observabilité prouvées pendant scénarios | 0 crash critique & 0 perte de données; logs exploitables; 90% écrans < 2s | TBD |

### 4.1 Global beta thresholds (jury-friendly)
Inspirés d’une grille “stabilité/usabilité/performance/consistance” utilisée en plan bêta structuré : :contentReference[oaicite:7]{index=7}  
- **Stability** : 0 crash critique, 0 perte de données sur les scénarios principaux.  
- **Core functionality** : 100% des tests critiques (scénarios ci-dessous) passent.  
- **Usability/UX** : ≥ 80% des testeurs réalisent les scénarios clés sans aide.  
- **Performance** : ≥ 90% des écrans chargent en < 2s (p95) sur environnement bêta.  

---

## 5. Beta testing scenarios (for execution & demo rehearsal)
> Objectif : scénarios **démontrables** en jury + exécutables en campagne bêta (préconditions, steps, expected outcomes).

### Scenario A — Onboarding & session
- **Role** : Utilisateur Beta  
- **Objective** : S’inscrire / se connecter / session stable / logout  
- **Preconditions** : compte inexistant  
- **Steps** : inscription → login → navigation routes privées → refresh token → logout  
- **Expected outcome** : accès sécurisé, aucune déconnexion surprise, logout complet

### Scenario B — Sources configuration
- **Role** : Utilisateur Beta  
- **Objective** : Ajouter/modifier/supprimer une source + subscribe  
- **Preconditions** : compte actif  
- **Steps** : ajouter RSS → modifier → subscribe → supprimer  
- **Expected outcome** : persistance + impact sur feed

### Scenario C — Collecte & preview
- **Role** : Utilisateur Beta  
- **Objective** : Vérifier que la collecte fournit des items normalisés  
- **Preconditions** : ≥ 1 source RSS active  
- **Steps** : lancer/attendre collecte → consulter endpoint preview  
- **Expected outcome** : items exploitables (titre/date/source), pas de format invalide

### Scenario D — Feed consumption
- **Role** : Utilisateur Beta  
- **Objective** : Feed unifié + ranking/matching + infinite scroll  
- **Preconditions** : collecte disponible  
- **Steps** : ouvrir feed → scroll → ouvrir item  
- **Expected outcome** : feed stable, pas de freeze, peu de doublons

### Scenario E — Search & summarization
- **Role** : Utilisateur Beta  
- **Objective** : rechercher → ouvrir résultat → obtenir synthèse IA + Trust Factor  
- **Preconditions** : contenus existants  
- **Steps** : search → ouvrir → afficher résumé + trust factor  
- **Expected outcome** : réponses rapides et non vides

### Scenario F — Engagement & profile
- **Role** : Utilisateur Beta  
- **Objective** : like/comment/bookmark + follow + avatar  
- **Preconditions** : contenu disponible  
- **Steps** : like → commenter → bookmark → follow → upload avatar  
- **Expected outcome** : persistance et synchronisation UI/API

---

## 6. Known issues & limitations (beta)
- **Twitter** : intégration en mode **stub/placeholder** (objectif : valider le pipeline et l’UX, pas l’intégration complète).  
- **Alertes avancées / dashboards avancés / extensions** : hors scope de cette bêta, réservés aux phases suivantes. :contentReference[oaicite:8]{index=8}  
- **Objectif bêta** : valider la proposition de valeur “veille centralisée + synthèse” et la stabilité en usage réel. :contentReference[oaicite:9]{index=9}  

---

## Appendix A — Traceability matrix (MVP tasks → Beta features)
> But : prouver que les **63 tasks** sont couvertes, sans les présenter comme 63 “features” à défendre.  
> Format : `Task # — Title → Feature ID(s)`

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
