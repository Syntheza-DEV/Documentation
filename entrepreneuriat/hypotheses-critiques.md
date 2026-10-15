# Hypothèses critiques du business plan d'octobre 2026 et plan de test

**Version du 15/10/2026.** Livrable de la séance d'octobre, objectif 2 du guide Entrepreneurship track, Tech 5 : « Note on the 2–3 critical assumptions and how you'll test them ».

Document de référence : *Syntheza_Business_Plan_Simple_185.pptx* (octobre 2026, pivot B2B, 185 €/siège/mois). Les numéros de slide renvoient à ce fichier. L'ICP et les critères de qualification sont dans [fiche-icp.md](./fiche-icp.md) ; le plan de prospection dans [prospection.md](./prospection.md).

Règles de lecture :
- **Observé** = lu dans le code, mesuré en production ou relevé sur une page publique datée. **Estimation** = chiffre ou affirmation construit par l'équipe ou repris d'une source indirecte, sans mesure sur la cible. **Rien** = aucune donnée, ni interne ni externe.
- Tout chiffre externe porte sa source et sa date en section 6. Sans source : « à vérifier ».
- Aucune facture n'est disponible. Toute ligne de coût du plan porte donc « facture à joindre » ; aucun coût n'est présenté comme facturé.
- Les seuils des sections 3 à 5 sont des **règles de décision fixées par l'équipe avant le test** (hypothèses), pas des seuils statistiques : les volumes visés sont trop petits pour une preuve statistique.

---

## 1. Inventaire des hypothèses, classées par fragilité

> Guide : « Rank your assumptions by fragility: which rest on observed data, which on an estimate, which on nothing at all. »

Tri : les plus fragiles d'abord (**Rien**), puis **Estimation**, puis **Observé**.

### 1.1 Reposent sur rien

| # | Hypothèse (slide) | Source qui la fonde aujourd'hui | Pourquoi « rien » |
|---|---|---|---|
| R1 | **Douleur** : un trader, un gérant ou un trésorier perd assez de temps ou d'argent sur « vraie ou rumeur, quel impact » pour payer une solution (slides 3, 11) | Aucune. Le BP écrit « Problème testable » et « hypothèses à valider en entretiens et pilotes » | Zéro entretien de professionnel consigné au 15/10 ([prospection.md](./prospection.md) §11). Le formulaire du 01/07 n'est pas dépouillé et les « 20 entretiens » de l'ancien modèle visaient un public grand public, sans méthode ni compte rendu ([jury-et-livrables.md](../contexte/jury-et-livrables.md)) |
| R2 | **Prix** : 185 €/siège/mois (slides 3, 12, 13) | Ancrage sur un concurrent (terminal ≈ 2 000 $, « 1/10 du prix », slide 10) | Le guide demande un prix construit sur ce que la cible dépense, ce que les concurrents facturent et ce que coûte le service. Seul l'ancrage concurrent existe ; la dépense actuelle de la cible n'est pas connue |
| R3 | **Tunnel** 300 comptes → 60 réponses → 25 démos → 12 pilotes → 8 contrats (slide 13) | Taux implicites : 20 %, 41,7 %, 48 %, 66,7 % (8 contrats sur 300 = 2,7 %) | Aucun envoi réalisé. Aucun benchmark sourcé de réponse à une prospection à froid dans cette cible : à vérifier |
| R4 | **Cycle de vente** de 2 à 4 mois, pilote de 30 jours inclus (slide 13) | Aucune | Aucun prospect n'a été suivi de bout en bout |
| R5 | **Conversion pilote → contrat annuel** : 8 sur 12 (slide 13) | Aucune | Aucun pilote ouvert au 15/10 |
| R6 | **Offres Équipe (140 €/siège, 5 minimum) et Entreprise (≈ 2 000 €/mois)** (slide 12) | Étiquetées « hyp. » dans le BP | Aucun prix testé ; l'offre Entreprise est « sur devis » dans le produit ([centre-operations.md](../contexte/centre-operations.md)) |
| R7 | **TAM / SAM** : ≈ 500 k professionnels UE, ≈ 40 k en FR/BE/CH (slide 9) | Aucune source citée | Le calcul est juste (500 000 × 2 220 € ≈ 1,11 Md€ ; 40 000 × 2 220 € ≈ 89 M€) mais les effectifs ne sont pas sourcés : à vérifier |
| R8 | **Droits sur les articles de presse résumés** (slides 5, 16, 17) | Risque inscrit au registre ; « avis d'un avocat » prévu | Aucun avis obtenu. Feu vert juridique attendu en janvier 2027, après les pilotes de novembre-décembre |
| R9 | **Statut AMF / CIF** : « informer sans jamais dire achetez » suffit à ne pas être du conseil (slides 5, 16) | Garde-fous techniques observés (titres en forme de conseil signalés, ton lexical « pas une direction de marché ») ; sources AMF S11 et S12 de [strategie-communication.md](./strategie-communication.md) | Aucun avis juridique sur le produit lui-même |
| R10 | **Le Trust Factor a une valeur pour le décideur** (slides 6, 11) | Score déterministe V3 en production | Utilité jamais mesurée auprès d'un utilisateur professionnel. Le Trust Factor par IA est indisponible (budget IA à 0) |
| R11 | **La piste d'audit (journal de décisions, MiFID II) est un déclencheur d'achat** (slides 4, 11) | Fonction livrée (journal en ajout seul, chaîne de hachage) | Demande non vérifiée. Durée de conservation de 5 ans « conseillée » dans le produit : base réglementaire à vérifier |
| R12 | **Un desk accepte une couche de tri sans cotations ni AFP / Reuters / AP** | Aucune. Le produit n'a « aucune cotation » et « pas d'AFP, de Reuters ni d'AP (aucun flux public) » | Dépend de ce que le desk utilise déjà (à demander en entretien) |
| R13 | **Une équipe d'étudiants est crédible** auprès de professionnels (SWOT : « aucune référence pro », « expertise métier à recruter ») | Aucune | Objection « Vous êtes étudiants » déjà anticipée, jamais confrontée à un vrai prospect |
| R14 | **Le résumé IA n'est pas indispensable** : le pitch dit « résume et note chaque info » (slide 3) alors que le budget IA est à 0 | Décision du 10/10 ; Brief et texte non IA | Aucun utilisateur n'a dit si le résumé non IA suffit |

