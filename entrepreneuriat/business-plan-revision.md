# Business plan révisé : version d'octobre 2026

**Version : v2.0 du 15/10/2026.** Document de séance d'octobre (objectif obligatoire 2 : « Prove someone will pay », volet « Rebuild your business plan »). Il remplace, pour les chiffres, le business plan simple d'octobre (`Syntheza_Business_Plan_Simple_185.pptx`) dont il corrige les incohérences (section 7), et il documente ce qui a changé depuis le modèle de juin 2026 (section 1).

**Règles de lecture.**
- **Aucun chiffre n'est présenté comme facturé.** Aucune facture n'a été jointe au dépôt à ce jour : toute ligne de coût porte « facture à joindre » et la section 4 liste exactement les pièces à récupérer.
- Un prix externe cite sa source, son lien et la date de lecture (tous lus le **15/10/2026**). Un prix sans source solide porte « à vérifier ».
- Une valeur que l'équipe a choisie sans donnée observée est étiquetée **hypothèse**. Au 15/10/2026, aucun entretien, aucune démo, aucun pilote ni aucun contrat n'est consigné ([`prospection.md`](./prospection.md) §11) : **toute la partie revenu est hypothèse.**
- Conversions USD vers EUR : 1 EUR = 1,1195 USD, taux de référence BCE du 14/10/2026 (https://www.ecb.europa.eu/stats/eurofxref/eurofxref-daily.xml, lu le 15/10/2026).
- Tous les calculs sont recomptés par le script de l'annexe A ; les résultats cités ici en sont la sortie.
- Dépôt public : aucun nom de prospect, e-mail ni téléphone.

---

## 1. Trace des changements : juin 2026 vers octobre 2026

> Exigence du guide : « A revised version produced after the seminar, dated, with a record of what changed from the previous version and why. »

**Version précédente (juin 2026)** : Free / Pro à 4,99 € par mois / Business sur devis, cible freelances, consultants, entrepreneurs ([`produit-et-roadmap.md`](../contexte/produit-et-roadmap.md), « Modèle commercial »). **Version d'octobre** : pivot B2B, licences par siège, pilote de 30 jours (pptx d'octobre ; [`centre-operations.md`](../contexte/centre-operations.md), « Décision produit » et « Offres et types de compte »).

| # | Ce qui change | Juin 2026 | Octobre 2026 | Pourquoi | Source |
|---|---|---|---|---|---|
| 1 | Modèle dominant | B2C : freelances, consultants, entrepreneurs, cadres, étudiants | **B2B** : décideurs professionnels, P1 trading et gestion d'actifs | Le segment « consultant » n'avait aucune preuve de paiement (« pas de preuve suffisante pour annoncer un segment gagnant »). Le problème (décider sur une info avec de l'argent en jeu) a un acheteur identifiable et un budget data existant (pptx diapo 8). | produit-et-roadmap.md « Cibles et usages » ; pptx diapos 3, 7, 8 ; centre-operations.md |
| 2 | Offre | Free / Pro 4,99 € / Business sur devis (Expert 50 € abandonné) | **Pro 185 €, Équipe 140 € (5 sièges min.), Entreprise sur devis (environ 2 000 €/mois)** ; plus de plan gratuit : il est remplacé par un pilote de 30 jours | Un prix à 4,99 € ne couvre ni le temps de support ni le coût de l'équipe (section 5) ; le gratuit « ne prouve pas une intention de payer ». | produit-et-roadmap.md ; centre-operations.md « Offres » |
| 3 | Unité de facturation | Abonnement individuel mensuel | **Siège** nommé, au sein d'une **organisation** ; contrat annuel | Les acteurs identifiés facturent au siège ou à l'utilisateur (section 3) ; l'achat B2B se décide par équipe et par budget annuel. | pptx diapo 12 ; centre-operations.md (modèle `Organization`) |
| 4 | Paiement | Paywall et Stripe envisagés (aucun raccordement trouvé) | **Pas de paiement en ligne** : offres attribuées dans le back-office, facturation hors plateforme. Le site public affiche « Coming soon » sans prix | Décision du propriétaire (10/10) ; un contrat B2B se signe et se facture, il ne se paie pas par carte. | centre-operations.md « Offres », « Site public » |
| 5 | Engagement d'entrée | Inscription libre | **Pilote de 30 jours** avec critères de succès écrits à J0, NPS à J+7 et J+25, décision à J+30 | Réduire le risque perçu par l'acheteur et obtenir un engagement documenté (guide : « a pilot accepted ») | prospection.md §7 |
| 6 | Acquisition | Organique : réseau Epitech, LinkedIn, SEO, Product Hunt ; tunnel 300 comptes vers 15 Pro (5 %) | **Outbound ciblé** : liste de 44 comptes (40 P1), tunnel 300 comptes, 60 réponses, 25 démos, 12 pilotes, 8 contrats | Un acheteur B2B ne s'inscrit pas seul sur un site ; il faut le joindre. | pptx diapo 13 ; prospection.md §1 à §4, §11 |
| 7 | Valeur vendue | « Couverture × digestion » : résumés et score de confiance par IA (Gemini) | **Temps réel, signal, traçabilité** : Trust Factor déterministe sans IA, journal de décisions scellé, alertes, brief. Syntheza informe et ne recommande jamais (AMF) | Une promesse de « ne rien rater » n'est pas mesurable ; un acheteur pro paie pour la vitesse et la piste d'audit. | centre-operations.md « Moteur de signal » |
| 8 | Coût variable | 0,40 €/Pro/mois (hypothèse de juin, non mesurée) | **Budget IA à 0 depuis le 10/10/2026** ; coût de livraison = hébergement + temps de support (section 3, axe 3) | Décision du 10/10 : aucun budget IA. Les appels Gemini ne peuvent plus partir. | centre-operations.md « Coût IA nul » |
| 9 | Sourcing | RSS élargi, 25 à 90 €/mois de sourcing indicatif | Catalogue de 317 éditeurs et 677 flux, connecteurs client (IMAP, JSON, e-mail entrant) ; pas de Reuters, AFP ni AP ; environ 45 flux en 403 depuis l'hôte OVH | Les licences de rediffusion de presse ne se négocient pas avant un client ; le client branche son propre abonnement. | centre-operations.md « Limites connues » |
| 10 | Seuil de rentabilité | « 21 Pro couvrent 96,39 €/mois de charges » | **Recalculé** : section 5 | Le seuil de juin ne comptait ni le temps de l'équipe ni les nouveaux coûts. | produit-et-roadmap.md « Coûts et seuil » |
| 11 | Scénarios d'ARR | 898 €, 5 393 €, 16 781 € (15, 60+1, 160+4 abonnés) | pptx : 70 k€ à 31 sièges ; **scénario central v2 : ARR de 39 960 € à 18 sièges en sept. 2027** | Le calendrier tient compte du cycle réel (pilote puis contrat, section 6). | produit-et-roadmap.md ; section 6 |
| 12 | Roadmap commerciale | P3 monétisation oct.-déc. 2026 (environ 20 Pro) ; P4 60 Pro ; P5 160 Pro et 4 Business | Pilotes en T1 2027 ; extension P2 dès juin 2027 ; P3 après PGE5 | La roadmap de juin supposait du volume B2C. | produit-et-roadmap.md ; pptx diapo 8 |
| 13 | Périmètre produit | Web et app iOS (TestFlight) | Web B2B ; **mobile hors périmètre** du lot d'octobre | Priorité au poste de travail du décideur. | centre-operations.md |
| 14 | Hors cible explicite | non défini | Traders particuliers, étudiants, VC en amorçage, contrôle de gestion | « Pas de budget pro ou pas de décision en temps réel » | pptx diapo 8 |
| 15 | Groupes de métiers | 4 cibles (freelances et consultants, cadres et veilleurs, étudiants, équipes et PME) | 6 groupes au pptx ; **un 7e (Direction générale) ajouté le 10/10** après audit, absent du pptx | Audit par métier du 10/10 | centre-operations.md ; pptx diapo 7 |

**Ce qui n'a pas changé** : l'équipe de trois, la base technique (Node, Prisma, PostgreSQL, React), l'hébergement OVH, l'interdiction de scraper LinkedIn, le principe « informer sans recommander ».

---

## 2. Modèle de revenus

> Exigence du guide : « A revenue model settled and justified: subscription, pay-per-use, one-off purchase, commission, licence, free but funded another way. »

**Modèle retenu : licence par siège, contrat annuel, précédé d'un pilote de 30 jours.** Trois offres : Pro (siège unitaire), Équipe (à partir de 5 sièges, tableaux partagés, webhooks), Entreprise (API, SSO, engagement de service, sur devis).

| Modèle envisagé | Verdict | Raison |
|---|---|---|
| **Licence par siège, contrat annuel** | **Retenu** | Le besoin est récurrent (chaque séance) ; le coût de livraison est quasi fixe (hébergement), donc le revenu croît avec les sièges sans coût proportionnel ; l'unité « siège » est celle des acheteurs de référence (Bloomberg au terminal, Benzinga Pro à l'utilisateur, section 3) ; un contrat annuel s'inscrit dans un cycle budgétaire et donne de la visibilité de trésorerie. |
| Abonnement mensuel individuel (juin) | Abandonné | Aucune preuve de paiement du segment ; 4,99 € ne couvre pas le temps de support. |
| Paiement à l'usage | Écarté | Un desk veut un budget fixe ; l'usage (alertes, lectures) est imprévisible pour l'acheteur et complique la facturation hors plateforme. |
| Achat unique | Écarté | Le service est un flux continu (ingestion, signal) : il a un coût récurrent. |
| Commission | Écarté | Aucune transaction financière n'est intermédiée ; Syntheza ne fait pas de conseil (AMF). |
| Gratuit financé autrement | Écarté | Pas de second payeur identifié ; le gratuit est remplacé par le **pilote de 30 jours**. |

