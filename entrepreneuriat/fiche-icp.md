# Fiche ICP : modèle dominant, profil de client idéal, critères de qualification, canaux

**Version du 15/10/2026.** Livrable de la séance d'octobre, objectif 2 du guide Entrepreneurship track, Tech 5 : « ICP sheet, qualification criteria, chosen channels ».

Références : business plan d'octobre 2026 (*Syntheza_Business_Plan_Simple_185.pptx*, slides citées), [prospection.md](./prospection.md), [hypotheses-critiques.md](./hypotheses-critiques.md), [centre-operations.md](../contexte/centre-operations.md), [strategie-communication.md](./strategie-communication.md).

Règles de lecture :
- **Observé** = lu dans le code, mesuré ou relevé sur une page publique datée. **Hypothèse** = choix d'équipe non encore confronté à un prospect. Aucun chiffre n'est donné sans l'une de ces deux étiquettes ou une source.
- Le dépôt est **public** : ce document ne contient que des types d'organisations et des fonctions. Les noms, e-mails et téléphones de prospects et de testeurs restent dans le pipeline privé du back-office.
- Aucune facture n'est disponible : aucun coût n'est présenté comme facturé.

---

## 1. Modèle dominant : B2B

> Guide : « State your dominant model: is your primary target an organisation (B2B) or an end user (B2C)? Justify it by the nature of the problem, the product and the decision cycle. Mixed projects name the side they prioritize over the term. »

**Décision : 100 % B2B sur la durée du projet (octobre 2026 à février 2027).** Le BP le dit : « B2B uniquement », contrats annuels par siège, pilote de 30 jours avant signature (slides 3 et 12). Le modèle grand public (Free, Pro à 4,99 €/mois, Business sur devis, décidé en juin) est abandonné par le pivot ; il n'est pas conservé comme second côté.

| Axe | Raisonnement | Preuve ou statut |
|---|---|---|
| **Nature du problème** | La douleur visée est une décision où de l'argent est engagé sur une info : « une news tombe, vraie ou rumeur, quel impact sur ma position ? » (slide 3). Un professionnel y répond dans l'exercice de son métier, avec un coût mesurable pour son employeur ; un particulier n'a ni obligation de traçabilité ni budget professionnel | **Hypothèse**. Aucun entretien de professionnel au 15/10 : c'est l'objet de H1 dans [hypotheses-critiques.md](./hypotheses-critiques.md) |
| **Nature du produit** | Les fonctions livrées sont des fonctions d'organisation : tableaux de bord partagés, journal d'équipe, journal de décisions en ajout seul scellé par chaîne de hachage, dossier PDF par décision, rôles, SSO (OIDC, SAML), API Entreprise, webhooks Slack / Teams, rapport de pilote. La facturation se fait hors plateforme, **sans paiement en ligne** | **Observé** : [centre-operations.md](../contexte/centre-operations.md), 10 et 11/10/2026 |
| **Cycle de décision** | Contrat annuel après un pilote de 30 jours, achat par le responsable d'un desk ou d'une société de gestion. Le BP prévoit 2 à 4 mois, pilote compris (slide 13) | **Hypothèse**, aucun cycle mesuré. Risque : le cycle de 2 à 4 mois dépasse la fenêtre du semestre (voir H3). C'est le coût assumé du choix B2B |
| **Économie** | Au prix du BP, un siège Pro rapporte 185 × 12 = 2 220 € par an ; un client Équipe de 5 sièges, 8 400 € (140 € × 5 × 12). L'ancien modèle donnait 898,20 € d'ARR pour 15 Pro à 4,99 € et 16 780,80 € pour 160 Pro et 4 Business, **scénarios** ([produit-et-roadmap.md](../contexte/produit-et-roadmap.md)) | Arithmétique recalculée le 15/10 ; **hypothèses** de volume et de prix |

**Ce que ce choix coûte.** Moins de clients, des cycles plus longs, des références indispensables (le SWOT note « aucune référence pro »), des objections de sécurité et de conformité (slide 6). Le guide, dans la voie B2B, exige donc 15 prospects qualifiés, 10 approches personnalisées, 3 réunions, des objections classées et un premier engagement ; ils sont traités dans [prospection.md](./prospection.md) et [hypotheses-critiques.md](./hypotheses-critiques.md).

---

## 2. ICP B2B pour P1 : trading et gestion d'actifs