### 1.2 Reposent sur une estimation

| # | Hypothèse (slide) | Source qui la fonde aujourd'hui | Réserve |
|---|---|---|---|
| E1 | **Latence < 60 s** de la news à la décision (slides 3, 5, 17) | Objectif d'ingénierie ; relevé fréquent des flux « chauds » toutes les 15 s, SSE | Mesuré le 10/10 : médiane 4 min, p90 22 min. Le délai vient en grande partie des éditeurs (caches CDN, articles ajoutés après leur date). 5 flux sur 667 annoncent un hub WebSub. Environ 45 flux répondent 403 depuis l'hôte. Voir O2 |
| E2 | **Coûts mensuels** 325 / 370 / 520 € (slide 14) | Prix publics « indicatifs, à vérifier » (Hetzner / OVH, NewsAPI.ai / Mediastack) | **Facture à joindre** pour chaque ligne. La somme des lignes de l'année 1 est 55 + 120 + 0 + 50 = **225 €**, pas 325 € : l'écart de 100 € n'est pas expliqué (la ligne « Salons » n'est pas chiffrée). Les 120 € de « scraping & API » ne correspondent pas à l'ingestion actuelle (RSS, sans API payante, proxy facultatif inerte). L'hôte actuel (1,9 Go de RAM, sans swap) a calé le 11/10 : le poste « serveurs » de 55 € est incertain |
| E3 | **Prix du terminal** : « ≈ 2 000 $ / mois / siège » (slide 10) | Slide 10 ; Wikipedia : environ 24 000 $ par an et par utilisateur ; CostBench, 10/10/2026 : 2 665 $/mois pour un terminal seul, 2 360 $/mois à plusieurs terminaux ; Bloomberg ne publie pas de prix | Fourchette 2 000 à 2 665 $ selon la source, sans conversion de change. Sources en section 6 |
| E4 | **Le budget data existe chez les cibles P1** (slide 8 : « budget data existant ») | Marché mondial de la donnée financière : 49,2 Md$ en 2025 (Burton-Taylor) | Chiffre mondial, pas le budget d'un desk ou d'une société de gestion de 10 à 300 personnes. À demander en entretien |
| E5 | **Taux de réponse de 20 %** à la prospection (slide 13, [strategie-communication.md](./strategie-communication.md)) | Hypothèse de l'équipe | Aucun benchmark sourcé : à vérifier |
| E6 | **LinkedIn atteint la cible** (slide 13 : « Outbound LinkedIn + salons ») | Brunswick, enquête 2023 : importance de LinkedIn 7,83 sur 10 pour des investisseurs institutionnels (S6 de strategie-communication.md ; tableau à revérifier visuellement) | Mesure d'importance pour la recherche d'actions, pas une preuve de réponse à une invitation. La page LinkedIn de Syntheza n'existe pas au 15/10 |
| E7 | **Rentable dès l'année 1** ; résultat 2 820 € (slide 15) | Calcul du BP | Hors temps des fondateurs, hors juridique, comptabilité et assurance ; coûts sans facture. Le CA de l'année 1 est 8 clients × 140 € × 6 mois = 6 720 € : il suppose **un siège par client au prix Équipe**, alors que l'offre Équipe impose 5 sièges minimum et que la slide 9 vise 11 clients et 31 sièges |
| E8 | **Seuil de rentabilité** | Non présent dans le BP | Calcul indicatif à partir des seuls coûts du BP (325 €/mois) : 325 / 185 = 1,76, soit **2 sièges Pro payants** pour couvrir ces coûts hors temps des fondateurs. Ce n'est pas un seuil de rentabilité au sens du guide, qui exige le temps de l'équipe valorisé |
| E9 | **Sécurité et hébergement acceptables** (slide 6 : « exigences sécurité ») | 2FA, SSO OIDC / SAML, registre d'accès livrés | SSO jamais vérifié de bout en bout avec un vrai fournisseur ; pays du centre de données OVH à confirmer avant de l'annoncer |

