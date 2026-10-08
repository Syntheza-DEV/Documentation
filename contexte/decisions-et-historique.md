# Décisions et historique

**Consolidation : 2026-10-09.** Lire ce document pour comprendre les choix déjà faits, leurs raisons et les contradictions à ne pas réintroduire. **Observé** = lecture locale actuelle ; **rapporté** = histoire datée ; **exploratoire** = intention ou recherche, sans livraison déduite. Aucun test, déploiement ou accès à la production n’a été exécuté pour cette consolidation.

## Règle d’arbitrage

Pour décrire le comportement, partir du code actuel et des preuves d’exécution datées, pas du titre « état actuel » d’une ancienne mémoire. Pour une décision de positionnement, préférer la décision explicitement datée la plus récente. Un plan de travail, un PDF exporté et un message de livraison n’ont pas la même force qu’un résultat de recette ou un accusé de dépôt.

**Le code local ne prouve pas la production ; une réussite historique ne garantit pas l’absence de régression.** Les divergences conservées ici sont intentionnelles : elles évitent de fabriquer un état unique à partir de documents d’époques différentes.

## Trajectoire du projet

### 2025 – avril 2026 : relier le produit et organiser l’équipe

| Date / statut | Évolution utile | Pourquoi la conserver |
|---|---|---|
| Mars 2025, documents historiques | Première ambition de veille, études et persona ; stack envisagée Python/cloud très différente du code ultérieur | Source du problème et de l’intention, pas référence technique actuelle |
| Octobre–décembre 2025, suivis rapportés | Développement réparti entre trois membres ; passage de Notion à GitHub Projects recommandé, réunions hebdomadaires ; front encore partiellement simulé en décembre | Le projet se construit sur l’année, pas uniquement lors d’un sprint final |
| Décembre 2025–mars 2026, suivis rapportés | Recentrage sur validation des sources et confiance, liaison front/back, authentification et CI/CD ; objectif de suppression des mocks | L’intérêt produit glisse de « collecter » vers « aider à juger et utiliser l’information » |
| 16 avril, audit historique | Backend déjà structuré, mais outillage défaillant et interfaces encore partiellement mockées selon l’audit | Snapshot de départ, pas inventaire actuel des défauts |
| 22–27 avril, compte-rendu et mentorat | Branchement aux API, industrialisation et Gemini 2.5 rapportés ; mentorat demande de clarifier cible, modèle économique et prix | La progression technique ne compensait pas l’insuffisance des preuves entrepreneuriales |

Le compte-rendu du 22 avril annonce une fin des mocks et des suites passantes, postérieure à l’audit du 16. Ce sont des observations historiques datées, **pas une nouvelle validation exécutée aujourd’hui**. Les compteurs de tests, endpoints et modèles des différents supports ne sont pas repris comme métriques actuelles.

### Mai – juillet 2026 : réduire les incohérences

| Date / statut | Décision ou apprentissage | Conséquence pour la reprise |
|---|---|---|
| Mai, benchmark puis jury blanc | Exploration Apify/social très large ; démonstration jugée décousue | Garder les leçons de coût et de risque, pas les vieux achats préconisés ; structurer la démo |
| 4 juin, décision mentor | Offre Pro à 4,99 € ; track avec deux obligatoires + B et E | Ne pas réintroduire Expert 49,99 € ni cinq faux objectifs de track |
| 30 juin–2 juillet, décisions produit et supports | Couverture avant monétisation ; source gratuite d’abord ; Apify écarté dans le nouveau cadrage ; Trust V1 distingué de V2 ; correction des ARR | Vendre la valeur produite par l’analyse plutôt qu’une promesse de couverture sans coût ; ne pas confondre roadmap et livraison |
| 5–8 juillet, organisation / rendu | Dossier RNCP enrichi de preuves ; board unifié et consolidé ; resynchronisation ponctuelle du dépôt école | Le backlog et le rendu ont une provenance spécifique, distincte du flux quotidien de développement |
| 13 juillet, résultat rapporté | Greenlight obtenu, mais acquisition, validation terrain et pilotage insuffisamment démontrés | Le feu vert ne certifie ni product-market fit ni réussite commerciale |