> Guide : « Describe your Ideal Customer Profile (ICP): In B2B: sector, size, the contact's role, budget, purchase trigger, decision chain (who uses, who decides, who pays). »

P1 = « cœur de cible » du BP : trading et gestion d'actifs, « décision directe sur une news, budget data existant », pilotes au premier trimestre 2027 (slide 8). On commence par les **structures indépendantes de taille moyenne** : la personne qui ressent le problème y est souvent celle qui signe, et un cycle court est donc possible ([prospection.md](./prospection.md) §1). Les grandes banques d'investissement ne sont pas la cible (cycle de référencement et de revue de sécurité long, terminaux imposés).

| Champ du guide | P1-A : desks de trading | P1-B : gestion d'actifs | Statut |
|---|---|---|---|
| **Secteur** | Trading pour compte propre (prop trading), teneurs de marché, hedge funds, dont stratégies quantitatives, macro et event-driven | Sociétés de gestion entrepreneuriales, multi-family offices, gestion de fortune et banques privées indépendantes | Choix d'équipe, repris de [prospection.md](./prospection.md) §1 ; groupes du BP slide 7 (« Trading », « Gestion d'actifs ») |
| **Zone** | France, Monaco, Suisse romande, Belgique | idem | Hypothèse : proximité de langue et de réseau ; SAM du BP « FR / BE / CH » (slide 9) |
| **Taille** | 10 à 300 personnes : assez grand pour un budget data, assez petit pour qu'un associé décide seul | Encours de 1 à 30 Md€ ; 5 à 50 gérants et analystes | **Hypothèse** d'équipe. À confirmer par la colonne « taille / encours » avec source URL de `prospects-cibles.xlsx` |
| **Rôle du contact** | Premier contact : head of trading, CIO, associé. Utilisateur à interroger ensuite : trader, analyste event-driven | Premier contact : directeur de la gestion, CIO, associé gérant. Utilisateur : gérant, analyste, gérant privé | Hypothèse ; fonctions de [prospection.md](./prospection.md) §1. Une personne qui n'est pas décideur doit nommer le décideur (relance J+4) |
| **Budget** | Dépense déjà existante en information de marché (terminal, fil d'actualités professionnel, newsletters payantes). Budget cible pour Syntheza : 185 €/siège/mois, soit 2 220 € par an et par siège ; 8 400 € par an pour 5 sièges au tarif Équipe (BP, slide 12) | idem | **Prix = hypothèse du BP**, non confronté à un acheteur. Le **budget réel de l'organisation est inconnu** : question à poser à 18–23 min de l'entretien. Repères externes ci-dessous |
| **Déclencheur d'achat** | (a) une rumeur ou une info fausse qui a coûté du temps ou de l'argent à la séance ; (b) un pic de volatilité (taux, énergie, géopolitique, slide 4) ; (c) un rafraîchissement ou une résiliation d'abonnement à un terminal ou à un fil professionnel (les contrats de terminal courent sur des cycles de deux ans et se renouvellent automatiquement : Wikipedia, CostBench en §7) ; (d) l'arrivée d'un analyste ou d'un nouvel associé qui veut une veille structurée | (a) une demande de client UHNW sur l'actualité d'un actif détenu ; (b) une exigence de **traçabilité des décisions** (MiFID II, slide 4) ou une demande d'un comité ; (c) un renouvellement ou une résiliation d'abonnement de données ; (d) un incident de réputation sur une ligne du portefeuille | **Hypothèses**, tous à tester en entretien (guide d'entretien de [prospection.md](./prospection.md) §5). Aucun n'a été entendu d'un professionnel au 15/10 |
| **Chaîne de décision : qui utilise** | Trader, analyste event-driven, quant | Gérant, analyste, gérant privé | Hypothèse |
| **Chaîne de décision : qui décide** | Head of trading, CIO ou associé ; consultation possible de la conformité (usage des données, mention « informer sans recommander ») et de l'informatique (SSO, hébergement) | Directeur de la gestion, CIO ou associé gérant ; conformité consultée si la traçabilité est le motif d'achat | Hypothèse. Le SSO n'est **pas vérifié de bout en bout** et le pays du centre de données OVH est « à confirmer avant de l'annoncer » ([prospection.md](./prospection.md) §8) : ce sont deux points de blocage possibles |
| **Chaîne de décision : qui paie** | Budget de la société ou du desk ; dans une structure entrepreneuriale, l'associé qui décide est aussi celui qui paie. Facture hors plateforme | idem | Hypothèse |

### Repères externes de budget et de prix

Aucun n'est le budget d'un desk de 10 à 300 personnes. Ils situent l'ordre de grandeur ; sources et réserves dans [hypotheses-critiques.md](./hypotheses-critiques.md) §6.

| Repère | Valeur | Source, date |
|---|---|---|
| Bloomberg Terminal, un terminal | 2 665 $/mois (31 980 $/an) ; 2 360 $/mois à plusieurs terminaux | CostBench, 10/10/2026 (tierce, « medium confidence » ; Bloomberg ne publie pas de prix). Wikipedia donne environ 24 000 $ par an |
| Benzinga Pro | Basic 37 $, Streamlined 147 $, Essential 197 $ par mois ; Essential à 166,42 $/mois en annuel | benzinga.com/pro/pricing, consulté le 15/10/2026 |
| Marché mondial de la donnée de marché | 49,2 Md$ en 2025 | Burton-Taylor, 26/03/2026 |
| Syntheza Pro | 185 €/siège/mois (HT, hypothèse) | BP octobre 2026 |

**Lecture.** En valeur nominale (sans conversion de change), 185 est environ 7 % de 2 665 : face à un terminal, Syntheza coûte une fraction. Face à Benzinga Pro Essential (197 $), le prix est proche : l'argument face à un outil de ce type doit être la fiabilité expliquée, la traçabilité et l'intégration, pas le prix.

### Ce qui reste hypothèse dans cet ICP

Taille, zone, rôles, déclencheurs, chaîne de décision et budget. Ils sont corrigés ou confirmés par les 10 entretiens qualifiés de H1 ; la fiche sera republiée avec l'écart entre l'ICP d'octobre et l'ICP observé en décembre.

---

## 3. Hors cible

> Guide : « Qualification criteria allowing you to say whether a prospect falls inside your target or not. »

Les exclusions viennent du BP (slide 8) et de [prospection.md](./prospection.md) §1.

| Profil | Raison |
|---|---|
| Traders particuliers, étudiants, VC en amorçage, contrôle de gestion | BP : « Pas de budget pro ou pas de décision en temps réel » |
| Grandes banques d'investissement | Cycle d'achat long, terminaux imposés |
| Conseillers sans gestion discrétionnaire ; acteurs 100 % gestion passive | Pas de décision déclenchée par une info |
| Groupes P2 (finance d'entreprise, énergie et matières premières, risque et conformité) et P3 (médias) | Dans le BP mais **après** P1 (dès juin 2027 pour P2) ; non démarchés pour la vente d'ici février. Les entretiens de découverte (AFTE, 26–27/11) restent possibles, sans vente |

---

## 4. Grille de qualification d'un prospect

> Guide : « Qualification criteria allowing you to say whether a prospect falls inside your target or not » ; et, côté vente B2B, « At least 15 qualified prospects identified by name: company, contact, role, why they match your profile ».

Une grille **binaire** : chaque question reçoit « Oui » ou « Non ». Un critère sans réponse vérifiable est « À qualifier » et ne compte pas. Chaque « Oui » s'appuie sur une URL publique ou une phrase d'entretien notée mot pour mot.

| Code | Critère éliminatoire | Question à laquelle on répond Oui ou Non | Source de la réponse |
|---|---|---|---|
| K1 | Activité | L'organisation prend elle-même des décisions de trading ou d'investissement discrétionnaires (prop trading, teneur de marché, hedge fund, gestion d'actifs entrepreneuriale, multi-family office, gestion privée ou banque privée indépendante) ? | Site de la société, registres publics |
| K2 | Zone | L'équipe qui décide est-elle basée en France, à Monaco, en Suisse romande ou en Belgique ? | Site, annuaire de membres (AMAF, AFG, BEAMA) |
| K3 | Taille | P1-A : 10 à 300 personnes ? P1-B : encours de 1 à 30 Md€ **et** 5 à 50 gérants et analystes ? | Site, document réglementaire public ; **hypothèse de seuil** |
| K4 | Non exclu | L'organisation est-elle **hors** des exclusions (grande banque d'investissement, particulier, conseil sans gestion discrétionnaire, gestion 100 % passive) ? | Site |
| K5 | Contact | La personne visée est-elle décideur (head of trading, CIO, associé, directeur de la gestion) **ou** utilisateur direct (trader, analyste, gérant), avec une fonction vérifiée publiquement et joignable par un canal de la section 6 ? | Profil professionnel public |
| K6 | Usage | L'organisation traite-t-elle l'actualité pour décider (stratégie event-driven, macro, gestion de conviction, recrutement d'analystes, publications de recherche) ? | Site, publications, offres d'emploi |

**Règle : « dans la cible » = K1 à K6 tous à Oui.** Un seul Non = hors cible. Un « À qualifier » = pas encore compté.

Les critères suivants **ne sont pas éliminatoires** : ils servent à ordonner les comptes.

| Code | Critère de priorité | Question |
|---|---|---|
| Q1 | Décideur = contact | Le contact signe-t-il lui-même ? (structure entrepreneuriale) |
| Q2 | Budget existant | L'organisation paie-t-elle déjà un outil d'information de marché ? (confirmé en entretien) |
| Q3 | Introduction chaude | Existe-t-il une introduction possible (alumni, communauté, connaissance) ? |
| Q4 | Pas de revue de sécurité longue | Le pilote peut-il démarrer sans comité de sécurité fournisseur ? (hypothèse à confirmer) |

Cette grille sert à compter les « 15 prospects qualifiés » du guide (« company, contact, role, why they match your profile ») et à qualifier les entretiens de H1.

---

## 5. Bêta-testeurs : grille de qualification et réalisme de l'objectif de 20

> Guide : « At least 20 beta testers recruited, onboarded and active, from outside your team. Qualify each one: profile, which target criteria they meet, recruitment channel, date they joined. A tester who doesn't meet your criteria doesn't count. »
> Calendrier : « Campaign launched in October, 20 testers by end of November. »

### 5.1 L'objectif de 20 testeurs actifs fin novembre est irréaliste avec l'ICP strict

Calcul à partir des objectifs de fin novembre de [prospection.md](./prospection.md) §11, eux-mêmes alignés sur le tunnel du BP :

| Étape à fin novembre | Objectif du plan | Source |
|---|---|---|
| Comptes contactés | 45 | prospection.md §11 |
| Réponses | 9 (20 %) | idem |
| Démos | 4 | idem |
| Demandes de pilote | 2 | idem |
| Pilotes actifs | **1** | idem |

À 2 ou 3 utilisateurs nommés par organisation pilote (**hypothèse**), le tunnel produit **2 à 3 testeurs dans l'ICP strict** à fin novembre, et 4 à 6 à fin décembre (2 pilotes actifs). Pour 20 testeurs actifs de profil P1, il faudrait entre 7 et 10 organisations pilotes actives, soit 7 à 10 fois l'objectif de pilotes du plan, avec un cycle de 2 à 4 mois (BP) pour une campagne démarrée le 19/10 et **aucun contact consigné au 15/10**.

La communauté propre n'aide pas à court terme : au 15/10, Instagram compte 5 abonnés et la page LinkedIn n'existe pas ([conformite-attentes.md](./conformite-attentes.md)). Le guide demande d'enregistrer combien de testeurs viennent de la communauté ; cette mesure sera probablement proche de zéro et on le dira.

Un testeur peut être un **individu** qui n'a pas de décision d'achat à obtenir (le pilote Pro est une organisation d'un siège créée au premier accès). Cela lève le cycle d'achat, pas les freins de conformité d'un employeur (outil externe, données) : **hypothèse** à vérifier au premier entretien.

**Conclusion : avec la grille stricte de la section 4 appliquée aux personnes, atteindre 20 testeurs actifs fin novembre n'est pas réaliste. Il faut une grille testeur argumentée, ou accepter un décalage de la date.**

### 5.2 Grille testeur proposée (binaire, trois niveaux)

Le critère du testeur n'est pas celui de l'acheteur : un testeur éprouve le **produit** (compréhension, adoption, usage répété, valeur perçue, guide objectif 1), pas le consentement à payer. Le critère commun est donc le travail réel avec de l'info de marché ; le niveau indique à quelle distance de la cible du BP on se trouve.

**Critères communs (tous obligatoires, Oui ou Non)**

| Code | Question |
|---|---|
| T1 | La personne est-elle **hors de l'équipe** (ni fondateur, ni proche direct) ? |
| T2 | Exerce-t-elle un métier où elle **décide ou prépare une décision à partir d'info de marché**, au moins 3 jours par semaine ? (hypothèse : alignée sur le critère de pilote « au moins 3 jours par semaine ») |
| T3 | A-t-elle au moins un **actif, émetteur ou sujet réel** à suivre, qu'elle suit déjà aujourd'hui ? |
| T4 | Accepte-t-elle d'utiliser le produit **seule, sur son poste réel**, sans accompagnement pendant la ronde, et d'être informée de la mesure d'usage ? |
| T5 | Accepte-t-elle un entretien de 15 min si elle arrête d'utiliser le produit (relance des décrochés) ? |
| T6 | Sa **fonction et son organisation** sont-elles vérifiables (profil public ou introduction) ? |

**Niveau du testeur (un seul niveau, déterminé par la personne et son organisation)**

| Niveau | Définition | Position par rapport au BP | Compte dans les 20 ? |
|---|---|---|---|
| **N1 : cœur** | T1 à T6 = Oui **et** organisation « dans la cible » (K1 à K6 de la section 4 tous Oui) | Dans la cible P1 | **Oui** |
| **N2 : voisin dans le BP** | T1 à T6 = Oui **et** métier d'un groupe P2 du BP (finance d'entreprise : trésorier, DAF ; énergie et matières premières ; risque et conformité), **ou** métier P1 dans une organisation qui échoue à K2 ou K3 seulement (autre pays européen, taille hors seuils, grande société de gestion) | Dans le BP (P1 ou P2), **hors ICP strict** | **Oui, mais comptabilisé séparément** |
| **N3 : hors cible du BP** | T1 à T6 = Oui **et** profil que le BP déclare hors cible (investisseur particulier actif, étudiant en finance avec un portefeuille réel) | **Hors cible du BP** (slide 8 : « Traders particuliers, étudiants ») | **Non par défaut.** Compte seulement avec un accord écrit du mentor. N'entre jamais dans la preuve de consentement à payer |

Un testeur qui échoue à T1–T6, ou qui n'a pas de niveau, **ne compte pas**.

**Composition proposée pour atteindre 20 (hypothèse, à faire valider par le mentor à la séance d'octobre)**

| Niveau | Objectif d'ici le 30/11 | Raison |
|---|---|---|
| N1 | au moins 5 | 2 ou 3 attendus par le tunnel (5.1) ; 5 suppose d'ajouter quelques utilisateurs individuels via les introductions chaudes |
| N2 | au moins 10 | Le BP les cible déjà (P2) ; ils sont plus accessibles par alumni, CFA Society et communautés que des desks (hypothèse) |
| N1 + N2 | **au moins 15** | Reste dans la cible du BP |
| N3 | jusqu'à 5, seulement si le mentor l'accepte | Comble l'écart pour atteindre 20 sans prétendre que ce sont des clients |

**Règle de bascule.** Au 15/11, si N1 + N2 est inférieur à 8, on demande au mentor par écrit : (a) d'accepter N3 pour compléter, ou (b) de fixer la date de 20 testeurs à fin décembre pour la ronde 2. On ne baisse pas la barre en silence. Le rapport de recrutement donne toujours trois nombres : N1, N2, N3, ainsi que le nombre venant de la communauté propre.

**Définition d'« actif » (hypothèse, à fixer dans le protocole avant la ronde 1)** : au moins 3 jours distincts d'utilisation pendant la période de la ronde, mesurés par l'usage dans l'application. Le suivi individuel du guide (qui a utilisé, combien de fois, quand a-t-il décroché) est tenu dans le back-office.

**Fiche testeur** (privée, hors dépôt) : profil (fonction, type d'organisation), T1 à T6, niveau (N1/N2/N3), canal de recrutement, **origine : communauté propre oui/non**, date d'entrée, dates d'usage, motif d'arrêt s'il y a lieu.

---

## 6. Canaux d'approche choisis, justifiés pour ces profils

> Guide : « Outreach channels identified and justified for those profiles specifically. »

Les profils P1 sont peu nombreux, identifiables nommément, et le BP avait un seul canal (slide 13 : « Outbound LinkedIn + salons »). On le complète parce que la confiance est le frein principal d'une équipe d'étudiants sans référence (SWOT).

| # | Canal | Justification pour P1 | Statut de la preuve |
|---|---|---|---|
| 1 | **Introductions chaudes** : réseau Epitech Alumni, CFA Society France, SFAF, A.C.I. Monaco, AMAF, Monaco Economic Board | Monde restreint et fermé où une recommandation pèse plus qu'un message à froid ; réponse à l'objection « vous êtes étudiants ». Alimente aussi les testeurs N1 et N2 | **Hypothèse** (« meilleur taux de réponse attendu », [prospection.md](./prospection.md) §3). Comparée au froid par l'indicateur C4 de H3 |
| 2 | **LinkedIn, invitation personnalisée manuelle puis message** | Les professionnels y sont ; une enquête Brunswick de 2023 note l'importance de LinkedIn à 7,83 sur 10 pour des investisseurs institutionnels (S6 de [strategie-communication.md](./strategie-communication.md), tableau à revérifier). **Pas de scraping ni d'extraction nominative en masse** (écarté dès juin) | Source indirecte. Taux de réponse de 20 % du BP = **hypothèse**. La page LinkedIn de Syntheza n'existe pas au 15/10 : à créer avant les envois pour que le prospect puisse vérifier l'expéditeur |
| 3 | **E-mail à froid** vers une adresse professionnelle publiée, avec ligne « stop » | Complète LinkedIn pour les décideurs qui y sont peu actifs. Conforme à la CNIL pour le B2B : personne informée, message en rapport avec sa profession, opposition simple (CNIL, 04/02/2022, reprise de [prospection.md](./prospection.md) §9) | Cadre juridique sourcé ; efficacité = hypothèse |
| 4 | **Événements** : AGEFI AM Tech Day (03/11/2026, Paris), AGEFI Forum Chief Compliance Officers (05/11/2026, Paris), Journées de l'AFTE (26–27/11/2026, P2, découverte seulement), FinTech Belgium Digital Finance Summit (03/12/2026, Bruxelles) | Contact direct avec des acheteurs de P1-B (technologie et données des asset managers) et validation de l'angle traçabilité auprès de la conformité | Dates vérifiées sur les sites officiels le 15/10 ([prospection.md](./prospection.md) §3). **Conditions d'accès et coût inconnus** : « à vérifier » ; aucune inscription faite. Le BP ne chiffre pas les salons |
| 5 | **Communautés** (annonce relayée pour 5 à 10 entretiens de 20 min) | Source rapide de testeurs N1 et N2 ; pas de vente | Message prêt dans [prospection.md](./prospection.md) §4 ; résultat inconnu |

**Canaux écartés** : scraping nominatif LinkedIn (écarté dès juin, [produit-et-roadmap.md](../contexte/produit-et-roadmap.md)) ; TikTok (écarté, [conformite-attentes.md](./conformite-attentes.md)) ; publicité payante (non budgétée, le BP ne prévoit que 50 €/mois d'abonnement LinkedIn en année 1 : à vérifier) ; Instagram comme canal d'acquisition de décideurs (5 abonnés au 15/10 ; il reste un canal de crédibilité).

**Mesure.** Un code de séquence par canal (`seq-desks`, `seq-gerants`, `seq-fo`, `seq-communaute`, liens UTM), saisie du canal dans le pipeline, et indicateurs C1 à C4 de [hypotheses-critiques.md](./hypotheses-critiques.md) §5 par canal.

---

## 7. Sources externes (consultées le 15/10/2026)

| Source | Utilisation |
|---|---|
| Benzinga, [Pricing](https://www.benzinga.com/pro/pricing), consultée le 15/10/2026 | Prix Benzinga Pro |
| CostBench, [Bloomberg Terminal Pricing 2026](https://costbench.com/software/financial-data-terminals/bloomberg-terminal), vérifié le 10/10/2026 | Prix du terminal, renouvellement automatique, durée de contrat |
| Wikipedia, [Bloomberg Terminal](https://en.wikipedia.org/wiki/Bloomberg_Terminal) | Prix indicatif d'environ 24 000 $ par an ; contrats en cycles de deux ans |
| Burton-Taylor (TP ICAP), [Global Share & Segment Sizing 2026](https://tpicap.com/burtontaylor/reports/03/2026/financial-market-dataanalysis-global-share-segment-sizing-2026), 26/03/2026 | Marché mondial de la donnée financière |
| CNIL, [La prospection commerciale par courrier électronique](https://www.cnil.fr/fr/la-prospection-commerciale-par-courrier-electronique), 04/02/2022 | Cadre de l'e-mail B2B (repris de prospection.md) |
| Brunswick Group, Digital Investor Survey 2023 | Importance de LinkedIn (reprise de strategie-communication.md, S6) |

Réserves détaillées dans [hypotheses-critiques.md](./hypotheses-critiques.md) §6.