### 1.3 Reposent sur une donnée observée

| # | Fait | Source | Date |
|---|---|---|---|
| O1 | Produit web en production : centre d'opérations, signal déterministe, Trust Factor V3, journal de décisions scellé, API Entreprise, connecteurs, 317 éditeurs et 677 flux | [centre-operations.md](../contexte/centre-operations.md) (code et production) | 10 et 11/10/2026 |
| O2 | **Latence mesurée** : médiane 4 min et p90 22 min « en régime normal » ; la médiane sur 24 h affichait 48 min à cause des rattrapages après coupure | centre-operations.md, « Latence affichée » | 10/10/2026 |
| O3 | Outillage de pilote en production : page `/pilote`, rapport de pilote PDF, NPS J+7 et J+25, statuts Pilote / Actif / Expiré | centre-operations.md | 11/10/2026 |
| O4 | Budget IA à 0 USD (décision du propriétaire) ; Trust Factor IA indisponible | centre-operations.md | 10/10/2026 |
| O5 | Prix publics de Benzinga Pro : Basic 37 $, Streamlined 147 $, Essential 197 $ par mois ; en annuel 30,58 $, 124,75 $, 166,42 $ par mois ; essai gratuit de 14 jours | benzinga.com/pro/pricing | consulté le 15/10/2026 |
| O6 | Aucun contact commercial consigné ; 44 comptes identifiés ; Instagram 5 abonnés, page LinkedIn inexistante | prospection.md §11 ; conformite-attentes.md | 15/10/2026 |
| O7 | Arithmétique interne du BP exacte sur la formule (2 220 € par siège Pro et par an ; 31 sièges × 185 € × 12 = 68 820 €, soit ≈ 70 k€) | Recalcul | 15/10/2026 |

### 1.4 Incohérences du BP d'octobre à corriger dans la version révisée

Elles sont des hypothèses fragiles au sens du guide car elles empêchent de lire le modèle.

| Où | Constat |
|---|---|
| Slide 14, total année 1 | Lignes : 55 + 120 + 0 + 50 = 225 € ; total affiché 325 € |
| Slide 15, année 2 | Coûts 370 € × 12 = 4 440 €, affichés 4 400 € ; résultat 13 440 − 4 440 = 9 000 €, affiché 9 000 € (cohérent avec 4 440 uniquement) |
| Slide 15, année 1 | « 2820 k€ » : unité fausse (2 820 €) |
| Slides 9, 12, 13, 15 | Cible de clients et de sièges : 8 clients (slide 13, 15), 11 clients et 31 sièges (slides 9, 12), « ≈ 8 sièges » (slide 15) ; ARR de 70 k€ contre CA de 6 720 € à la même échéance sans passage explicite de l'un à l'autre |
| Slide 15, prix | Le CA multiplie 140 € (Équipe, 5 sièges minimum) par 8 clients : un siège par client |
| Slide 10 | « 1/10 du prix » : 185 / 2 665 ≈ 7 % en valeur nominale (sans change) face au terminal, mais 185 / 197 ≈ 94 % face à Benzinga Pro Essentiel ; l'avantage de prix n'existe que face aux terminaux |

