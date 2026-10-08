# Jury et livrables

**Consolidation : 2026-10-09.** Pour préparer une soutenance ou reprendre un rendu, distinguer **document disponible**, **parcours exécuté** et **dépôt accepté**. Ce dossier restitue les informations utiles sans nécessiter l’accès aux anciennes conversations ou fichiers personnels. Les fichiers natifs cités ne sont pas tous embarqués dans le dépôt Documentation.

## Track et preuves attendues

**Décision rapportée, confirmée avec le mentor le 4 juin et remappée au guide le 1er juillet 2026 : track Entrepreneuriale.** Le but est de valider une proposition et un modèle commercial, pas une thèse de recherche. Le référentiel comporte **deux objectifs obligatoires et deux complémentaires choisis**, pas les cinq objectifs reformulés dans les premiers supports.

| Objectif retenu | Ce qu’il faut pouvoir montrer | État historique documenté |
|---|---|---|
| Obligatoire 1 — Validation de l’idée | Méthode terrain, retours, deux cycles MVP mesurés, capacité à pivoter | Entretiens rapportés et formulaire diffusé ; cycles formalisés et méthode insuffisamment prouvés au jury de juillet |
| Obligatoire 2 — Outils entrepreneuriaux | Canvas, projections cohérentes, indicateurs et pilotage | Plan d’action et BMC présentés ; GitHub Projects, issues et comptes-rendus utilisés comme preuves |
| Complément B — Stratégie et vision | Cible, différenciation, priorités et roadmap | Matière présente ; le périmètre de sortie et les critères de réussite restaient trop peu explicités |
| Complément E — Croissance et expansion | Plan à 6–12 mois et acquisition testable | Canaux et premiers visuels décrits, mais acquisition, funnel et activité réelle insuffisamment démontrés |

Le **Beta Test Plan (BTP)** structure la démonstration ; il n’est pas un cinquième objectif de track. Les plans de test ne sont pas des résultats. Le guide mentor demandait les livrables Go/No-Go 48 heures avant la soutenance : consigne historique, non confirmation d’un dépôt effectué.

## Retours des jurys

### Mai 2026 : rendre la démonstration lisible

Le suivi du **20 mai** rapporte un MVP démontrable, des objectifs jugés pertinents mais confus et une démonstration décousue. Les notes consignées sont présentation 2/3, objectifs 5/6, démo 5/8, roadmap 3/3.

- Organiser le parcours en sections explicites, avec un même scénario utilisateur.
- Montrer aussi le back-office pour expliquer l’exploitation réelle.
- Prévoir un appareil mobile réel : le simulateur n’était pas accepté pour le greenlight.
- Avoir un secours enregistré sur la version montrée, sans le présenter comme une exécution live.

### Juillet 2026 : feu vert, pas validation du marché

**Résultat reçu le 13 juillet : feu vert rapporté.** Les trois grilles donnent **14/20, 12/20 et 12/20**, soit environ **12,7/20 par moyenne arithmétique**. Le mode d’agrégation officiel n’a pas été confirmé ; « 14/20 final » ne doit pas être repris comme note globale.

| Point reconnu | Apprentissage à conserver |
|---|---|
| Problème clair, produit démontrable, outils projet et freemium présentés | La technique seule ne remplit pas les objectifs entrepreneuriaux |
| Acquisition et funnel jugés insuffisants malgré un plan existant | Une liste de canaux ne prouve ni funnel testable ni résultat de campagne |
| Vingt entretiens informels sans méthode, comptes-rendus ni insights exploitables | Relier chaque changement produit à un retour réel ; ne pas inventer de verbatim après coup |
| Chiffres de bêta incohérents, personas peu reliés au terrain, revenus trop optimistes | Distinguer inscrits, utilisateurs actifs, testeurs, clients et hypothèses ; garder un seul jeu de chiffres sourcé |
| TestFlight inaccessible aux évaluateurs, cron régulier non démontré, roadmap imprécise | L’accès du jury et les preuves d’exploitation font partie de la préparation, même si le code existe |

Les reproches « pas de stratégie » et « pas de plan » décrivent la **preuve perçue par le jury** ; ils ne signifient pas qu’aucun document n’existait. Le différentiel entre matière produite et matière montrée est le principal enseignement.

## Chiffres terrain : ne pas les recycler sans réserve

Les anciens supports mentionnent **20 professionnels interrogés**, puis **85 %** de surcharge, **70 %** perdant une à deux heures par jour et **90 %** souhaitant un outil centralisé. Une mémoire les rattache à un plan d’action de mars 2025, alors que le deck préparatoire les situe entre octobre 2025 et mars 2026. La chronologie et le protocole ne sont donc pas réconciliés.