Le deck de juillet a volontairement conservé **l’histoire d’une année de travail**, plutôt qu’un récit « tout a été fait en trois mois ». Le creux d’organisation d’avril peut expliquer une priorisation, mais ne doit pas devenir une dramatisation ni effacer les contributions antérieures.

### Fin juillet – septembre 2026 : communication et preuve du parcours

| Date / statut | Évolution | Frontière à garder |
|---|---|---|
| 27 juillet, outil rapporté | Skill Instagram portable construit à partir des premiers SVG manuels | Génération de visuels ≠ publication ≠ acquisition mesurée |
| 6 août, recherche | Étude de généralisation du moteur social, benchmark de briques et corpus tiers | Recherche de communication, pas extension prouvée des sources du feed Syntheza |
| 1er–3 septembre, fichiers locaux observés | Préparation ATP, Book, affiche A2 et Warm-up ; registre de claims conservateur | Les blocages de préparation sont datés, pas le dernier verdict produit |
| 9 septembre, histoire de recette consolidée | Corrections et résultats plus récents que les premiers fichiers locaux | Voir [État et recette](./etat-et-recette.md) pour les preuves et réserves ; ne pas les nier au seul motif qu’un ancien classeur local est incomplet |
| 10–20 septembre, évolutions rapportées | Pitch final de 11 slides, signature « Moins de flux. Plus de recul. », vision multi-sources réaffirmée pour le Book | Proposition ou export final ne prouve pas dépôt ; vision ne prouve pas connecteur livré |

## Organisation de l’équipe

**Équipe : Ilhan Neuville, Tristan Gauci, Raphaël Pelerin.** Les dominantes rapportées en juillet sont Ilhan transverse/algorithmes/infrastructure et mobile, Tristan backend, Raphaël frontend web. Elles ont évolué : les comptes-rendus antérieurs attribuent aussi du backend à Ilhan et du DevOps/base de données à Tristan. Ne pas transformer une slide de rôles en exclusivité de contribution.

- **Contrainte rapportée :** année répartie entre Taïwan, Chine et Canada, avec fort décalage horaire. Le travail asynchrone, les issues, un rendez-vous fixe et la CI/CD permettaient d’avancer sans présence simultanée.
- **Accompagnement :** changement de mentor en cours d’année et recentrage entrepreneurial au printemps. Conserver l’effet sur le projet, sans reproches personnels ni attribution de faute.
- **Pilotage :** GitHub Projects/issues, Discord et comptes-rendus constituent la mémoire d’équipe. Les notes existantes organisent décisions, responsable, échéance et points en suspens.
- **Limite historique :** développement souvent poussé directement sur main, avec peu de PR selon le dossier RNCP. Le board consolidé en juillet ne doit pas être décrit comme un processus de revue systématique appliqué depuis l’origine.
- **Capacité :** projet étudiant porté par trois personnes, avec examens et disponibilité limitée ; priorisation et gel de périmètre ont été des arbitrages de capacité, pas une promesse de startup à temps plein.

## Board et dépôt de rendu : deux histoires distinctes

Le dossier RNCP rapporte le choix du **Project Syntheza-EIP nº 1858** comme board unique le **6 juillet**, les autres boards étant archivés. Des phases, features, critères d’acceptation et liens vers des commits réels ont été ajoutés rétrospectivement. Les réassignations ont équilibré la présentation des responsabilités ; elles ne reconstituent pas les temps passés ni les auteurs de chaque livraison.

Le développement réel avait migré vers les dépôts séparés de **Syntheza-DEV**. Le monorepo officiel école, resté sur un ancien état d’octobre 2025, a fait l’objet d’une **resynchronisation ponctuelle rapportée le 7 juillet 2026**, en conservant auteurs et dates par transposition des historiques, sans force-push. Des issues réellement livrées ont été fermées, d’autres laissées ouvertes parce que partielles.