---

## 2. Les 2 hypothèses (3 au maximum) dont l'échec détruirait le modèle

> Guide : « Identify the 2 or 3 whose failure would destroy the model: is the price acceptable, is the decision cycle compatible with your cash flow, does your planned channel actually reach the target, is the problem painful enough that anyone will pay? »

Trois hypothèses sont retenues. Le critère de choix : si elle est fausse, **aucun réglage de prix, de scope ou de projection ne sauve le modèle** ; il faut changer de cible ou de promesse.

| Retenue | Hypothèse | Questions du guide couvertes | Pourquoi son échec détruit le modèle |
|---|---|---|---|
| **H1** | **Douleur payante** : des décideurs et utilisateurs de P1 vivent assez souvent et assez cher le problème « news : vraie ou rumeur, quel impact » pour payer une couche de tri **en plus** de leurs outils actuels, à un prix de l'ordre de 185 €/siège/mois | « is the problem painful enough that anyone will pay? » ; « is the price acceptable? » (R1, R2, R4, R12, E4) | Tout le BP (SOM, revenus, tunnel) suppose un paiement. Si la douleur n'existe pas, il n'y a pas de client ; si elle existe mais que le prix est refusé, le modèle de revenus change. Le coût mensuel du BP étant faible (325 €, **facture à joindre**), ce n'est pas la structure de coûts qui peut tuer le modèle, c'est l'absence de revenu |
| **H2** | **Promesse de latence** : Syntheza peut tenir « de la news à la décision en moins de 60 s » sur les sources accessibles, et cette vitesse compte pour la cible | « is the problem painful enough… » côté valeur ; promesse centrale de la slide 3 et de la preuve de décembre (slide 17) (E1, O2, R12) | La promesse est la phrase de vente et la preuve n° 1 du BP. L'état mesuré est une médiane de 4 min (10/10). Les éditeurs gratuits publiant par RSS limitent la vitesse. Si la promesse tombe, le positionnement face aux terminaux (qui vendent la vitesse) s'effondre et seule la synthèse et la traçabilité restent à justifier |
| **H3** | **Canal et cycle** : l'outbound (LinkedIn, e-mail, communautés, événements) atteint des décideurs P1, obtient des réunions, et un engagement écrit qui coûte quelque chose au client arrive dans la fenêtre du semestre | « does your planned channel actually reach the target? » ; « is the decision cycle compatible with your cash flow? » (R3, R4, R5, E5, E6, R13) | Sans accès à la cible, ni H1 ni H2 ne peuvent être testées ; sans engagement avant fin décembre, le guide n'est pas rempli (« At least one first customer… a pilot accepted… with written agreement ») et le BP « Go sous conditions » (slide 17) tombe |

### Hypothèses non retenues comme critiques, avec raison et action

| Hypothèse | Pourquoi pas dans le top 3 | Action prévue |
|---|---|---|
| R8 Droits presse, R9 AMF / CIF | Risque réel, mais **non testable sur le terrain d'ici décembre**. Le plan fixe le feu vert en janvier 2027. Un avis négatif aurait un effet fort, et il faut donc le lancer tôt | Rédiger la demande d'avis d'avocat avant le 30/11 (responsable proposé : Tristan). Aucun coût n'est supposé : **à vérifier** auprès de l'avocat. Avant l'avis, les pilotes restent gratuits, sur des sources publiques, sans redistribution de contenu et avec la mention « Syntheza informe et ne recommande jamais » |
| E2, E7, E8 Coûts et rentabilité | Les coûts du BP sont faibles ; leur erreur de quelques dizaines d'euros ne change pas la décision | Relever les factures réelles (OVH, domaine, LinkedIn, API éventuelle) avant la séance de décembre : « facture à joindre » tant qu'elles ne sont pas dans le dossier |
| R10 Trust Factor, R11 Piste d'audit, R14 Résumé non IA | Mesurés en même temps que H1 par des questions dédiées de l'entretien | Questions 18–27 min du guide d'entretien ([prospection.md](./prospection.md) §5) ; verbatims notés mot pour mot |
| R7 TAM / SAM | Sans effet sur la décision de fin d'année | Sourcer ou supprimer dans la version de décembre |

---

## 3. H1 : la douleur est-elle payante, à ce prix ?

> Guide : « For each one: how you'll test it before December, through what concrete action, and at what result you'll consider it validated or invalidated. »