Le formulaire court aurait été créé et diffusé le **1er juillet 2026** pour recueillir des données vérifiables : profil, surcharge, temps passé, confiance, outils, intérêt, fonctionnalités et prix acceptable. Aucun dépouillement probant n’est établi ici. Ces pourcentages, les gains de temps et les citations du plan de deck ne doivent pas devenir des résultats acquis simplement parce qu’ils figurent dans un support. Les pivots réellement rapportés sont le recentrage sur la confiance et la baisse du prix ; ils ne remplacent pas deux cycles utilisateurs mesurés.

## Démonstration : deux contextes distincts

### Soutenance longue de juillet

Le canevas prévoyait **5 min de présentation, 10 min d’objectifs, 20 min de démo, 10 min de roadmap et questions**. Répartition proposée : Raphaël pour le web, Tristan pour le backend/back-office, Ilhan pour le mobile. C’est une organisation de présentation, pas une mesure de contribution au code.

Le scénario « l’utilisateur ajoute une source RSS/Twitter depuis Settings » était devenu incohérent avec le modèle produit : les canaux sont administrés et l’utilisateur suit des éditeurs. Une feature du BTP doit décrire ce parcours réel, pas maintenir une ancienne route `/api/sources` pour les besoins du récit.

### Warm-up court de septembre

Le script local prévoit **90 secondes**, un présentateur et un trajet unique : **QR → Découvrir → article réel → résumé ou Trust visible → feedback lié au scénario ATP**. Il proscrit les fonctionnalités non affichées et les identifiants dans les captures. En cas de réseau indisponible, la vidéo doit reproduire le même parcours ; sans résumé ou signal visible, on passe à la demande de retour sans l’inventer.

**Ce script READY n’atteste ni passage live ni vidéo enregistrée.** La fiche de préparation demandait de précharger un article, masquer les notifications, vérifier le QR depuis un autre téléphone et essayer la vidéo hors réseau. Les comptes de démonstration sont transmis séparément, jamais incorporés au contexte ou au QR.

## Supports et état de livraison

### Références accessibles depuis Documentation

Les PDF **Syntheza-Beta-Test-Plan**, **Syntheza-Plan-Action** et **Syntheza-Moteur-Veille** sont présents dans `Documents/`. Ils restent les références détaillées, mais leurs dates, scénarios et projections doivent être confrontés au contexte actuel. Aucun de ces fichiers existants n’a été modifié par cette consolidation.

### Natifs et préparations repérés hors du dépôt Documentation

| Ensemble | Ce qui est disponible ou rapporté | Ce que cela ne prouve pas |
|---|---|---|
| Jury de juillet | `Syntheza-Jury-Blanc.key` présent localement ; génération PPTX v3 de 28 slides rapportée le 2 juillet | Le `BoardPlan` de 31 slides est un plan antérieur, pas le deck final ni un relevé de faits validés |
| RNCP | `Syntheza-RNCP.key` présent ; PPTX passé de 13 à 17 slides rapporté les 5–6 juillet | Présence des captures ne prouve pas les compétences sans explication de leur provenance |
| Warm-up | `Syntheza_Warmup_Demo.pptx`, générateur et scripts locaux présents ; support intermédiaire de 16 slides | Ce n’est pas nécessairement la dernière version utilisée en septembre |
| Pitch final | Historique du 10 septembre : `Syntheza_Pitch_Final` en PPTX natif, PPT et PDF, 11 slides | Exports rapportés, pas réception par l’école ni répétition chronométrée établie |
| Affiche / communication | SVG, PDF et PNG A2 locaux ; logos et visuels ; brouillon Book et fiche de dépôt | Existence de fichiers ne démontre ni conformité print complète, ni QR testé, ni téléversement accepté |

**Dernière évolution rapportée du pitch :** introduction noire révélant Syntheza, sa signature puis « Moins de flux. Plus de recul. » en trois clics ; même phrase en clôture. La slide « Ce qu’un abonnement Pro peut contribuer » a été retirée. Le calibrage annoncé était huit minutes de contenu plus trente secondes de marge, sans preuve de répétition. Les scénarios commerciaux restent des hypothèses, détaillées dans [Produit et roadmap](./produit-et-roadmap.md).

Les sources natives de génération existent historiquement dans les outils DOCX/PPTX/Instagram Syntheza ; cette consolidation **ne transfère pas ces outils ni les fichiers Office/Keynote**. Leur rôle, les décisions graphiques et leurs pièges sont résumés dans [Décisions et historique](./decisions-et-historique.md), sans imposer leur lecture pour comprendre le projet.

## Book et affiche : ce qui a changé en septembre

### Book EIP

Le brouillon du **1er septembre** était marqué DRAFT : logo prêt, noms confirmés, thématique officielle et exercice public de l’URL non confirmés à cette date. Les plafonds du formulaire consignés étaient : nom 15 caractères, sous-titre 40, description 500, « en bref » 110, baseline 60, espaces compris.