**Ce n’était pas un miroir automatique.** La clarification du 8 juillet indique qu’un push dans Syntheza-DEV ne mettait pas à jour le rendu école. L’ancien script de push global supposait un monorepo et n’était plus adapté. Des options d’automatisation ou de resynchronisation à la demande ont été discutées, sans preuve de mise en place à cette date. Aucun état courant du dépôt école n’est affirmé ici et aucune opération n’y a été effectuée pour cette consolidation.

## Documentation : pièges qui ont déjà coûté de la crédibilité

1. **Stack fantôme :** les supports 2025 évoquaient Flask/Django, AWS/Kubernetes, GPT/BERT et des scrapers Python. Le produit développé utilise Node/Express/Prisma/PostgreSQL, React web et React Native/Expo, avec Gemini pour l’IA. Les README historiques peuvent eux aussi être en retard : le web actuel n’est plus le frontend JavaScript/Ant Design décrit autrefois.
2. **Ancien modèle de données :** Source/IngestedItem a laissé place à Publisher/Channel/Item. L’utilisateur suit un éditeur ; administrer ses canaux n’est pas la même chose que lui proposer un écran d’ajout de sources.
3. **Trust et ranking :** le Trust V1 repose sur la corroboration, avec neutralité explicite sans source trouvée. Le composite Trust 50/30/20 était une vision, pas sa formule réelle. Un ranking déterministe est un autre mécanisme ; son existence ne prouve pas que son explication est affichée à l’utilisateur.
4. **Business :** les anciennes courbes, capacités VPS, coûts unitaires, break-even et taux de conversion doivent rester des hypothèses datées. Les erreurs arithmétiques et les calculs corrigés sont exposés dans [Produit et roadmap](./produit-et-roadmap.md).
5. **Preuves :** tests annoncés dans un deck, formulaire diffusé, capture d’écran, ATP rempli, texte de Book et dépôt accepté sont des preuves de natures différentes. Les qualifier explicitement évite de les faire passer l’une pour l’autre.

## Identité graphique et sources natives

### Décisions à conserver

**Charte des supports rapportée depuis mai, reprise en septembre :** monochrome noir `#0A0A0A`, gris `#3A3A3A`, gris clair `#F5F5F5`, blanc, typographie **Space Grotesk**, globe Syntheza officiel. Les couleurs sémantiques servent aux états réels, pas à une nouvelle palette publicitaire. Un essai de gradient violet dans les schémas a été retiré le 1er juillet ; ne pas le reprendre parce qu’il existe dans un ancien token.

Le **2 juillet**, le wordmark en bas à gauche des slides a été supprimé. Pour ces nouveaux decks, la marque intérieure repose sur le globe en haut à droite et la pagination ; les anciennes règles réservant le bas de slide à ce wordmark sont caduques. Les supports de septembre ont renforcé une direction éditoriale : une idée par unité, noir pour les ruptures, clair pour les preuves, schémas explicatifs plutôt que décoration.

### Où existent les éléments réutilisables

| Élément | Disponibilité observée ou rapportée | Limite |
|---|---|---|
| Logo applicatif | Fichiers `logo.png` observés dans les assets mobile et le public web ; liens facultatifs ci-dessous | Le globe seul n’est pas interchangeable avec un lockup complet globe + mot-symbole |
| Typographie produit | Déclaration Space Grotesk et poids dans le code mobile | Un poste PowerPoint/Keynote n’utilise pas automatiquement les fontes de l’application |
| Documents et schémas | Générateurs DOCX/PPTX et SVG sources rapportés ; PDF métier présents dans Documentation | Les générateurs et leurs fontes ne sont pas incorporés à ce dossier de contexte |
| Communication de lancement | Avatar et trois posts de juillet présents localement en SVG/PNG : problème, promesse, fonctionnalités ; Keynote jury/RNCP et exports de septembre repérés ou rapportés | Présence d’un asset ≠ publication sur un compte ; les SVG initiaux pouvaient contenir des dépendances de machine |
| Outil Instagram | Skill portable rapporté le 27 juillet, enrichi d’un générateur éditorial sourcé décrit le 6 août | État historique de l’outil, sans nouvel audit complet de son dépôt dans cette consolidation |