**Pilote de 30 jours** : gratuit, mais il engage l'acheteur (30 minutes d'onboarding, critères de succès signés à J0, accès aux données de ses propres actifs). Le guide précise : « An agreement that costs nothing proves nothing. » Le pilote n'est donc retenu comme premier engagement que s'il est confirmé **par écrit** avec les critères de succès ; la conversion en contrat annuel est la preuve de paiement.

**Prérequis non remplis au 15/10/2026** : la société n'est pas immatriculée (les mentions légales du site attendent ses informations d'immatriculation, centre-operations.md « Site public » §« À compléter »). Sans entité, aucune facture ni contrat ne peut être émis ; un pilote accepté par écrit reste possible.

---

## 3. Note de prix

> Exigence du guide : « An explicit price hypothesis: an amount, a billing unit, and how you built it (what your target spends today to solve the problem, what the players you identified charge, what your service costs to deliver). »

### 3.1 Hypothèse de prix

| Offre | Montant (HT) | Unité de facturation | Engagement |
|---|---|---|---|
| **Pro** | **185 €** | par siège nommé et par mois (2 220 € par siège et par an) | contrat annuel après pilote de 30 jours |
| Équipe | 140 € | par siège et par mois, 5 sièges minimum (8 400 € par an pour 5 sièges) | idem |
| Entreprise | sur devis, repère de 2 000 €/mois (24 000 €/an) | par organisation (API, SSO, engagement de service) | idem |

Statut : **hypothèse**. Ces montants viennent du business plan d'octobre, pas d'une mesure. Ils n'ont été soumis à aucun prospect au 15/10/2026. Le site public n'affiche aucun prix (« Coming soon »).

### 3.2 Axe 1 : ce que la cible dépense aujourd'hui

Aucune donnée de première main : **aucun entretien n'a eu lieu**. La question « Quels outils payez-vous aujourd'hui pour l'info, et qui a décidé de l'achat ? » est au guide d'entretien ([`prospection.md`](./prospection.md) §5) ; les réponses remplaceront cette ligne en décembre. En attendant, la dépense de référence est celle des outils que la cible nous cite en objection (« On a déjà Bloomberg / LSEG ») : voir axe 2.

### 3.3 Axe 2 : ce que facturent les acteurs identifiés

Prix publics en USD, convertis en EUR au taux BCE du 14/10/2026 ; lus le 15/10/2026.