Le **20 septembre**, les propositions plus récentes retiennent **Data/IA**, la baseline **« Moins de flux. Plus de recul. »**, et l’équipe **Ilhan NEUVILLE, Tristan GAUCI, Raphaël PELERIN**. Le porteur a demandé de présenter la vision multi-sources, pas de limiter le pitch à l’ingestion RSS. La thématique proposée n’est pas une preuve de choix confirmé dans le formulaire ; le texte proposé n’est pas un accusé de dépôt. L’adresse publique retenue est `https://syntheza.ovh/` ; ne pas en déduire une disponibilité vérifiée aujourd’hui.

### Affiche A2 et registre de promesses

- La direction « Signal éditorial » fait de l’affiche une entrée vers le produit : identité, une promesse, un visuel réel, QR public et logo Epitech officiel. Noir/blanc/gris, Space Grotesk ; format A2 portrait, images adaptées à 300 dpi, fond perdu et marges sûres prévus.
- Les consignes ont évolué entre carte d’article sans mockup et utilisation d’un visuel de téléphone réel autorisé. La capture locale était une **preuve visuelle autorisée**, pas un résultat ATP ni l’autorisation de reprendre ses données comme texte public.
- Le registre local du 1er–3 septembre retirait les claims non prouvés, dont « explique ses choix ». Il est **antérieur aux corrections et à la recette rapportées du 9 septembre** ; il ne décrit pas le dernier état du produit et ne prouve pas que l’affiche a ensuite été remise à jour.
- Les contrats print prévoyaient logo Epitech non modifié, QR sans accès privé, contrôle au format réel et options d’export documentées. Les fichiers présents ne suffisent pas à confirmer CMJN, fonds perdus, scans indépendants ou validation finale.

La baseline tardive du pitch ne doit pas être attribuée automatiquement à un PDF d’affiche plus ancien : les supports peuvent rester désynchronisés.

## Dépôt : canal prévu, réception non établie

Le kick-off du **31 août 2026** désignait **Innovation** comme canal principal pour ATP et affiche. Une consigne affiche antérieure prévoyait aussi un envoi complémentaire ; la fiche locale demandait de confirmer ce double circuit dans l’activité authentifiée. L’échéance et le gel public annoncés étaient le **13 septembre 2026**. Ce sont des consignes historiques, pas des échéances nouvelles à exécuter aujourd’hui.

Aucune référence de soumission réouverte et acceptée n’a été établie dans les sources ici consolidées. **READY**, un fichier présent ou un texte proposé ne signifient pas **SUBMITTED**. De même, les premiers fichiers locaux de préparation ATP ne permettent pas d’écarter les résultats plus récents : [État et recette](./etat-et-recette.md) porte la synthèse probante de septembre et ses limites.

## RNCP Bloc 7 : ce que raconte le dossier

Le deck RNCP couvre la **gestion de projet et le management**, avec A15/A16, compétences C35 à C40 : planifier, affecter les ressources, organiser les rôles, piloter les aléas, communiquer et travailler en contexte international.

Les preuves rapportées sont le planning, les rôles, le board, des issues et captures, un bug et une PR, la réunion hebdomadaire et le travail asynchrone entre trois fuseaux. Le backlog de juillet a été **consolidé rétrospectivement** ; le présenter comme suivi continu depuis l’origine serait faux. Une répartition équilibrée des cartes n’est pas une mesure historique du travail réel.

L’accessibilité était abordée par les comportements du produit et les modalités de collaboration, sans inventer de situation personnelle. La slide internationale était partiellement en anglais ; traduction documentaire et PR anglaise étaient proposées comme preuves. Leur mention historique ne confirme ni fusion de la PR ni complétude actuelle de la documentation anglaise.

## Sources

- **Jury / mentorat :** `jury_blanc_decisions.md` (mai–1er juillet 2026), `jury-greenlight-jul2026.md` (13 juillet), `mentor-monitoring-history.md` (octobre 2025–juin 2026), `reference_jury_docs.md` (références 2025–mai 2026), `rncp_bloc7_deck.md` (5–6 juillet).
- **Références détaillées :** [BTP](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/Syntheza-Beta-Test-Plan.pdf), [Plan d’action](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/Syntheza-Plan-Action.pdf), [Moteur de veille](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/Syntheza-Moteur-Veille.pdf), [comptes-rendus](https://github.com/Syntheza-DEV/Documentation/tree/main/note-reunion).
- **Préparations locales de septembre, contenu utile extrait ci-dessus :** `innovation-handoff.md`, `submission-channels.md`, `EIP-book-content.md`, `warmup-demo-script.md`, `warmup-operator-sheet.md`, `poster-spec.md`, `claims-register.md` ; plans `2026-09-01-warmup-pitch-redesign.md`, `2026-09-01-warmup-editorial-refinement.md`, `2026-09-01-syntheza-a2-poster-design.md`.
- **Évolutions tardives :** échanges du 10 et du 20 septembre sur le pitch final et le Book. Voir [Sources](./sources.md) pour la provenance et [État et recette](./etat-et-recette.md) pour les résultats ATP distincts des préparations de communication.