### Leçons de portabilité

- **Respecter les proportions :** un ancien bug de lecture des dimensions forçait les images DOCX à une hauteur constante. Corriger le calcul de ratio, pas déformer les SVG ni compenser à l’œil.
- **Préserver les vrais logos :** ne pas écraser un wordmark vertical dans une zone horizontale ; utiliser la déclinaison adéquate. Le logo blanc doit garder ses détails, pas devenir un aplat uniforme.
- **Utiliser les bonnes fontes :** la fonte variable pouvait être identifiée comme « Space Grotesk Light » dans Office ; le renderer social ne suivait pas son axe de graisse. Les variantes statiques Regular/Medium/Bold ont été retenues pour des poids reproductibles.
- **Embarquer les ressources :** le skill Instagram utilise images intégrées et rendu SVG/PNG via `resvg`, sans dépendre des chemins d’une machine. Les compositions par blocs évitent les coordonnées libres ; les césures restent un choix éditorial.
- **Relire le rendu cible :** un fichier natif valide ou un rendu réussi n’établit ni lisibilité en présentation, ni conformité print, ni permission de publier.

## Recherche sociale du 6 août : apport et limites

**Nature : recherche exploratoire pour les outils de communication**, pas roadmap produit automatiquement adoptée. Le rapport initial et son JSON préconisent d’étendre le moteur Syntheza existant : charte versionnée, preuves rattachées aux affirmations, adaptation propre à chaque réseau, rendu déterministe, aperçu et approbation humaine. Le modèle choisit angle et structure ; le moteur applique la charte.

Le rapport initial déclarait un corpus de **0 publication Instagram, 5 X, 0 LinkedIn**. Le **README final du bundle, plus tard le même jour**, rapporte **12 Instagram, 30 X, 0 LinkedIn**. Ces deux états ne sont pas des statistiques contradictoires à fusionner : le second indique une collecte ultérieure. Le corpus et les sous-dépôts n’ont pas été réexplorés ici ; les comptes finaux sont donc **rapportés**, non recomptés. Ils restent historiques, partiels et ne prouvent ni cadence actuelle ni performance éditoriale.

### Conclusions utiles à Syntheza

- **Conserver le cœur existant :** génération SVG/PNG, fontes embarquées, source de chaque affirmation, texte alternatif et bundle relisible. Le rapport du 6 août rapporte des tests réussis à cette date ; aucun résultat actuel n’en est déduit.
- **Séparer les responsabilités :** recherche, analyse, rédaction, rendu, revue et publication. Un exemple de marque tierce fournit des structures abstraites, jamais une nouvelle charte Syntheza ni des textes à recopier.
- **Garder les inconnus :** aucun corpus LinkedIn exploitable n’était disponible. Les généralisations sur l’algorithme, les horaires ou la performance ne sont pas étayées ; importer un corpus autorisé plutôt qu’inventer.
- **Éviter un gros remplacement :** `resvg` reste la brique de rendu ; les patterns de charte, contexte produit et registre de claims sont utiles. Satori, vidéo Remotion et ordonnanceurs Postiz/n8n/Typefully n’étaient que des options, soumises à besoin réel et licence.
- **Ne pas confondre licences :** le rapport signalait une incohérence Proprietary/ISC dans l’outil local et des licences différentes parmi les références. Leur étude ne vaut pas autorisation de redistribuer leur code ou leurs médias.

