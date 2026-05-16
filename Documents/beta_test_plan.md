---
title:          Plan de Test Bêta — Syntheza
subtitle:       MVP / Portée Bêta — Web + Mobile (Édition Business & Stratégie)
author:         Équipe Syntheza
module:         G-EIP-700
version:        2.2
document:       beta_test_plan.md
repository:     https://github.com/Syntheza-DEV
---

## 0. Accès à la bêta (où récupérer l’application et comment y accéder)

Cette section répond explicitement à : “Quel site ? En ligne ou en local ? Où je récupère l’application ou le client web ?”

### 0.1 Client Web

**Option A — En ligne (recommandé pour le jury)**

- URL (staging ou prod) : https://syntheza.app
- Identifiants jury (comptes de test) :
   - Utilisateur Test Free : jury_free@mail.com / Mot de passe : demojury2026
   - Utilisateur Test Pro : jury_pro@mail.com / Mot de passe : demojury2026
   - Operator (Back-office) : ops_beta@mail.com / Mot de passe : opsdemo2026

**Option B — En local (si l’environnement en ligne est indisponible)**

- Prérequis : Docker + Docker Compose
- Commande : `docker compose up`
- URL locale : http://localhost:3000
- Identifiants : Mêmes comptes que ci-dessus ou comptes créés via le script de seed.

### 0.2 Client Mobile (Expo Go)

L’application mobile est accessible via Expo Go (aucun APK/TestFlight requis pour la démo jury).

- Prérequis : Installer Expo Go sur un smartphone (iOS ou Android).
- Accès à l’app : Scanner le QR Code Expo (fourni lors de la démo) ou ouvrir le lien https://expo.dev/@syntheza/beta.
- Identifiants jury : Mêmes comptes que le Web.

### 0.3 Pack “preuve & démo”

Pour garantir une démonstration stable et reproductible, nous fournissons :
- Un script de seed/dataset pour générer des sources, des statuts d'abonnement et des contenus de test : `npm run seed:demo`
- Un back-office minimal (Operator only) pour déclencher manuellement la synthèse quotidienne, déclencher une notification de test, simuler le franchissement de la barrière Freemium et visualiser l’état du système.

---

## 1. Contexte du projet, objectifs et fonctionnement

### 1.1 Contexte & Positionnement Cible

Syntheza est une plateforme de veille stratégique assistée par IA conçue pour lutter contre l'infobésité. Contrairement aux réseaux sociaux traditionnels fondés sur l'économie de l'attention, Syntheza se positionne sur le créneau du "Slow Content" productif.

Le produit cible prioritairement le segment des "Prosumers" :
* Étudiants en fin de cursus / Mastériens : Besoin de sourcer des mémoires et projets.
* Freelances et Consultants : Veille sectorielle et concurrentielle pour leurs clients.
* Cadres et Veilleurs en entreprise : Gagner du temps au quotidien.

La promesse client : Diviser par 10 le temps de veille quotidien (passer de 45 minutes de recherche fragmentée à 5 minutes de lecture hautement qualitative et synthétisée).

### 1.2 Objectifs du Beta Test Plan

* Lister les fonctionnalités du scope bêta qui seront montrées pendant la Greenlight defense.
* Organiser les fonctionnalités par parcours utilisateur (user flow) incluant la validation business.
* Définir des critères de succès simples, quantifiables et vérifiables pour mesurer la maturité technique et l'adéquation marché de la bêta.

### 1.3 Fonctionnement (vue “utilisateur externe”)

