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
| --- | --- | --- | --- |
| **1** | Activer connexion Login au Backend Prod (web) | F2 | Premier point de contact utilisateur, fluidification du tunnel d'accès web. |
| **2** | Modèle de données initial (PostgreSQL) | F8, F18 | Solidité architecturale, intégrité des données utilisateurs et des préférences de veille. |
| **3** | Auth backend (Inscription/Connexion + JWT) | F1, F2, F4 | Sécurisation de l'accès et isolation des données utilisateurs. |
| **4** | CRUD Sources (RSS + Twitter placeholder) | F6 | Personnalisation de l'expérience, premier levier d'engagement de la cible. |
| **5** | Ingestion RSS + normalisation items | F8 | Suppression du bruit publicitaire, valeur brute délivrée via un contenu propre. |
| **6** | Endpoint aperçu données collectées | F8 | Outil QA interne pour valider la qualité de la donnée avant diffusion. |
| **7** | Service Résumé IA simple | F12 | Promesse de base du produit : gain de temps immédiat par la synthèse automatique. |
| **8** | Résumé quotidien batch | F13 | Valeur d'usage quotidienne (le rendez-vis de l'info). |
| **9** | Pipeline tri articles (Ranking V1) | F9 | Élimination de l'addiction au scroll (tri par premier critère). |
| **10** | Matching utilisateur ↔ articles | F9 | Hyper-pertinence du feed, réduction du taux de churn des utilisateurs exigeants. |
| **11** | Résumé automatique articles | F12 | Automatisation de la valeur, réduction de la charge cognitive à l'ouverture. |
| **12** | Trust Factor V1 | F14 | Différenciation concurrentielle majeure (Lutte Fake-News). |
| **13** | Exposition Trust Factor API | F14 | Disponibilité multi-plateforme de la brique de confiance exclusive de Syntheza. |
| **14** | Benchmark solutions LLM | F12, F18 | Maîtrise des coûts de serveurs (marge de 92%) et optimisation de la latence. |
| **15** | Endpoint Feed unifié | F9 | Centralisation multi-sources permettant d'unifier l'expérience de veille. |
| **16** | Like backend | F15 | Création d'un premier niveau d'engagement et récolte de signaux d'intérêt. |
| **17** | Commentaires backend | F15 | Rétention sociale, animation de la communauté de veilleurs professionnels. |
| **18** | Password Lost Reset API | F3 | Réduction de la friction de reconnexion, rétention des utilisateurs actifs. |
| **19** | Recherche articles V1 | F11 | Outil de productivité pour retrouver instantanément une info archivée ou chaude. |
| **20** | Configuration sources backend | F7 | Flexibilité du catalogue de veille, adaptation fine aux pivots sectoriels de l'utilisateur. |
| **21** | Stabilisation backend | F18 | Fiabilité technique indispensable pour la crédibilité auprès d'un public B2B. |
| **22** | Page Password Lost complète (backend) | F3 | Autonomie de l'utilisateur sur le Web, diminution des demandes de support. |
| **23** | Composant Trust Factor (web) | F14 | Transparence algorithmique immédiate sur grand écran pour rassurer le professionnel. |
| **24** | Configuration sources Settings (web) | F7, F19 | Point d'entrée de la personnalisation et de la gestion des quotas. |
| **25** | Pagination infinite scroll Feed/Discover (web) | F10 | Confort de navigation web sans chargement de page forcé, contrôle du flux de données. |
| **26** | Bookmark posts (web) | F15 | Création d'une bibliothèque de connaissances personnelle, valeur d'usage long terme. |
| **27** | Recherche Search onglet manquant (web) | F11 | Complétude de l'expérience de bureau pour une recherche transversale fluide. |
| **28** | Follow/Unfollow (web) | F16 | Mécanisme de viralité in-app permettant de suivre les experts de son secteur. |
| **29** | Upload photo profil (web) | F16 | Personnalisation de l'identité visuelle de l'utilisateur sur la plateforme. |
| **30** | Changement mot de passe Settings (web) | F3 | Sécurité du compte utilisateur de bout en bout depuis l'espace personnel web. |
| **31** | Navigation mobile UI (web responsive) | F18 | Accessibilité cross-device pour l'utilisateur de bureau en déplacement temporaire. |
| **32** | Responsive mobile global (web) | F18 | Uniformisation de l'image de marque et flexibilité d'affichage multi-écrans. |
| **33** | API Password Reset (web) | F3 | Sécurisation technique du protocole de récupération de compte web. |
| **34** | API Trust Factor (web) | F14 | Intégration de l'affichage du score de confiance sur le client web. |
| **35** | API Sources (web) | F6, F7 | Synchronisation instantanée des ajouts de flux RSS depuis le tableau de bord web. |
| **36** | Protection routes auth (web) | F4 | Protection de la propriété intellectuelle de l'application et sécurisation de l'espace abonné. |
| **37** | Gestion token refresh (web) | F4 | Rétention utilisateur (expérience fluide sans reconnexions). |
| **38** | Gestion erreurs globales UI (web) | F18 | Maintien de l'image de marque (zéro écran blanc d'erreur) en cas d'anomalie réseau. |
| **39** | Logout complet (web) | F5 | Respect strict de la confidentialité des sessions sur les ordinateurs partagés. |
| **40** | Auth mobile | F1, F2, F4 | Point d'entrée clé de l'application mobile, sécurisation de la session sur smartphone. |
| **41** | Password Lost mobile | F3 | Autonomie de récupération de compte directement depuis le smartphone de l'utilisateur. |
| **42** | Feed mobile | F9 | Cœur de l'usage nomade, consultation ultra-rapide de l'actualité filtrée. |
| **43** | Infinite scroll mobile | F10 | Ergonomie mobile fluide optimisée pour l'analyse rapide des titres à la volée. |
| **44** | Discover mobile | F10 | Moteur de sérendipité, découverte de nouvelles sources pour enrichir sa veille. |
| **45** | Search mobile | F11 | Accès immédiat à l'information ciblée, même en situation de mobilité. |
| **46** | Settings mobile | F16, F19 | Affichage de l'état du plan (Free vs Pro) et upgrade. |
| **47** | Sources mobile | F6, F7 | Possibilité de capturer et d'ajouter un flux RSS directement depuis son mobile. |
| **48** | Like mobile | F15 | Interaction tactile rapide facilitant le feedback utilisateur instantané. |
| **49** | Commentaires mobile | F15 | Participation active aux débats sectoriels en direct depuis son smartphone. |
| **50** | Trust Factor mobile | F14 | Prise de décision instantanée dans les transports : savoir si l'info est fiable en un coup d'œil. |
| **51** | Bookmark mobile | F15 | Sauvegarde instantanée d'un article pour une lecture approfondie plus tard au bureau. |
| **52** | Follow mobile | F16 | Extension de son réseau professionnel in-app depuis son appareil mobile. |
| **53** | Subscribe mobile | F7 | Activation instantanée du suivi de nouveaux flux d'information sur mobile. |
| **54** | Upload avatar mobile | F16 | Utilisation de l'appareil photo/galerie mobile pour humaniser le profil. |
| **55** | Password change mobile | F3 | Sécurisation et modification rapide des identifiants à la volée. |
| **56** | Profile mobile | F16 | Vitrine personnelle de l'utilisateur synthétisant ses centres d'intérêt de veille. |
| **57** | Connexion prod mobile | F2 | Connexion au serveur de production pour tester l'application en conditions réelles. |
| **58** | UX/UI mobile | F18 | Confort visuel (Slow Content) limitant la fatigue cognitive de l'utilisateur nomade. |
| **59** | Stabilisation Beta mobile | F18 | Réduction des crashs sur mobile (KPI clé pour la rétention en phase de bêta). |
| **60** | Ingestion Twitter stub | F8 | Preuve de concept technique pour l'extension future vers la veille sur les réseaux sociaux. |
| **61** | Notifications in-app | F17 | Réengagement de la cible Prosumer face aux urgences de veille. |
| **62** | Observabilité logs | F18 | Supervision de l'infrastructure, détection proactive des anomalies avant impact client. |
| **63** | Documentation technique MVP | F18 | Alignement de l'équipe technique et pérennité du code pour les futures itérations du produit. |