La règle historique « le skill ne publie pas » s’applique au **générateur**. **Observé dans le backend local :** un chemin distinct relie un bundle Discord revu à l’approbation puis à la publication Instagram. Cela ne prouve ni fonctionnement en production ni collecte de posts pour le feed. La frontière à préserver est l’approbation humaine, pas l’affirmation devenue trop large « aucun code Syntheza ne peut publier ».

## Contradictions ouvertes et exclusions

| Sujet | Traitement retenu |
|---|---|
| Persona, périodes d’entretiens et chiffres 85/70/90 | Conserver leur origine historique ; aucune validation terrain reconstruite |
| Phases, dates et objectifs de revenu divergents | Restituer le plan juin/septembre comme prévision ; ne pas inventer une roadmap actualisée après les échéances |
| Decks, Book, affiche et baselines désynchronisés | Nommer la version et la date ; la proposition de septembre ne met pas à jour les vieux fichiers par magie |
| Rendus école et dépôts publics | Conserver le fait de la synchronisation ponctuelle ; ne pas affirmer miroir, dépôt final ou acceptation sans preuve |
| Préparations ATP contre corrections ultérieures | Utiliser [État et recette](./etat-et-recette.md), qui rassemble les preuves récentes avec leurs limites |

**Exclus volontairement :** préférences personnelles d’agents, conversations brutes, commandes de session, coordonnées privées, accès et secrets ; données détaillées du corpus tiers, snapshots de scrapers, classements de popularité de dépôts, recettes de contournement et éléments de charte d’une autre marque. Les entrées de suivi intitulées **« Tactic »**, consacrées au coaching sportif, ont été écartées : leur rattachement à Syntheza n’est pas établi. Les raisons utiles et limites de la recherche ont été conservées, pas ses dumps.

## Sources

- **Trajectoire :** `mentor-monitoring-history.md` (octobre 2025–juin 2026), `full-audit-apr2026.md` (16 avril), [compte-rendu du 22 avril](https://github.com/Syntheza-DEV/Documentation/blob/main/note-reunion/2026-04%20Avril/2026-04-22_reunion_note.md), `feedback_stack_docs_vs_reel.md` (mai 2026), `reference_jury_docs.md` (références 2025–mai 2026).
- **Décisions de juillet :** `business-roadmap-sourcing.md` (juin–1er juillet), `jury_blanc_decisions.md` (mai–1er juillet), `rncp_bloc7_deck.md` (5–6 juillet), `epitech-render-repo-sync.md` (7–8 juillet), `jury-greenlight-jul2026.md` (13 juillet). Les liens optionnels et révisions sont rassemblés dans [Sources](./sources.md).
- **Marque et outils :** `da_skills_syntheza.md` (20 mai–2 juillet), `da_pptx_branding_rules.md` (2 juillet), `skill-insta-syntheza.md` (27 juillet) ; [logo mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/90b3c9deaa8f232c9d58a9577588d851e0d9ee13/assets/images/logo.png), [logo web](https://github.com/Syntheza-DEV/frontend-web/blob/7d22813710d0d4e22b2dee24271d5fe99da05709/public/logo.png), [typographie mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/90b3c9deaa8f232c9d58a9577588d851e0d9ee13/src/design/typography.ts).
- **Recherche, sections représentatives uniquement :** `social-content-skill-foundation-report.md`, `social-content-skill-patterns.json` et `README.md` du bundle `social-content-skill-research-bundle-20260806T232901Z` (6 août 2026). Aucun contenu d’archive ou sous-dépôt n’a été copié.
- **Code actuel et communication :** [orchestrateur Discord/Instagram](https://github.com/Syntheza-DEV/backend/blob/af162cd726d5b1e98b8184d26cc0f491e5d5d4c9/src/services/discordInstagramPublisher/orchestrator.ts), `Syntheza-Deck-v3-BoardPlan.md` (juillet, préparatoire), fichiers Warm-up/Book/affiche de septembre et échanges du 10–20 septembre. Voir [Jury et livrables](./jury-et-livrables.md) pour leur substance et leurs statuts.