**Énoncé testable.** Parmi des praticiens qui entrent dans l'ICP de [fiche-icp.md](./fiche-icp.md), au moins la moitié a vécu le problème au cours des 3 derniers mois, en a chiffré le coût en temps ou en argent, et une part significative juge 185 €/siège/mois finançable en plus de ses outils actuels.

| Élément | Contenu |
|---|---|
| **Action concrète** | Entretiens de découverte de 30 min, méthode « Mom Test » ([prospection.md](./prospection.md) §5), menés **dans l'ICP seulement**. Question de prix posée **en deux temps** : (1) « combien paieriez-vous aujourd'hui, par siège et par mois, pour résoudre cela ? » sans montrer de prix ; (2) on révèle 185 € et on note la réaction. Les entretiens commencent la semaine du 26/10 et durent jusqu'au 11/12 |
| **Volume** | 10 entretiens qualifiés (grille de [fiche-icp.md](./fiche-icp.md) §4). Le plan de [prospection.md](./prospection.md) vise 12 entretiens à fin décembre (hypothèse) : au moins 10 doivent être qualifiés |
| **Indicateurs** | **I1** : part des qualifiés confirmant le problème (« oui ») avec une fréquence d'au moins une fois par mois **et** un coût cité (temps ou argent). **I2** : part des qualifiés qui paient déjà un outil d'information de marché (budget existant). **I3** : prix cité spontanément (médiane) et réaction à 185 € (« dans le budget » / « à arbitrer » / « non »). **I4** : engagements obtenus (pilote accepté par écrit, ou introduction vers un décideur) |
| **Validée si** | I1 ≥ 6 sur 10 **et** au moins 4 sur 10 répondent « dans le budget » ou « à arbitrer » à 185 € **et** au moins 2 engagements (I4) |
| **Invalidée si** | I1 ≤ 2 sur 10 (la douleur n'existe pas) **ou** au moins 7 sur 10 répondent « non » à 185 € et que le prix cité spontanément a une médiane inférieure à la moitié de 185 € (soit 92,50 €) |
| **Incertaine si** | Entre les deux, ou moins de 10 entretiens qualifiés au 11/12 (le dire, ne pas conclure) |
| **Justification des seuils** | Seuils d'équipe, hypothèses. 6 sur 10 : plus de la moitié, pour ne pas valider sur 2 ou 3 interlocuteurs chaleureux. 2 sur 10 : en dessous, le problème est marginal dans la cible. Moitié du prix : en dessous de 92,50 €, l'ARR visé de 70 k€ (slide 9) demanderait environ 63 sièges au lieu de 31 (70 000 / (92,50 × 12) ≈ 63), soit un modèle nettement plus lourd à vendre |
| **Date** | Point d'étape le **30/11/2026** (au moins 6 entretiens qualifiés tenus, hypothèse alignée sur l'objectif de 7 de [prospection.md](./prospection.md) §11) ; clôture le **11/12/2026**, résultat figé 48 h avant la séance de décembre (date de séance à confirmer) |
| **Responsable proposé** | Ilhan (pipeline et relevé commercial, [strategie-communication.md](./strategie-communication.md) §Rythme) ; les trois mènent les entretiens ; verbatims notés mot pour mot pendant l'entretien |
| **Preuves** | Grille de synthèse §5 de prospection.md, remplie le jour même ; export du pipeline du back-office ; aucun verbatim reconstitué |
| **Décision selon le résultat** | **Validée** : poursuivre sur P1 ; garder 185 € pour le pilote ; lancer P2 selon le BP. **Validée sur la douleur, invalidée sur le prix** : changer le prix, l'unité (par équipe plutôt que par siège) ou le périmètre, puis retester sur les entretiens restants. **Invalidée sur la douleur** : changer de segment. Pistes à comparer à partir des réponses : P1-B (gestion d'actifs) seul, ou P2 (risque et conformité, trésorerie) si l'angle traçabilité (R11) est celui qui ressort. **Incertaine** : poursuivre en janvier, sans élargir à P2 |

### Résultat H1 (à remplir pour la séance de décembre)

> Guide : « The outcome of each critical assumption you identified in October: validated, invalidated, still uncertain (with the corresponding data) »