| Acteur | Prix relevé | En EUR | Nature et fiabilité de la source |
|---|---|---|---|
| **Bloomberg Terminal** | 2 665 $/mois pour un terminal (31 980 $/an) ; 2 360 $/mois par terminal à partir de deux (28 320 $/an), contrat de 2 ans | 2 380,53 €/mois (1 terminal) ; 2 108,08 €/mois (multi) | Hausse de 6,5 % au 01/01/2025 annoncée par courrier aux clients, rapportée par NeuGroup le 09/10/2024 (https://connect.neugroup.com/public/blogs/bloomberg-terminals-how-much-more-youll-pay-next-year). Repris par Quantify Terminal, mis à jour le 27/09/2026 (https://www.quantifyterminal.com/blog/bloomberg-terminal-cost-2026), qui rappelle que Bloomberg ne publie pas de grille. **Source secondaire** ; contrats négociables. |
| **LSEG Workspace** | Pas de prix public. Vendr : licence de base de 1 500 à 3 000 $ par utilisateur et par mois, hors données (https://www.vendr.com/marketplace/refinitiv). Un recensement cité en recherche donne environ 22 000 $ par utilisateur et par an, version allégée à partir de 3 600 $ (https://www.hudson-labs.com/blog/free-and-low-cost-alternatives-to-bloomberg, extrait de résultat de recherche, page non relue en entier) | 1 340 à 2 680 €/mois (Vendr) ; 19 652 €/an (22 000 $) ; 3 216 €/an (3 600 $) | **À vérifier.** La page Vendr est elle-même incohérente : elle affiche aussi un contrat médian de 4 752 $ par an (35 contrats, de 1 161 à 20 190 $). La fourchette est large : elle n'est utilisable que comme ordre de grandeur. |
| **Dataminr** | Pas de prix public. Offre First Alert (secteur public) : « à partir de 20 000 USD par an », document de tarification G-Cloud 13 du 13/05/2022 (https://assets.applytosupply.digitalmarketplace.service.gov.uk/g-cloud-13/documents/720009/395974893365566-pricing-document-2022-05-13-1320.pdf, extrait vu en résultat de recherche ; la page renvoie 403 en lecture directe). Vendr : contrat médian de 22 000 $ par an, de 15 000 à 62 500 $ (https://www.vendr.com/marketplace/dataminr) | 17 865 €/an (20 000 $) ; 19 652 €/an (22 000 $) | **Contrat, pas siège** ; produits orientés sécurité d'entreprise et secteur public, pas trading. Repère d'ordre de grandeur seulement. |
| **Benzinga Pro** | Basic 37 $/mois ; Streamlined 147 $/mois ; Essential 197 $/mois ; en facturation annuelle 30,58, 124,75 et 166,42 $/mois (https://www.benzinga.com/pro/pricing) | 33,05 ; 131,31 ; 175,97 €/mois (mensuel) ; 148,66 €/mois (Essential annuel) | **Source primaire**, lue le 15/10/2026. Offre orientée traders particuliers (« Join 40,000+ traders »). |
| **Syntheza Pro (hypothèse)** | 185 €/siège/mois | 207,11 $/mois | |

Lecture :
- **Plafond** : un terminal Bloomberg seul coûte 2 380,53 €/mois ; Syntheza Pro représente **7,8 %** de ce prix, soit environ 1/13 (8,8 % face au tarif multi-terminal, 1/11,4). Le pptx annonce « 1/10 » à partir de « 2 000 $/mois » : voir section 7.
- **Plancher** : Benzinga Pro Essential, orienté particuliers, coûte 175,97 €/mois en facturation mensuelle. À 185 €, Syntheza est **5,1 % au-dessus** : le prix de Syntheza ne se justifie face à Benzinga que par la cible (équipes pro), la traçabilité et le Trust Factor, pas par un prix plus bas.
- Aucune preuve que 185 € soit le prix acceptable : c'est une hypothèse critique à tester en entretien (note sur les hypothèses critiques, livrable séparé).

### 3.4 Axe 3 : ce que coûte le service à délivrer

Voir le calcul du script (annexe A, sections 6 et 7) et la structure de coûts (section 4). Coût de livraison par siège à 18 sièges (fin sept. 2027 du scénario central) :

| Composante | Calcul | €/siège/mois | Statut |
|---|---|---|---|
| Coûts cash (hébergement, Apple, e-mail, outils) | 108,58 € / 18 sièges | 6,03 | hypothèse de budget, facture à joindre |
| Support et accompagnement | 2 h par client et par mois × 30 €/h / 3 sièges | 20,00 | **hypothèse** (2 h) et **hypothèse** (30 €/h) |
| **Total** | | **26,03** | |
| Marge brute à 185 € | (185 − 26,03) / 185 | **85,9 %** | |

Coût complet (temps de l'équipe valorisé, section 5) : il faut **28 sièges** pour qu'un prix de 180,66 €/siège/mois couvre tous les coûts. **À 18 sièges, le prix de revient complet serait de 281,03 €**. Le prix de 185 € n'est donc rentable, temps valorisé, qu'à partir de 28 sièges (point mort, section 5).

**Choix retenu** : maintenir 185 €/siège/mois HT pour le Pro jusqu'aux premiers entretiens. Il se situe entre le plancher retail (176 €) et le plafond terminal (2 109 à 2 381 €), et couvre le coût de livraison avec 86 % de marge brute. Ajustements prévus en décembre selon les objections de prix collectées.

---

## 4. Structure de coûts

> Exigence du guide : « A cost structure: development costs including your own time valued, recurring costs, and what you actually pay out every month (usage-billed external services, tools, hosting). Taken from real invoices, not estimated. »

**Constat honnête : le montant réellement payé chaque mois n'est pas établi.** Aucune facture n'est dans le dépôt. Les montants ci-dessous sont soit des **tarifs publics** lus le 15/10/2026 (indicatifs, non facturés), soit des **hypothèses**. Le tableau 4.2 reste à compléter avec les factures ; l'exigence « Taken from real invoices » n'est pas satisfaite tant que la liste 4.3 n'est pas jointe.

### 4.1 Coût de développement : temps de l'équipe valorisé

Aucun relevé d'heures n'existe. Les dépôts de code ne permettent pas de reconstituer le temps passé (les commits sont groupés : par exemple 5 jours de commit pour un membre sur le backend alors que le travail d'octobre est considérable). La valorisation ci-dessous est donc une **hypothèse** à remplacer par une déclaration individuelle d'heures.

| Paramètre | Valeur | Statut |
|---|---|---|
| Taux horaire valorisé | **30 €/h** (sensibilité : 20 €/h et 46 €/h) | **hypothèse**. Le repère haut de 46 €/h vient de la note interne de mai (data engineer à 7 000 € brut chargé par mois, divisé par 151,67 h : `veille_scraping_comparatif.md` §7.3, hypothèse interne non sourcée) |
| Membres | 3 | fait |
| Début traçable | 12/03/2025 (premier commit, `frontend-web`) | fait (git) |
| Durée jusqu'au 15/10/2026 | 83,1 semaines | calcul |
| Temps par membre et par semaine, période écoulée | **10 h** (sensibilité : 6 h et 15 h) | **hypothèse** (projet mené en parallèle des études) |

**Développement déjà réalisé** (coût d'opportunité, non décaissé) : 3 × 10 h × 83,1 semaines = **2 494 h**, soit **74 829 €** à 30 €/h (fourchette de 29 931 € à 172 106 € selon les hypothèses du script).

**Douze mois à venir** (oct. 2026 à sept. 2027) : 3 membres × **15 h/semaine** (hypothèse, prospection et exploitation en plus du développement) × **44 semaines** (hypothèse : 52 moins vacances et examens) = 1 980 h, soit 165 h par mois ; à 30 €/h : **4 950 €/mois, 59 400 € sur 12 mois**. Aucun salaire n'est versé (hypothèse du pptx : fondateurs non payés l'année 1) : cette somme est un coût économique, pas une dépense.

### 4.2 Coûts récurrents : services réellement utilisés

Services repérés dans `AGENTS.md`, `exploitation.md`, `centre-operations.md`, `etat-et-recette.md` et le code des workflows. Colonne « Tarif public » : prix catalogue indicatif, **pas un montant payé**. Chaque ligne : **facture à joindre**.

| Service | Usage constaté (source) | Tarif public indicatif (source, lu le 15/10/2026) | Montant payé | Facture à joindre |
|---|---|---|---|---|
| **OVHcloud, VPS (API)** | Hôte de l'API publique `api.syntheza.ovh` : 1,9 Go de RAM, pas de swap, disque de 24 Go (centre-operations.md, incident du 11/10) | VPS-1 (2 vCores, 4 Go, 40 Go) : 3,81 € HT/mois ; VPS-3 (6 vCores, 12 Go, 100 Go) : 10,40 € HT/mois (https://www.ovhcloud.com/fr/vps/). L'hôte actuel (1,9 Go, 24 Go) ne correspond à aucune offre actuelle : **modèle réel à identifier** | inconnu | **oui** |
| **OVHcloud, second VPS (web)** | `technical-overview.md` : « Two OVH VPS (API + web) » ; `README.en.md` : « single-VPS ». **Contradiction** à lever | idem | inconnu | **oui, ou attestation d'un seul serveur** |
| **OVHcloud, options** (sauvegarde, snapshot) | non documenté | sauvegarde de 1,10 € HT/mois, snapshot de 0,30 € HT/mois (même page) | inconnu | **oui** |
| **Domaine `syntheza.ovh`** | URL publique du site, de l'API et des adresses du domaine (AGENTS.md §3.1) | prix du .ovh non retrouvé sur le site OVH en lecture automatique : **à vérifier** | inconnu | **oui** (enregistrement et renouvellement) |
| **Cloudflare** | DNS et proxy devant le site (AGENTS.md §3.1) ; Worker d'e-mail cité en option client | plan gratuit à 0 $/mois ; Pro 20 $/mois (annuel) ; Business 200 $/mois (https://www.cloudflare.com/plans/). **Plan réel à confirmer** | inconnu | **oui, ou capture datée « Plan : Free »** |
| **Brevo (SMTP)** | Expéditeur vérifié pour les e-mails de réinitialisation, d'alerte, de brief et d'invitation ; recette du 09/09 (etat-et-recette.md F3) ; variables `SMTP_*` du workflow backend | plan Free inclus ; Starter 7 €/mois ; Standard 15 €/mois (https://www.brevo.com/pricing/, page générée le 29/09/2026) | inconnu | **oui, ou capture du plan** |
| **Apple Developer Program** | Compte requis par `testflight.yml` (build, upload App Store Connect, TestFlight) ; Sign in with Apple | **99 USD par an** soit 88,43 € (https://developer.apple.com/support/enrollment/ et https://developer.apple.com/programs/whats-included/) | inconnu | **oui** (reçu Apple, date d'adhésion, titulaire du compte) |
| **GitHub** (organisation Syntheza-DEV : dépôts privés, GHCR, Actions) | Les cinq dépôts, la CI et le registre d'images (AGENTS.md §2, §3.2) ; build iOS sur runner macOS | runner macOS standard : 0,062 $/minute ; Linux 2 cœurs : 0,006 $/minute (https://docs.github.com/en/billing/reference/actions-runner-pricing). **Plan de l'organisation inconnu** | inconnu | **oui** (facturation et rapport d'usage Actions) |
| **Expo / EAS** | `eas build --local` sur le runner GitHub, donc pas de crédit de build cloud ; secret `EXPO_TOKEN` (exploitation.md) | Free 0 $ ; Starter 19 $/mois ; Production 199 $/mois (https://expo.dev/pricing). **Plan réel à confirmer** | inconnu | **oui, ou capture du plan** |
| **Google Gemini (API)** | Modèle `gemini-2.5-flash` (backend-et-moteur.md). **Budget IA fixé à 0 USD depuis le 10/10/2026** : aucun appel ne peut partir | **Une facture d'environ 16 €** a été rapportée en juin 2026 (backend-et-moteur.md « Historique rapporté, juin 2026 ») : non vérifiée sur pièce | 0 € attendu depuis le 10/10 ; juin à octobre inconnu | **oui** (factures Google Cloud ou AI Studio de juin à octobre, et capture du budget à 0) |
| **Google OAuth** | Connexion Google (web et iOS) | aucun frais connu | 0 € attendu | non (aucune facturation prévue) |
| **Traefik, Let's Encrypt, Docker, PostgreSQL** | TLS et conteneurs sur l'hôte | logiciels libres | 0 € | non |
| **Meta (Instagram Graph API), Discord** | Circuit de publication éditorial, inerte faute de variables `DISCORD_INSTAGRAM_*` dans le workflow (AGENTS.md §3.2, point 6) | aucun frais connu | 0 € attendu | non |
| **Agent Hermes** | Auteur de commits (backend, 28 et 29/07/2026) ; « travaux Hermès » cités dans produit-et-roadmap.md | hébergement et modèle utilisés **non documentés** | inconnu | **oui, après inventaire** |
| **LinkedIn** (abonnement de prospection) | Le pptx (diapo 14) prévoit 50 €/mois ; aucun abonnement n'est attesté | prix de l'abonnement non retrouvé : **à vérifier** | inconnu | **oui, ou attestation « aucun abonnement »** |
| **Proxys de sortie** | `OUTBOUND_PROXY_URL` facultatif, **inerte** tant qu'aucun proxy n'est fourni ; environ 45 flux en 403 depuis l'hôte | le pptx prévoit environ 30 €/mois : non sourcé | 0 € attendu | oui si souscrit |
| **Outils de développement** (assistants IA, IDE, design) | **Non documentés** dans les sources lues | non applicable | inconnu | **oui, après inventaire** |

**Coûts hors services, non chiffrés faute de devis ni de facture** : création de la société (statuts, greffe), avis d'avocat sur la presse et l'AMF (prévu avant janvier 2027, pptx diapos 16 et 17), comptabilité, assurance responsabilité civile professionnelle, billets d'événements (AGEFI AM Tech Day 03/11, Forum CCO 05/11, AFTE 26 et 27/11, Digital Finance Summit 03/12 : aucune inscription faite, conditions à demander, prospection.md §3). Leur absence rend les totaux ci-dessous **minorants**.

**Budget de référence retenu pour les calculs** (hypothèse, à remplacer par les factures) :

| Ligne | €/mois | Hypothèse |
|---|---|---|
| OVH : VPS-3 (API) + VPS-1 (web) | 14,21 | prix publics HT (10,40 + 3,81), dimensionnés après l'incident du 11/10 (1,9 Go de RAM) |
| Apple Developer | 7,37 | 99 USD divisés par 1,1195 puis par 12 |
| Brevo Starter | 7,00 | prudence : plan réel inconnu |
| LinkedIn | 50,00 | valeur du pptx, abonnement non attesté |
| **Total, oct. à déc. 2026** | **78,58** | |
| Proxys de sortie (dès janv. 2027) | 30,00 | valeur du pptx, nécessité due aux 403 (centre-operations.md) |
| **Total, dès janv. 2027** | **108,58** | |

Le pptx annonçait 225 € de coûts mensuels en année 1 (et affichait 325 €, voir section 7) : ces lignes (« API de news à 90 € », « IA open source ») ne correspondent pas à ce que le code utilise (RSS uniquement, IA coupée).

### 4.3 Factures à récupérer (liste précise)

À joindre au dépôt de rendu (hors dépôt public si elles portent des données personnelles) avant la séance de décembre. Pour chacune : période couverte du premier achat à aujourd'hui, montant HT et TTC, payeur (carte d'un membre, association, école : **à préciser**).

| # | Fournisseur | Pièce à récupérer | Où |
|---|---|---|---|
| 1 | OVHcloud | Toutes les factures du ou des VPS (API, et web s'il existe), options de sauvegarde et snapshots comprises, depuis la première commande | Manager OVHcloud, Facturation |
| 2 | OVHcloud | Facture d'enregistrement et de renouvellement du domaine `syntheza.ovh` | idem |
| 3 | Cloudflare | Facture du plan de la zone `syntheza.ovh`, ou capture datée du plan « Free » | Tableau de bord, Billing |
| 4 | Brevo | Factures du compte SMTP, ou capture datée du plan (Free ou payant) et du volume d'envois | Compte Brevo, Plan et facturation |
| 5 | Apple | Reçu de l'Apple Developer Program (99 USD) et date de la première adhésion ; nom du titulaire du compte | Reçu Apple, App Store Connect |
| 6 | GitHub | Factures de l'organisation Syntheza-DEV, rapport d'usage Actions (minutes macOS et Linux), stockage GHCR | Organization, Settings, Billing |
| 7 | Expo | Plan du compte et rapport d'usage EAS, ou capture « Free » | expo.dev, Billing |
| 8 | Google Cloud ou AI Studio | Factures Gemini de juin à octobre 2026 (dont la facture d'environ 16 € de juin), et capture du budget IA à 0 | Console de facturation du projet Gemini |
| 9 | LinkedIn | Facture d'un éventuel abonnement Premium ou Sales Navigator ; sinon attestation écrite « aucun abonnement » | Compte LinkedIn |
| 10 | Hébergeur de l'agent Hermes et de ses modèles | Factures, après identification du fournisseur | à identifier |
| 11 | Outils de développement (assistants IA, design, IDE) | Factures de chaque abonnement payant utilisé pour le projet | inventaire par membre |
| 12 | Proxys de sortie | Facture si un proxy a été souscrit ; sinon aucune | fournisseur à identifier |
| 13 | Greffe, avocat, expert-comptable, assureur | Devis ou factures de création de société, d'avis juridique, de comptabilité, d'assurance | à demander |
| 14 | Organisateurs d'événements | Tarifs d'inscription des quatre événements de novembre et décembre | sites officiels |
| 15 | Équipe (3 membres) | **Relevé d'heures** déclaré par membre depuis le 12/03/2025 (pièce justificative du temps valorisé, section 4.1) | à produire |

**Total facturé et justifié à ce jour : 0 €** (aucune pièce jointe).

---

## 5. Point mort

> Exigence du guide : « Break-even point: how many paying customers or users you need to cover your costs, and by when. »

**Formule** : le nombre de sièges Pro payants à l'équilibre mensuel est

N = ⌈ F / (p − v) ⌉

où F est la charge fixe mensuelle (€), p le prix du siège (185 €) et v le coût variable par siège (0,40 €, hypothèse héritée de juin, seule valeur documentée, non mesurée ; elle pèse moins de 1 siège dans tous les cas). Résultats du script (annexe A) :

| Niveau de coûts | F (€/mois) | Sièges Pro nécessaires | Clients de 3 sièges | Date dans le scénario central |
|---|---|---|---|---|
| Coûts cash minimaux (sans LinkedIn ni proxy) | 28,58 | **1** | 1 | février 2027 (premier client facturé) |
| **Coûts cash du budget de référence** | 108,58 | **1** (0,59 siège) | 1 | **février 2027** (CA de 555 € contre 108,58 € de coûts ; le CA cumulé dépasse aussi les coûts cumulés dès février 2027) |
| **Coûts cash + temps de l'équipe valorisé** (165 h/mois à 30 €/h) | 5 058,58 | **28** (27,4) | 9,3 (soit 10 clients) | **février 2028** (extrapolation : 18 sièges en sept. 2027, + 2 sièges par mois, hors de la fenêtre de 12 mois) |
| Même niveau, au prix Équipe de 140 € | 5 058,58 | 37 | | non atteint dans la fenêtre |

**Conclusion** : sur les seuls coûts cash, **un seul siège Pro** couvre les dépenses mensuelles, dès que le premier contrat est facturé (février 2027 dans le scénario central ; il suppose l'immatriculation de la société). Si l'on valorise le temps de l'équipe, il faut **28 sièges Pro**, soit environ 10 clients de 3 sièges, atteints en **février 2028** à rythme constant. Aucun de ces deux seuils n'est une preuve : les deux dépendent de contrats qui n'existent pas.

**Sensibilité du seuil complet** (nombre de sièges Pro ; taux horaire × heures par semaine et par membre ; 44 semaines, 3 membres) :

| Taux \ heures/sem. | 8 h | 15 h | 25 h |
|---|---|---|---|
| 20 €/h | 11 | 19 | 31 |
| **30 €/h** | 15 | **28** | 46 |
| 46 €/h | 23 | 42 | 70 |

---

## 6. Projection à 12 mois (oct. 2026 à sept. 2027)

> Exigence du guide : « A 12-month projection with one central scenario, costed, and every assumption written out one by one. »

### 6.1 Hypothèses du scénario central (une par une)

Toutes sont des **hypothèses** : le jour de la rédaction, il y a 0 pilote, 0 contrat et 0 client.

| N° | Hypothèse | Valeur | Fondement |
|---|---|---|---|
| H1 | Prix du siège Pro | 185 €/siège/mois HT | pptx diapo 12 |
| H2 | Offres vendues | **Pro uniquement** (ni Équipe ni Entreprise) | prudence : le minimum de 5 sièges de l'offre Équipe et l'API Entreprise n'ont aucun prospect ; ce choix **sous-estime** le haut de fourchette |
| H3 | Pilotes lancés | 1 par mois de nov. 2026 à juil. 2027, 2 en août, 1 en sept. : **12 pilotes** | tunnel du pptx (diapo 13 : 12 pilotes) ; cohérent avec les objectifs de [`prospection.md`](./prospection.md) §11 (1, 2, 3 pilotes actifs fin nov., fin déc., fin janv.) |
| H4 | Conversion pilote vers contrat | 2 pilotes sur 3 (les n° 3, 6, 9 et 12 ne convertissent pas) soit **8 contrats** | pptx diapo 13 (12 pilotes, 8 contrats) et diapo 17 (3 pilotes, 2 qui paient) ; **aucune donnée observée** |
| H5 | Délai pilote vers premier mois facturé | **3 mois** (30 jours de pilote, décision, bon de commande, première facture) | pptx diapo 13 (« 2 à 4 mois, pilote inclus ») ; objectif d'un premier contrat en février 2027 (prospection.md §11) |
| H6 | Sièges par client | **3** | 31 sièges pour 11 clients au pptx (diapo 9), soit 2,82, arrondi à 3 |
| H7 | Résiliation, remise, impayé, montée en sièges | aucun(e) | contrat annuel ; simplification |
| H8 | Facturation et encaissement | mensuels, encaissés le mois de la facture | simplification ; une facturation annuelle d'avance améliorerait la trésorerie |
| H9 | Taxes | montants HT | |
| H10 | Hébergement OVH | 14,21 €/mois dès octobre (VPS-3 + VPS-1) | tarifs publics HT du 15/10/2026 ; dimensionnement après l'incident du 11/10 ; facture à joindre |
| H11 | Apple Developer | 7,37 €/mois | 99 USD, taux BCE du 14/10/2026 ; facture à joindre |
| H12 | Brevo | 7,00 €/mois (plan Starter) | prudence ; facture à joindre |
| H13 | LinkedIn | 50,00 €/mois | pptx diapo 14, abonnement non attesté ; facture à joindre |
| H14 | Proxys de sortie | 30,00 €/mois dès janvier 2027 | pptx diapo 14 ; environ 45 flux en 403 (centre-operations.md) ; facture à joindre |
| H15 | IA | 0 € | décision du 10/10/2026 (budget IA à 0) |
| H16 | Société, juridique, comptabilité, assurance, événements | **non chiffrés** (0 € dans les totaux) | aucun devis ; les coûts cash sont un **minorant** |
| H17 | Temps de l'équipe | 3 × 15 h/semaine × 44 semaines, à 30 €/h, soit 4 950 €/mois | hypothèses de la section 4.1 ; coût économique, non décaissé |
| H18 | Rémunération des fondateurs | aucune | hypothèse du pptx (diapo 15) |

### 6.2 Résultat mois par mois

| Mois | Pilotes lancés | Clients facturés | Sièges Pro | CA du mois (€) | Coûts cash (€) | Résultat cash (€) | CA cumulé (€) | Coûts cash cumulés (€) |
|---|---|---|---|---|---|---|---|---|
| oct. 2026 | 0 | 0 | 0 | 0 | 78,58 | -78,58 | 0 | 78,58 |
| nov. 2026 | 1 | 0 | 0 | 0 | 78,58 | -78,58 | 0 | 157,16 |
| déc. 2026 | 1 | 0 | 0 | 0 | 78,58 | -78,58 | 0 | 235,74 |
| janv. 2027 | 1 | 0 | 0 | 0 | 108,58 | -108,58 | 0 | 344,32 |
| févr. 2027 | 1 | 1 | 3 | 555 | 108,58 | 446,42 | 555 | 452,90 |
| mars 2027 | 1 | 2 | 6 | 1 110 | 108,58 | 1 001,42 | 1 665 | 561,48 |
| avr. 2027 | 1 | 2 | 6 | 1 110 | 108,58 | 1 001,42 | 2 775 | 670,06 |
| mai 2027 | 1 | 3 | 9 | 1 665 | 108,58 | 1 556,42 | 4 440 | 778,63 |
| juin 2027 | 1 | 4 | 12 | 2 220 | 108,58 | 2 111,42 | 6 660 | 887,21 |
| juil. 2027 | 1 | 4 | 12 | 2 220 | 108,58 | 2 111,42 | 8 880 | 995,79 |
| août 2027 | 2 | 5 | 15 | 2 775 | 108,58 | 2 666,42 | 11 655 | 1 104,37 |
| sept. 2027 | 1 | 6 | 18 | 3 330 | 108,58 | 3 221,42 | 14 985 | 1 212,95 |

**Chiffre d'affaires à 12 mois : 14 985 €. ARR en fin de période : 39 960 € (18 sièges × 185 × 12). Coûts cash sur 12 mois : 1 212,95 €. Résultat cash : 13 772,05 €. Coût du temps de l'équipe sur la même période : 59 400 € : résultat économique : -45 627,95 € (14 985 − 1 212,95 − 59 400).**

Sur les 12 pilotes, 8 contrats naissent mais **6 seulement sont facturés avant fin septembre 2027** (les pilotes de juillet à septembre facturent à partir d'octobre 2027 et au-delà).

### 6.3 Sensibilité du CA à 12 mois

Une seule hypothèse varie à la fois ; les autres restent celles du scénario central.

| Variante | CA 12 mois (€) | Sièges fin sept. 2027 | ARR (€) |
|---|---|---|---|
| **Central** | **14 985** | 18 | 39 960 |
| 2 sièges par client | 9 990 | 12 | 26 640 |
| 5 sièges par client | 24 975 | 30 | 66 600 |
| Délai de 2 mois | 18 315 | 18 | 39 960 |
| Délai de 4 mois | 11 655 | 15 | 33 300 |
| Conversion 1 pilote sur 2 | 11 100 | 12 | 26 640 |
| Conversion 1 pilote sur 3 | 8 325 | 9 | 19 980 |
| Prix de 140 € | 11 340 | 18 | 30 240 |

Les trois paramètres les plus fragiles (conversion, délai, taille du client) n'ont **aucune** donnée observée ; l'ARR du pptx (70 k€) n'est atteint dans aucune variante.

---

## 7. Incohérences relevées dans le pptx d'octobre

Chaque correction est recalculée par le script (annexe A, section 1).

| # | Diapo | Constat | Recalcul | Correction proposée |
|---|---|---|---|---|
| 1 | 15 | Résultat de l'année 1 affiché **« 2820 k€ »** | 6 720 − 3 900 = **2 820 €**, pas 2 820 k€ (unité erronée d'un facteur 1 000) | Écrire « 2 820 € » (et voir le point suivant) |
| 2 | 14 et 15 | Total mensuel de l'année 1 affiché **325 €** | 55 + 120 + 0 + 50 = **225 €** ; l'année 2 (370 €) et l'année 3 (520 €) se recomptent juste | Corriger en 225 € (ou ajouter la ligne de 100 € qui manque, par exemple les salons marqués « ~ ») ; coûts annuels de 2 700 € au lieu de 3 900 € ; résultat de l'année 1 de 4 020 € avec les hypothèses du pptx |
| 3 | 15 | Coûts de l'année 2 affichés 4 400 € | 12 × 370 = **4 440 €** ; le résultat de 9 000 € est juste avec 4 440 € (13 440 − 4 440), faux avec 4 400 € (9 040) | Afficher 4 440 € |
| 4 | 15 | Le CA de l'année 1 (6 720 € « = 8 clients ») utilise le prix **Équipe** : 8 × 6 mois × **140 €** | À 185 € (offre Pro) : 8 × 6 × 185 = **8 880 €** ; années 2 et 3 : 16 × 6 × 140 = 13 440 € et 40 × 6 × 140 = 33 600 € | Préciser l'offre retenue ; si Pro, 8 880 €. Le minimum de 5 sièges de l'offre Équipe contredit « 8 clients = 8 sièges » |
| 5 | 9 et 15 | Contradiction sur les volumes en fin d'année 1 : **31 sièges et 11 clients** (diapos 9, 12, 13) contre **≈ 8 sièges** (diapo 15) | Aucun scénario ne donne 8 et 31 sièges à la même date | Un seul chiffre : voir le scénario central (6 clients, 18 sièges à fin sept. 2027) |
| 6 | 9 vs 13 | SOM de **11 clients** contre tunnel de **8 contrats** | 8 / 300 = 2,67 % ; à ce taux, 11 contrats exigent 412,5 comptes, pas 300 | Aligner sur 8 contrats (tunnel) ou porter le tunnel à environ 413 comptes |
| 7 | 9 | ARR de « ≈ 70 k€ » pour 31 sièges | 31 × 185 × 12 = **68 820 €** si **tous** les sièges sont Pro ; 52 080 € si tous Équipe. 31 sièges pour 11 clients (2,8) est incompatible avec le minimum de 5 sièges de l'offre Équipe | Indiquer « 68,8 k€ si 31 sièges Pro » ou recalculer selon un mix d'offres avec la formule de la diapo 12 |
| 8 | 9 vs 15 | ARR de **70 k€** fin d'année 1, mais CA de l'année 2 de **13 440 €** | Sans résiliation, le CA de l'année 2 doit être au moins égal à l'ARR de fin d'année 1, soit environ 68,8 k€ | Remplacer le prévisionnel de la diapo 15 par la projection mensuelle de la section 6 |
| 9 | 10 | « 1/10 du prix », terminal « ≈ 2 000 $/mois/siège » | Bloomberg : 2 665 $/mois (1 terminal) ou 2 360 $/mois (multi) ; 185 € = 207,11 $ soit **7,77 %** et 8,78 % de ces prix, soit environ 1/13 et 1/11 ; 10,36 % seulement avec la valeur de 2 000 $ | Citer la source (NeuGroup, 09/10/2024) et écrire « environ 1/13 d'un terminal Bloomberg » ; LSEG et Dataminr sans prix public : « à vérifier » |
| 10 | 14 | « IA open source (Qwen, Mistral, Llama), 0 € de licence » ; « API de news ≈ 90 € » | Le code n'appelle aucune de ces IA : Gemini, **budget à 0 depuis le 10/10** ; moteur de signal lexical ; ingestion RSS, aucune API de news payante | Remplacer par la décision du 10/10 et retirer la ligne API de news ou la marquer « hypothèse non engagée » |
| 11 | 14 et 15 | « Hors salaires » et « Rentable dès l'année 1 » | Le guide exige que le temps de l'équipe soit valorisé : 59 400 € sur 12 mois (section 4.1) | Ajouter le coût économique ; « rentable » ne vaut que sur les coûts cash |
| 12 | 16 | Réponse au risque « Pas encore de client qui paie » : « Pour 300 demandes » | Phrase tronquée | « Tunnel de 300 comptes ciblés, 3 pilotes écrits avant mars 2027 » |
| 13 | 17 | « 2 quipaient » ; échéance « 3 pilotes, 2 qui paient » en mars 2027 | Coquille ; avec H5, le 2e client est facturé en mars 2027 (scénario central) | « 3 pilotes, 2 qui paient » |
| 14 | 7 et 8 | Six groupes de métiers ; le produit en compte **sept** depuis le 10/10 (Direction générale) | | Ajouter le groupe 7 |
| 15 | 9 | TAM de 500 000 professionnels à 2,2 k€ et SAM de 40 000 à 2,2 k€ | 500 000 × 2 200 = 1,10 Md€ (juste) ; 40 000 × 2 200 = **88 M€** (89 M€ affichés : arrondi) ; **aucune source** pour 500 k et 40 k | Citer une source ou mettre « à vérifier » |
| 16 | 15, 16, 17 | Numérotation : pages affichées 16, 18, 20 pour les diapos 15, 16, 17 | | Renuméroter |

Écart entre le pptx et le scénario central v2 : CA de l'année 1 de 6 720 € (pptx) contre **14 985 €** (central), mais ARR de fin d'année 1 de 70 k€ (pptx) contre **39 960 €** (central). Les deux écarts viennent de la taille des clients (8 sièges contre 18) et du cadencement des pilotes.

---

## 8. Version de décembre

> Exigence du guide : « Business plan, October and December versions, dated, with a record of the differences. »

*Section volontairement vide : à remplir à la séance de décembre, avec la date de la version finale et le tableau des différences entre cette version (v2.0 du 15/10/2026) et celle de décembre (prix, cible, modèle de revenus, canal principal, périmètre de l'offre, projection), chaque ligne appuyée par la donnée de terrain correspondante (log de prospection, objections, factures jointes, relevé d'heures).*

| Ce qui change | Octobre (v2.0, 15/10/2026) | Décembre (à remplir) | Pourquoi (donnée de terrain) | Source |
|---|---|---|---|---|
| | | | | |

---

## Annexe A : script de recalcul

Script Python autonome (bibliothèque standard seulement). Exécuté le 15/10/2026 ; les sorties sont celles citées dans ce document.

```python
from math import ceil
from datetime import date

# ---------- 0. Paramètres ----------
USD_EUR = 1.1195            # BCE, 14/10/2026 : 1 EUR = 1,1195 USD
PRO, TEAM, ENT = 185, 140, 2000   # €/siège/mois HT (pptx oct. 2026)
V = 0.40                    # coût variable €/siège/mois (hypothèse de juin 2026, non mesurée)

# ---------- 1. Incohérences du pptx ----------
inc = {}
inc["S14 total A1 recompté"] = 55+120+0+50          # affiché 325
inc["S14 total A2 recompté"] = 150+120+0+100        # affiché 370
inc["S14 total A3 recompté"] = 300+120+0+100        # affiché 520
inc["S15 coûts A1 = 12 x 325"] = 12*325             # 3900
inc["S15 coûts A1 corrigés (12 x 225)"] = 12*inc["S14 total A1 recompté"]
inc["S15 coûts A2 = 12 x 370"] = 12*370             # 4440 (affiché 4400)
inc["S15 CA A1: 8 x 6 x 140"] = 8*6*140             # 6720
inc["S15 CA A1 à 185: 8 x 6 x 185"] = 8*6*185       # 8880
inc["S15 CA A2: 16 x 6 x 140"] = 16*6*140           # 13440
inc["S15 CA A3: 40 x 6 x 140"] = 40*6*140           # 33600
inc["S15 résultat A1 = 6720-3900"] = 6720-3900      # 2820 (affiché '2820 k€')
inc["S15 résultat A1 corrigé = 6720-2700"] = 6720-12*225
inc["S15 résultat A2 = 13440-4440"] = 13440-4440    # 9000 (OK avec 4440)
inc["S15 résultat A2 avec 4400 affiché"] = 13440-4400
inc["S15 résultat A3 = 33600-6240"] = 33600-6240    # 27360 (OK)
inc["S9 : 31 sièges Pro x 185 x 12"] = 31*PRO*12    # 68820 (~70 k)
inc["S9 : 31 sièges Équipe x 140 x 12"] = 31*TEAM*12
inc["S9 : 11 clients, 31 sièges = sièges/client"] = round(31/11, 2)
inc["S13 : 8 contrats / 300 comptes"] = round(8/300*100, 2)
inc["S13 : comptes pour 11 contrats au même taux"] = round(11/(8/300), 1)
inc["S13 taux 300>60"] = 60/300; inc["S13 60>25"] = round(25/60,3); inc["S13 25>12"] = round(12/25,3); inc["S13 12>8"] = round(8/12,3)
inc["S9 SAM 40k x 2,2k"] = 40000*2200; inc["S9 TAM 500k x 2,2k"] = 500000*2200
inc["S10 : 185 € en $"] = round(185*USD_EUR, 2)
inc["S10 : 185 € / terminal seul 2665 $/mois"] = round(185*USD_EUR/2665*100, 2)
inc["S10 : 185 € / terminal multi 2360 $/mois"] = round(185*USD_EUR/2360*100, 2)
inc["S10 : 185 € / 2000 $ (valeur du pptx)"] = round(185*USD_EUR/2000*100, 2)
for k,v in inc.items(): print(f"{k:50s} {v}")

# ---------- 2. Conversions de prix concurrents ----------
for n,u in [("Bloomberg 1 terminal",2665),("Bloomberg multi",2360),("Benzinga Basic",37),("Benzinga Streamlined",147),("Benzinga Essential",197),
            ("Benzinga Essential annuel",166.42),("Benzinga Basic annuel",30.58),("Dataminr (médiane contrat/an, Vendr)",22000/12)]:
    print(f"{n:40s} {u:9.2f} $/mois = {u/USD_EUR:8.2f} €/mois = {u*12/USD_EUR:10.2f} €/an")
print("Apple Developer 99 USD/an =", round(99/USD_EUR,2), "€/an =", round(99/USD_EUR/12,2), "€/mois")

# ---------- 3. Scénario central (oct. 2026 -> sept. 2027) ----------
months = ["2026-10","2026-11","2026-12","2027-01","2027-02","2027-03","2027-04","2027-05","2027-06","2027-07","2027-08","2027-09"]
pilots_start = {"2026-11":1,"2026-12":1,"2027-01":1,"2027-02":1,"2027-03":1,"2027-04":1,"2027-05":1,"2027-06":1,"2027-07":1,"2027-08":2,"2027-09":1}
assert sum(pilots_start.values()) == 12
DELAY = 3          # revenu à M+3 après le début du pilote
SEATS = 3          # sièges Pro par client
# pilote n°k converti si k % 3 != 0 (2 sur 3)
order = [m for m in months if m in pilots_start]
pilot_list = []
for m in order:
    for _ in range(pilots_start[m]): pilot_list.append(m)
idx = lambda m: months.index(m) if m in months else None
conv_start = []    # mois de début de facturation
for k,m in enumerate(pilot_list, start=1):
    if k % 3 == 0: continue
    i = months.index(m)+DELAY
    conv_start.append(i)
tot_rev = 0; cum_rev = 0; cum_cost = 0; rows = []
OVH = 3.81*0 + 10.40 + 3.81   # VPS-3 (API) + VPS-1 (web), tarif public HT
APPLE = 99/USD_EUR/12
BREVO = 7.00
LINKEDIN = 50.0
PROXY = 30.0
for i,m in enumerate(months):
    clients = sum(1 for s in conv_start if s <= i)
    seats = clients*SEATS
    rev = seats*PRO
    cost = OVH + APPLE + BREVO + LINKEDIN + (PROXY if i >= 3 else 0)
    cum_rev += rev; cum_cost += cost; tot_rev += rev
    rows.append((m, pilots_start.get(m,0), clients, seats, rev, round(cost,2), round(rev-cost,2), round(cum_rev,2), round(cum_cost,2)))
print("mois | pilotes lancés | clients payants | sièges | CA | coûts cash | résultat cash | CA cumulé | coûts cumulés")
for r in rows: print(r)
print("CA 12 mois", tot_rev, "| ARR fin sept. 2027", rows[-1][3]*PRO*12, "| coûts cash 12 mois", round(cum_cost,2))
print("pilotes lancés", sum(pilots_start.values()), "contrats démarrés ou à démarrer", len(conv_start), "dont facturés avant fin sept.", sum(1 for s in conv_start if s < 12))

# ---------- 4. Point mort ----------
F_cash = OVH + APPLE + BREVO + LINKEDIN + PROXY
RATE, HPW, WEEKS = 30, 15, 44      # €/h, h/sem/membre, semaines travaillées/an (hypothèses)
F_time = 3*HPW*WEEKS/12*RATE
def be(F, p=PRO, v=V): return F/(p-v), ceil(F/(p-v))
print("F cash", round(F_cash,2), "F temps", round(F_time,2), "heures/mois", 3*HPW*WEEKS/12)
for name,F in [("cash seul",F_cash),("cash sans proxy ni LinkedIn",F_cash-PROXY-LINKEDIN),("cash + temps valorisé",F_cash+F_time)]:
    print(name, round(F,2), be(F))
# sensibilité taux horaire x heures
for rate in (20,30,46):
    for h in (8,15,25):
        Ft = 3*h*WEEKS/12*rate + F_cash
        print("taux",rate,"h/sem",h,"F",round(Ft,0),"sièges",ceil(Ft/(PRO-V)))
# point mort au prix Équipe 140
print("au prix Équipe 140:", ceil((F_cash+F_time)/(TEAM-V)))
# date : mois où le cash cumulé couvre les coûts cumulés ; extrapolation du rythme final
first = next(r[0] for r in rows if r[4] > r[5])
print("1er mois où CA mensuel > coûts cash :", first)
cum_pos = next((r[0] for r in rows if r[7] > r[8]), None)
print("1er mois où CA cumulé > coûts cumulés :", cum_pos)
# rythme: +1 client (3 sièges) tous les 1,5 mois ~ dernier semestre ; extrapolation linéaire
last_seats = rows[-1][3]; seats_6m_ago = rows[-7][3]
rate_seats = (last_seats-seats_6m_ago)/6
need = ceil((F_cash+F_time)/(PRO-V))
months_more = ceil((need-last_seats)/rate_seats)
print("sièges nécessaires", need, "rythme sièges/mois (6 derniers mois)", rate_seats, "mois supplémentaires après sept. 2027", months_more)
y,mn = 2027, 9+months_more
y += (mn-1)//12; mn = (mn-1)%12+1
print("date extrapolée", y, mn)

# ---------- 5. Temps passé (valorisation du développement déjà réalisé) ----------
d0, d1 = date(2025,3,12), date(2026,10,15)
weeks = (d1-d0).days/7
print("semaines depuis le 1er commit", round(weeks,1))
for rate in (20,30,46):
    for h in (6,10,15):
        print("dev déjà réalisé taux",rate,"h/sem/membre",h,"=", round(3*h*weeks), "h ->", round(3*h*weeks*rate), "€")
# coût d'acquisition en temps (hypothèse 0,5 h par compte contacté)
acq_h = 300*0.5; print("temps d'acquisition tunnel 300 comptes", acq_h, "h =", acq_h*RATE, "€ ->", acq_h*RATE/8, "€ par contrat (8 contrats)")
print("CA annuel d'un client de 3 sièges", 3*PRO*12)
# ---------- 6. Sensibilité du CA à 12 mois (scénario central = 3 sièges, délai 3 mois, 2 pilotes sur 3 convertis) ----------
def scenario(seats=3, delay=3, conv=(2,3), price=PRO):
    k_ok, k_n = conv
    cl = []
    for k,m in enumerate(pilot_list, start=1):
        if (k-1) % k_n >= k_ok: continue
        cl.append(months.index(m)+delay)
    rev = 0
    for i in range(12):
        rev += sum(1 for s in cl if s <= i)*seats*price
    seats_end = sum(1 for s in cl if s <= 11)*seats
    return rev, seats_end
print("contrôle central", scenario())          # doit donner (14985, 18) si la règle 'k%3==0 échoue' est remplacée par (k-1)%3<2
for lab,kw in [("2 sièges/client",dict(seats=2)),("5 sièges/client",dict(seats=5)),("délai 2 mois",dict(delay=2)),("délai 4 mois",dict(delay=4)),
               ("conversion 1/2",dict(conv=(1,2))),("conversion 1/3",dict(conv=(1,3))),("prix 140 €",dict(price=TEAM))]:
    r,s = scenario(**kw); print(f"{lab:18s} CA 12 mois = {r:7d} €, sièges fin sept. = {s}, ARR = {s*kw.get('price',PRO)*12}")
# ---------- 7. Coût de livraison par siège (fin sept. 2027, 18 sièges, 6 clients) ----------
cash_seat = F_cash/18
support_h_client = 2          # hypothèse : h/client/mois de support et d'accompagnement
support_seat = support_h_client*RATE/SEATS
print("coût cash/siège", round(cash_seat,2), "support/siège", round(support_seat,2), "total", round(cash_seat+support_seat,2),
      "marge brute", round((PRO-cash_seat-support_seat)/PRO*100,1), "%")
print("prix qui couvre le coût complet à 28 sièges :", round((F_cash+F_time)/28,2), "€/siège/mois")
print("prix qui couvre le coût complet à 18 sièges :", round((F_cash+F_time)/18,2), "€/siège/mois")

```

## Annexe B : sources externes (lues le 15/10/2026)

| Donnée | Source |
|---|---|
| Taux EUR/USD du 14/10/2026 : 1,1195 | BCE, https://www.ecb.europa.eu/stats/eurofxref/eurofxref-daily.xml |
| Bloomberg Terminal : 2 665 $/mois (1 terminal), 2 360 $/mois (multi), hausse de 6,5 % au 01/01/2025 | NeuGroup, 09/10/2024, https://connect.neugroup.com/public/blogs/bloomberg-terminals-how-much-more-youll-pay-next-year ; Quantify Terminal, publié le 31/08/2026, mis à jour le 27/09/2026, https://www.quantifyterminal.com/blog/bloomberg-terminal-cost-2026 |
| LSEG Workspace : 1 500 à 3 000 $/utilisateur/mois (Vendr) ; contrat médian de 4 752 $/an (même page) | https://www.vendr.com/marketplace/refinitiv |
| LSEG : environ 22 000 $/utilisateur/an, version allégée à partir de 3 600 $ | https://www.hudson-labs.com/blog/free-and-low-cost-alternatives-to-bloomberg (extrait de résultat de recherche) |
| Dataminr : contrat médian de 22 000 $/an (15 000 à 62 500 $) | https://www.vendr.com/marketplace/dataminr |
| Dataminr First Alert : « à partir de 20 000 USD par an » (document du 13/05/2022) | https://assets.applytosupply.digitalmarketplace.service.gov.uk/g-cloud-13/documents/720009/395974893365566-pricing-document-2022-05-13-1320.pdf (extrait de résultat de recherche ; 403 en lecture directe) |
| Benzinga Pro : 37, 147, 197 $/mois ; annuel 30,58, 124,75, 166,42 $/mois | https://www.benzinga.com/pro/pricing |
| OVHcloud VPS-1 à VPS-4 ; sauvegarde 1,10 € HT/mois ; snapshot 0,30 € HT/mois | https://www.ovhcloud.com/fr/vps/ |
| Cloudflare : Free 0 $, Pro 20 $/mois (annuel), Business 200 $/mois | https://www.cloudflare.com/plans/ |
| Brevo : Free, Starter 7 €/mois, Standard 15 €/mois | https://www.brevo.com/pricing/ (page générée le 29/09/2026) |
| Apple Developer Program : 99 USD par an | https://developer.apple.com/support/enrollment/ ; https://developer.apple.com/programs/whats-included/ |
| GitHub Actions : macOS standard 0,062 $/min ; Linux 2 cœurs 0,006 $/min | https://docs.github.com/en/billing/reference/actions-runner-pricing |
| Expo EAS : Free 0 $, Starter 19 $/mois, Production 199 $/mois | https://expo.dev/pricing |
| Prix du domaine .ovh, Gemini 2.5 Flash, LSEG fiable, LinkedIn | **à vérifier** : non retrouvés ou non utilisés |

Sources internes : `produit-et-roadmap.md`, `centre-operations.md`, `exploitation.md`, `backend-et-moteur.md`, `etat-et-recette.md`, `prospection.md`, `veille_scraping_comparatif.md`, `AGENTS.md` (dépôt de travail, lu le 15/10/2026) et le pptx d'octobre.