1. L’utilisateur crée un compte (qui lui attribue par défaut un plan Free).
2. Il configure ses sources dans Settings → Sources (flux RSS ou Topics d'intérêt).
3. L'algorithme de Matching & Ranking V1 trie, filtre et nettoie le bruit pour générer un feed unifié.
4. Au clic sur un article, l'utilisateur accède à la synthèse IA (limitée à 3 par jour en Free, illimitée en Pro) et au Trust Factor (notre indicateur de fiabilité).
5. L'utilisateur peut basculer vers l'offre Pro (4,99 €/mois) pour débloquer la puissance totale de l'outil.

---

## 2. Rôles utilisateurs & Structure Tarifaire

| Role name | Modèle Économique | Description / Droits dans la Bêta |
| :--- | :--- | :--- |
| **Utilisateur Beta (Free)** | **0 €** | Accès au feed unifié, gestion des sources RSS. Bridé à 3 synthèses IA automatiques par jour. |
| **Utilisateur Beta (Pro)** | **4,99 € / mois** | Accès illimité aux synthèses IA, alertes en temps réel et détails complets du Trust Factor. |
| **Operator (Back-office)** | — | Rôle interne. Déclenche les batchs, simule les quotas et audite l'observabilité. |

---

## 3. Feature table (organisée par parcours utilisateur)

| Feature ID | User role | Feature name | Short description |
| :--- | :--- | :--- | :--- |
| **F1** | Utilisateur Beta | S’inscrire | Créer un compte (web ou mobile) et obtenir une session valide (JWT). |
| **F2** | Utilisateur Beta | Se connecter | Se connecter sur web et mobile (en ligne ou local). |
| **F3** | Utilisateur Beta | Réinitialiser MDP | Déclencher “mot de passe oublié”, recevoir un reset, définir un nouveau MDP. |
| **F4** | Utilisateur Beta | Pages sécurisées | Vérifier que les pages privées sont bloquées sans login (refresh token fonctionnel). |
| **F5** | Utilisateur Beta | Se déconnecter | Logout complet et destruction locale de la session. |
| **F6** | Utilisateur Beta | Ajouter source | Depuis Settings → Sources : ajouter une source RSS ou un topic simulé. |
| **F7** | Utilisateur Beta | Gérer sources | Modifier/supprimer une source, subscribe/unsubscribe pour influencer le feed. |
| **F8** | Utilisateur Beta | Collecte normalisée | Voir des items normalisés (titre/date/source/url) débarrassés des publicités. |
| **F9** | Utilisateur Beta | Feed personnalisé | Consulter le feed unifié basé sur l’algorithme de matching + ranking V1. |
| **F10** | Utilisateur Beta | Charger contenus | Infinite scroll / pagination fluide pour éviter la surcharge cognitive. |
| **F11** | Utilisateur Beta | Rechercher | Rechercher des articles/posts via l'index de recherche local (Search/Discover). |
| **F12** | Utilisateur Beta | Synthèse d’article | Ouvrir un contenu et afficher une synthèse IA concise (génération de valeur brute). |
| **F13** | Operator | Synthèse manuelle | Déclencher manuellement le batch “daily summary” depuis le back-office pour le jury. |
| **F14** | Utilisateur Beta | Trust Factor | Consulter l'indicateur algorithmique de fiabilité (pilier anti-désinformation). |
| **F15** | Utilisateur Beta | Interactions | Like + commentaire + bookmark (persistants) pour l'ancrage communautaire. |
| **F16** | Utilisateur Beta | Profil & Réseau | Accéder au profil, upload d'avatar, système de suivi inter-utilisateurs. |
| **F17** | Operator | Trigger Notification | Envoyer une notification de test (ex: alerte urgente) vers l'application de l'utilisateur. |
| **F18** | Operator | Back-office Ops | Surveiller la santé du système et forcer les états de démo. |
| **F19** | Utilisateur Beta | **Paywall Freemium** | **Nouveau :** Visualiser le blocage après 3 lectures et le tunnel simulé vers l'offre à 4,99 €. |

---

## 4. Critères de succès (Techniques & Validation Marché)

| Feature ID | Key success criteria | Indicator/metric | Result achieved |
| :--- | :--- | :--- | :--- |
| **F1 à F5** | Robustesse des sessions | 100% des routes privées bloquées en mode incognito ; rafraîchissement transparent. | TBD |
| **F6 & F7** | Intuitivité de la configuration | Taux de complétion de l'ajout d'une source sans assistance externe >= 80%. | TBD |
| **F9 & F10** | Performance du flux | Temps de chargement initial du feed (p95) < 2 secondes. Zéro doublon d'article. | TBD |
| **F12** | Efficacité de la synthèse IA | Temps de réponse du LLM < 5 secondes ; taux de résumés vides ou tronqués = 0%. | TBD |
| **F14** | Pertinence du Trust Factor | Affichage systématique du score basé sur l'origine et le recoupement des données. | TBD |
| **F19** | **Intention d'achat (Prix)** | **>= 15%** des utilisateurs gratuits cliquent sur "Passer Pro" face au blocage. | TBD |
| **MKT-1** | **Gain de temps (Valeur)** | **>= 75%** des testeurs confirment via le formulaire avoir optimisé leur temps de veille. | TBD |

---

## 5. Scénarios de démonstration (orientés preuve jury)

### Scenario A — Preuve F4 : Routes protégées & Persistance
1. Ouvrir un onglet en navigation privée et tenter d'accéder à `/app/feed`.
2. **Attendu :** Redirection immédiate vers `/login` (sécurité prouvée).
3. Connexion, puis réduction artificielle de la durée de vie du token à 30 secondes via le back-office.
4. **Attendu :** L'utilisateur continue de naviguer de manière fluide, le token se rafraîchit en tâche de fond.

### Scenario B — Preuve F14 : Le Trust Factor en action (Anti-Fake News)
1. L'utilisateur sélectionne un article provenant d'une source alternative non vérifiée.
2. **Attendu :** Le Trust Factor affiche un score "Bas" ou "Orange" (ex: 35/100).
3. Cliquer sur le badge pour afficher le détail : l'outil indique *Métémetadonnées incomplètes* et *Zéro recoupement détecté*.
4. L'utilisateur clique sur "Signaler une incohérence" (Inspiration Note de communauté).

### Scenario C — Preuve F19 : Déclenchement du Paywall Freemium (La preuve business)
1. Se connecter avec le compte `jury_free@mail.com`.
2. Consulter successivement 3 articles et générer leurs synthèses IA.
3. Tenter d'ouvrir un 4ème article.
4. **Attendu :** Le résumé est flouté. Un écran s'interpose : *« Vous avez atteint votre limite quotidienne de 3 synthèses. Gagnez du temps en illimité pour seulement 4,99€/mois. »*
5. Cliquer sur "Simuler l'abonnement". Le compte bascule instantanément en mode Pro et libère la fonctionnalité.

---

## 6. Algorithme du Trust Factor (Formalisé & Crédible)

Pour répondre aux exigences de transparence et éviter l'effet "boîte noire", le Trust Factor V1 calcule un score d'intégrité de l'information basé sur trois piliers mathématiques configurés dans notre pipeline d'ingestion :

### 1. La formule de calcul (V1 objective)

Le score final sur 100 est défini par la pondération suivante :

Score = (M * 0.3) + (R * 0.5) + (W * 0.2)

Avec :
* **M (Qualité des Métadonnées - 30%) :** Présence d'un auteur identifié, d'une date de publication valide, d'une URL sécurisée (HTTPS) et d'une structure de flux standardisée.
* **R (Recoupement Algorithmique - 50%) :** Extraction des entités nommées et des mots-clés du titre. Notre algorithme scanne la base de données des dernières 48 heures pour vérifier si la même information est partagée par d'autres canaux indépendants.
* **W (Pondération de la Whitelist - 20%) :** Une base de données interne répertorie les agences de presse officielles et les revues à comité de lecture (ex: AFP, Reuters, Nature). Une source issue de cette liste obtient un bonus automatique.

### 2. Modération & Approche Communautaire (Style Notes de Communauté)
Si un utilisateur Beta Expert détecte une hallucination de l'IA ou un biais flagrant, il peut soumettre un signalement qualifié. Dès que 3 utilisateurs concordent sur un rapport, le score global du Trust Factor subit un malus automatique, agissant comme un **Trustpilot de l'information en temps réel**.

---

## 7. Back-office / QA / Ops (Le cockpit de contrôle)

L'interface opérateur (`ops_beta@mail.com`) est l'outil de démonstration pour le jury. Elle isole la couche technique des aléas temporels :
* **Bouton "Simuler la fin des quotas" :** Permet à l'évaluteur de vider les droits de lecture d'un compte de test pour observer le déclenchement immédiat de la barrière de paiement (Paywall).
* **Bouton "Exécuter le pipeline d'ingestion" :** Force l'aspiration immédiate de nouveaux flux RSS pour démontrer en direct le fonctionnement du Matching, du Ranking et du calcul du Trust Factor.
* **Console d'observabilité :** Permet de visualiser les logs d'exécution des LLM et les latences exactes de génération des résumés.

---

## Annexe A — Matrice de traçabilité (63 tâches MVP -> Spécifications Bêta)

| N° Tâche | Libellé Technique de la Tâche | Fonctionnalité Bêta | Impact Stratégique / Business |
| :--- | :--- | :--- | :--- |
| **3** | Auth backend (Inscription/Connexion + JWT) | F1, F2, F4 | Sécurisation de l'accès et isolation des données utilisateurs. |
| **8** | Résumé quotidien batch | F13 | Valeur d'usage quotidienne (le rendez-vous de l'info). |
| **9** | Pipeline tri articles (Ranking V1) | F9 | Élimination de l'addiction au scroll (tri par premier critère). |
| **12** | Trust Factor V1 | F14 | Différenciation concurrentielle majeure (Lutte Fake-News). |
| **24** | Configuration sources Settings (web) | F7, F19 | Point d'entrée de la personnalisation et de la gestion des quotas. |
| **37** | Gestion token refresh (web) | F4 | Rétention utilisateur (expérience fluide sans reconnexions). |
| **46** | Settings mobile (Abonnement) | F16, F19 | Affichage de l'état du plan (Free vs Pro) et upgrade. |
| **61** | Notifications in-app | F17 | Réengagement de la cible Prosumer face aux urgences de veille. |