| Champ | Valeur |
|---|---|
| Statut | _(validée / invalidée / incertaine)_ |
| Date de clôture | |
| Entretiens qualifiés tenus / visés | / 10 |
| I1 (problème confirmé, fréquence, coût cité) | |
| I2 (budget existant) | |
| I3 (prix spontané, réaction à 185 €) | |
| I4 (engagements) | |
| Verbatims marquants (mot pour mot, anonymisés) | |
| Écart aux seuils | |
| Décision prise | |
| Correction du business plan | _(prix, cible, modèle, canal, périmètre, projection)_ |
| Reste à tester | |

---

## 4. H2 : la latence de moins de 60 s est-elle tenable et utile ?

**Énoncé testable.** Sur les sources que Syntheza peut atteindre, la médiane du délai entre publication et affichage passe sous 60 s, et la cible déclare qu'une alerte à ce délai a une valeur pour sa décision.

| Élément | Contenu |
|---|---|
| **Action concrète** | **Côté mesure** : instrumenter et publier un relevé quotidien de médiane et p90 du délai entre la date de publication déclarée par l'éditeur et la capture, **par palier de source** (officiel, agence, référence, spécialisé), sur la watchlist de référence du compte pilote P1. Mesure 1 sur 7 jours avec la configuration actuelle ; actions correctives (par exemple les flux 403 depuis l'hôte, la fréquence de relevé, l'abonnement WebSub là où un hub existe) ; mesure 2 sur 14 jours. **Garde-fou** : sur un échantillon de 30 infos tirées au hasard, comparer la date publiée avec l'heure d'apparition constatée sur la page de l'éditeur, car certains flux ajoutent les articles après leur date ([centre-operations.md](../contexte/centre-operations.md)). **Côté cible** : une question ajoutée au guide d'entretien (« à partir de quel délai une alerte n'a plus de valeur pour votre décision ? ») |
| **Périmètre de mesure** | Sources de paliers « officiel » et « agence », relevées toutes les 15 s, sur au moins 200 infos par mesure (hypothèse). Les sources lentes (presse à cache CDN) sont suivies mais n'entrent pas dans l'indicateur de la promesse ; le résultat doit donc être présenté avec la **liste des sources qui le portent** |
| **Indicateurs** | **L1** : médiane du délai de capture sur le périmètre (formule : médiane de `capturé − publié`). **L2** : p90 sur le même périmètre. **L3** : pour chaque interlocuteur qualifié, délai au-delà duquel une alerte n'a plus de valeur pour sa décision : moins de 1 min / de 1 à 5 min / plus de 5 min |
| **Validée si** | L1 ≤ 60 s sur 7 jours consécutifs sur le périmètre déclaré **et** au moins 3 interlocuteurs sur 10 placent leur seuil de valeur à 1 min ou plus (la vitesse atteinte sert à quelqu'un) |
| **Invalidée si** | À la mesure 2, L1 > 120 s sur le périmètre « officiel et agence » **ou** au moins 7 interlocuteurs sur 10 placent leur seuil de valeur sous 1 min alors que L1 est supérieure à 60 s |
| **Incertaine si** | 60 s < L1 ≤ 120 s, ou L1 ≤ 60 s sans que 3 interlocuteurs sur 10 valorisent ce délai, ou moins de 8 réponses à la question côté cible |
| **Justification des seuils** | 60 s est la promesse du BP (slides 3 et 17). 120 s = le double de la promesse : au-delà, la formule ne peut plus être soutenue sans la réécrire. 7 sur 10 reprend la règle de H1 pour l'invalidation. Hypothèses d'équipe |
| **Dates** | Mesure 1 : 19/10 au 25/10. Actions correctives jusqu'au 20/11. Mesure 2 : 21/11 au 04/12. Lecture : **07/12/2026**. Résultat figé 48 h avant la séance de décembre |
| **Responsable proposé** | Tristan (backend, ingestion, back-office) |
| **Preuves** | Export du relevé quotidien (médiane, p90, volume, par palier), échantillon de 30 infos avec les deux horodatages, liste des sources du périmètre ; captures datées du statut public (`/statut`) |
| **Décision selon le résultat** | **Validée** : garder « moins de 60 s » **uniquement pour le périmètre mesuré** et le dire dans la démo. **Invalidée** : réécrire la promesse sur le chiffre mesuré (« en minutes », avec la liste des sources) ; reclasser le segment : les traders event-driven (P1-A), qui achètent la vitesse, passent après la gestion d'actifs (P1-B), dont la valeur est plutôt le Brief pré-ouverture, la fiabilité et la traçabilité ; la slide 17 et le message « décider en < 60 s » sont modifiés. **Incertaine** : ne promettre aucun chiffre en démonstration, montrer la mesure du pilote en direct |

### Résultat H2 (à remplir pour la séance de décembre)

| Champ | Valeur |
|---|---|
| Statut | _(validée / invalidée / incertaine)_ |
| Date de clôture | |
| L1 médiane, mesure 1 (configuration actuelle) | |
| L1 médiane, mesure 2 (après actions) | |
| L2 p90, mesures 1 et 2 | |
| Sources du périmètre | |
| Échantillon de contrôle (30 infos) : écart date publiée / apparition | |
| L3 (délai de valeur cité, entretiens) | |
| Actions correctives et effet mesuré | |
| Décision prise | |
| Correction du business plan | _(promesse, segment, message, slide 17)_ |
| Reste à tester | |

---

## 5. H3 : le canal atteint-il la cible, et l'engagement arrive-t-il à temps ?

**Énoncé testable.** L'approche personnalisée (LinkedIn, e-mail, communautés, événements) obtient des réponses de décideurs P1 au rythme supposé par le BP, au moins 3 réunions, et un engagement écrit qui coûte quelque chose au client avant fin décembre.

> Guide : « An agreement that costs nothing proves nothing. »

**Définition de l'engagement retenu.** Pilote accepté **par écrit** (e-mail suffit) par une personne qui peut décider, avec : au moins 2 utilisateurs nommés ; une watchlist réelle chargée ; des critères de succès écrits ; 30 min d'onboarding et 2 points de retour ; une date de décision. Le coût pour le client : du temps de professionnels, l'exposition de ses actifs suivis, un changement d'habitude. Un contrat payant ou une lettre d'intention est plus fort et reste l'objectif de février.

| Élément | Contenu |
|---|---|
| **Action concrète** | Lancer la prospection du plan de [prospection.md](./prospection.md) §12 dès le 19/10 : séquences `seq-desks`, `seq-gerants`, `seq-fo`, `seq-communaute` ; relance J+4 et J+10 ; chaque contact saisi le jour même dans le pipeline du back-office. **Comparaison de canaux** par code de séquence et par origine (froid / introduction chaude), pour tester l'hypothèse de [prospection.md](./prospection.md) §3 selon laquelle les introductions ont le meilleur taux de réponse |
| **Indicateurs** (formules) | **C1** taux de réponse = comptes ayant répondu / comptes contactés. **C2** taux de réunion = réunions tenues / comptes ayant répondu. **C3** délai = jours entre premier contact et accord écrit de pilote. **C4** taux de réponse froid contre chaud |
| **Validée si** | À la date de clôture : au moins 45 comptes contactés avec C1 ≥ 20 % ; **au moins 3 réunions** ; **au moins 1 accord de pilote écrit** correspondant à la définition ci-dessus, avec C3 ≤ 56 jours (8 semaines) |
| **Invalidée si** | Au 30/11, au moins 45 comptes contactés et C1 < 10 % **ou** aucune réunion obtenue ; **ou** au 14/12, aucun accord de pilote sur au moins 3 réunions tenues avec des comptes de l'ICP |
| **Incertaine si** | Entre les deux seuils (C1 entre 10 et 20 %, ou 1 ou 2 réunions seulement), ou moins de 45 comptes contactés à la date (retard d'exécution, pas du marché) |
| **Justification des seuils** | 20 % et 45 contacts : valeurs du BP (300 → 60) et de l'objectif de fin novembre de [prospection.md](./prospection.md) §11 (45 contactés, 9 réponses). 10 % = la moitié de l'hypothèse. 3 réunions = minimum du guide (« At least 3 meetings or reasoned replies obtained »). 8 semaines : le premier contact est prévu le 19/10 ; un accord écrit au plus tard le 14/12 laisse le pilote démarrer avant Noël. Un pilote de 30 jours commençant après le 01/12 ne sera pas terminé le 31/12 : le cycle de 2 à 4 mois du BP est donc **incompatible avec un contrat signé avant fin décembre** et seul un pilote accepté peut être l'engagement de décembre |
| **Dates** | Premiers envois : 19/10. Point d'étape : **30/11/2026** (45 contactés, 9 réponses, 7 entretiens, 2 demandes de pilote : objectifs de prospection.md §11). Clôture : **14/12/2026**. Résultat figé 48 h avant la séance de décembre |
| **Responsable proposé** | Raphaël (mesure des canaux et des visites `/pilote` par UTM) avec Ilhan (pipeline et relances). Chacun des trois mène son quart de la liste de comptes |
| **Preuves** | Export CSV du pipeline (anonymisé pour le dépôt public) ; captures horodatées des messages ; accord écrit de pilote ; test sheet par canal |
| **Décision selon le résultat** | **Validée** : poursuivre l'outbound, viser la conversion en contrat annuel en février. **Invalidée** : passer en priorité aux introductions chaudes (Epitech Alumni, CFA Society, SFAF, A.C.I. Monaco, AMAF, Monaco Economic Board) et à la présence physique (AGEFI AM Tech Day le 03/11, FinTech Belgium le 03/12), réduire le volume pour personnaliser davantage, tester un autre ICP (P2) si la raison de refus est « pas le bon interlocuteur » ou « pas de besoin ». **Incertaine** : doubler le volume de contacts et fixer une date de relecture au 31/01 |

### Résultat H3 (à remplir pour la séance de décembre)

| Champ | Valeur |
|---|---|
| Statut | _(validée / invalidée / incertaine)_ |
| Date de clôture | |
| Comptes contactés | |
| C1 taux de réponse (global, par séquence) | |
| C2 taux de réunion | |
| C3 délai premier contact → accord écrit | |
| C4 froid contre chaud | |
| Relances faites (J+4, J+10) | |
| Réunions tenues (nombre, dates) | |
| Accord de pilote écrit (oui / non, pièce jointe) | |
| Objections classées (prix, timing, maturité, chaîne de décision, concurrence, budget) | |
| Décision prise | |
| Correction du business plan | _(canal, tunnel, cycle, projection)_ |
| Reste à tester | |

---

## 6. Sources externes (consultées le 15/10/2026)

| Réf. | Source | Ce qu'elle établit | Réserve |
|---|---|---|---|
| X1 | Benzinga, [Benzinga Pro, Pricing](https://www.benzinga.com/pro/pricing), page consultée le 15/10/2026 | Basic 37 $, Streamlined 147 $, Essential 197 $ par mois ; en annuel 30,58 $, 124,75 $, 166,42 $ par mois ; essai gratuit de 14 jours ; la page compare à « Bloomberg at $30,000/year » | Prix publics du fournisseur, en dollars ; produit centré sur les actions américaines |
| X2 | CostBench, [Bloomberg Terminal Pricing 2026](https://costbench.com/software/financial-data-terminals/bloomberg-terminal), vérifié par la source le 10/10/2026 | 2 665 $/mois (31 980 $/an) pour un terminal seul ; 2 360 $/mois à plusieurs terminaux ; médiane des contrats 31 980 $/an (15 achats) | Source tierce, « medium confidence » ; Bloomberg ne publie pas de prix |
| X3 | Wikipedia, [Bloomberg Terminal](https://en.wikipedia.org/wiki/Bloomberg_Terminal), consultée le 15/10/2026 | Environ 24 000 $ par an et par utilisateur, ou 27 000 $ pour un terminal seul ; à partir de 30 000 $ dans une autre section | Source secondaire, dates des chiffres non précisées |
| X4 | Burton-Taylor (TP ICAP), [Financial Market Data/Analysis, Global Share & Segment Sizing 2026](https://tpicap.com/burtontaylor/reports/03/2026/financial-market-dataanalysis-global-share-segment-sizing-2026), 26/03/2026 | Revenu mondial de la donnée de marché : 49,2 Md$ en 2025 (+6,5 %) | Résumé public du rapport ; mondial, pas le budget de la cible |
| X5 | Brunswick Group, Digital Investor Survey 2023 (S6 de [strategie-communication.md](./strategie-communication.md)) | Importance de LinkedIn 7,83 sur 10 pour des investisseurs institutionnels | Tableau lu par conversion automatique, à revérifier visuellement |
| X6 | CNIL, [La prospection commerciale par courrier électronique](https://www.cnil.fr/fr/la-prospection-commerciale-par-courrier-electronique), 04/02/2022 (reprise de [prospection.md](./prospection.md) §9) | Conditions de la prospection B2B par e-mail | Non revérifiée dans cette note |

Aucune source n'a été trouvée pour le prix de Dataminr (seules des estimations de blogs concurrents sont apparues) : le BP le cite sans prix, il reste « à vérifier ».

Chiffres internes : [centre-operations.md](../contexte/centre-operations.md) (10 et 11/10/2026), [prospection.md](./prospection.md) (15/10/2026), [jury-et-livrables.md](../contexte/jury-et-livrables.md), business plan d'octobre 2026.
