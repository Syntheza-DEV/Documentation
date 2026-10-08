# Sources, versions et nettoyage

Consolidation : **9 octobre 2026**. Ce fichier décrit la provenance du contexte, pas une nouvelle recette ni un état de production vérifié aujourd'hui.

## Périmètre du dossier

Le contexte partageable auparavant dispersé entre les repos, les notes projet privées, les livrables locaux et les échanges récents est **synthétisé dans ce dossier**. Un lecteur n'a pas besoin de ces sources privées pour comprendre les décisions, les contraintes ou les validations retenues.

Les sources brutes n'ont pas été recopiées en bloc : elles contiennent des doublons, des états successifs, des accès privés et des préférences personnelles. Les fichiers locaux non versionnés sont cités par leur nom uniquement ; leur substance utile est intégrée au sujet concerné, sans lien dépendant d'un poste personnel.

Les documents techniques complets et les binaires déjà présents dans le dépôt restent à leur emplacement. Le contexte est distinct d'une copie de leur documentation exhaustive ou d'une archive de tous les médias.

## Repos et base de révision

Les quatre HEAD ci-dessous ont été lus localement au début de la consolidation. **Ils identifient les commits de base des clones, pas les versions actuellement déployées, ni une garantie que les arbres applicatifs locaux n'ont aucune modification.** Les observations de code des fichiers de contexte portent sur le contenu local effectivement consulté.

| Dépôt | Base HEAD locale au 9 octobre |
| --- | --- |
| [Backend](https://github.com/Syntheza-DEV/backend) | `af162cd726d5b1e98b8184d26cc0f491e5d5d4c9` |
| [Web](https://github.com/Syntheza-DEV/frontend-web) | `7d22813710d0d4e22b2dee24271d5fe99da05709` |
| [Mobile](https://github.com/Syntheza-DEV/frontend-mobile) | `90b3c9deaa8f232c9d58a9577588d851e0d9ee13` |
| [Documentation](https://github.com/Syntheza-DEV/Documentation) | `2eb7259d9bb355884bb790cd8b06a664738a3b3b` avant ajout de ce dossier |

Avant publication, `main` a été synchronisée en avance rapide avec `origin/main` : les ajouts anglais (`README.en.md` et `Documents/en/technical-overview.md`) au commit `92c1099` sont préservés. Ce contexte n'écrase pas ces contributions.

La racine du workspace n'est pas un dépôt Git. Les anciens dossiers locaux `docs/` et `deliverables/` ne deviennent pas partagés en poussant un repo applicatif : leur contexte utile est désormais rapatrié ici.

## Documentation déjà partageable

| Source | Utilité et limite |
| --- | --- |
| [Documentation backend](../Documents/backend_documentation.md) | Référence technique de juin ; compteurs et hypothèses de budget à confronter au code plus récent |
| [Documentation Web](../Documents/frontend_web_documentation.md) | Architecture Vite/React/Tailwind ; descriptions d'auth à confronter à l'implémentation actuelle |
| [Documentation mobile](../Documents/mobile_documentation.md) | Expo/Tamagui, navigation, conventions UI ; pas une recette sur appareil |
| [Comparatif sourcing](../Documents/veille_scraping_comparatif.md) | Étude de solutions et budgets, pas liste de connecteurs sociaux livrés |
| [Comptes rendus](../note-reunion/README.md) | Historique décembre 2025 à juin 2026 ; pas état final de septembre |
| [Plan de test bêta](../Documents/Syntheza-Beta-Test-Plan.pdf) | Plan et critères, pas preuve que chaque scénario a été exécuté |
| [Plan d'action](../Documents/Syntheza-Plan-Action.pdf) | Roadmap, intention et hypothèses business |
| [Moteur de veille](../Documents/Syntheza-Moteur-Veille.pdf) | Présentation du pipeline et des évolutions envisagées |
| [Vue technique anglaise](../Documents/en/technical-overview.md) | Contribution récupérée avant publication ; son résumé de l'infrastructure et du build iOS ne remplace pas les workflows effectivement consultés |

Les README applicatifs plus anciens et les specs de mai sont utiles pour retrouver l'histoire, mais ne doivent pas réintroduire l'ancien modèle `Source`/`IngestedItem` ou l'ancienne stack Web. Des copies locales des PDF existent ; une taille identique ne démontre pas leur identité. Le dossier ne choisit pas arbitrairement une copie locale comme nouvelle édition officielle.

## Notes projet privées : substance rapatriée

Ces noms servent de provenance. Ce ne sont pas des chemins à demander aux autres membres ou à leur Claude.

### Architecture et moteur

- `MEMORY.md`, `backend-architecture.md`, `publisher_channel_refacto.md` : index de juillet et trajectoire du modèle ; contradictions de compteurs et de statut traitées comme états datés, pas faits simultanés.
- `trust-factor-ia-architecture.md`, `gemini_budget_lessons.md` : corroboration, distinction confiance/pertinence, budget et coût des appels.
- `admin_panel_v1.md`, `admin_panel_v2_system.md`, `admin_cron_v3.md` : progression du contrôle opérateur et des jobs.
- `reference_prisma_v7_gotchas.md`, `feedback_prod_db_migration.md`, `resolved-issues.md` : invariants Prisma, migrations et pièges récurrents.
- Documentation du backend `instagram-publishing.md` et `instagram-discord-publisher.md` : contexte de publication éditoriale sortante, approbation humaine et reprise sans double publication ; à ne pas confondre avec une ingestion sociale.

### Applications et exploitation

- `frontend-architecture.md`, `mobile_charter_iso.md`, `feedback_port_iso.md`, `feedback_zustand_reactive_pattern.md` : reconstruction Web, parité et état réactif.
- `web-auth-session-cache.md`, `future_features_mobile.md` : corrections de session et évolutions prévues, sans présenter une ancienne anomalie comme encore reproduite.
- `deployment.md`, `apple_compliance_ci.md`, `deploy-web-lint-and-prod-env.md` : livraison, configurations et distribution iOS ; les coordonnées d'accès et secrets sont retirés.
- `feedback_prod_ops.md`, `incident_2026-05-16.md`, `feedback_mobile-expo-cli.md`, `feedback_mobile-testing.md` : incidents et contraintes d'exploitation/testing reformulés en connaissances projet.

### Produit, jury et design

- `business-roadmap-sourcing.md`, `sourcing_scraping_strategy.md` : scénario commercial, sources et progression envisagée.
- `jury_blanc_decisions.md`, `jury-greenlight-jul2026.md`, `mentor-monitoring-history.md`, `full-audit-apr2026.md` : apprentissages et événements datés ; les anciennes affirmations de réussite et chiffres ne deviennent pas des résultats courants.
- `rncp_bloc7_deck.md`, `epitech-render-repo-sync.md`, `reference_jury_docs.md` : dossiers jury/RNCP, preuves attendues et rôle distinct du repo de rendu.
- `da_skills_syntheza.md`, `da_pptx_branding_rules.md`, `skill-insta-syntheza.md`, `feedback_stack_docs_vs_reel.md` : charte, fabrication des supports et fidélité du récit à la stack réelle.
- `social-content-skill-foundation-report.md`, `social-content-skill-patterns.json` et README du bundle de recherche sociale d'août : synthèse exploratoire ; ni moteur social applicatif livré ni archive de repos tiers nécessaire à l'onboarding.

## Livrables locaux et échanges récents

### Recette

| Source | Traitement dans le dossier |
| --- | --- |
| `ATP-readiness.md`, README du paquet et index de preuves préparatoire | État du 1er septembre conservé comme préparation historique |
| `Syntheza_ATP.xlsx` | Plan original distingué des résultats exécutés |
| `Syntheza_ATP_fix-test-cycle-1.xlsx` | Recette locale et données contrôlées distinguées de la production |
| `Syntheza_ATP_resultats_2026-09-09_corriges.xlsx` | Rapport final relu, résultats et limites synthétisés dans [État et recette](./etat-et-recette.md) ; aucun accès privé requis |
| Échange de fin de recette du 9 septembre | Corrections finales, maintien des abonnements, limites du rejeu et confirmation du Digest testé trois fois par le propriétaire |

### Communication et rendu

- `innovation-handoff.md`, `submission-channels.md`, `warmup-demo-script.md`, `warmup-operator-sheet.md` : préparation des dépôts et de la démo ; retirer adresses personnelles, liens d'édition et statuts non corroborés.
- `poster-spec.md`, `claims-register.md`, `EIP-book-content.md` et plans/specs locaux de septembre : composition, promesses et statut des preuves ; un brouillon n'est pas une soumission effectuée.
- `Syntheza-Deck-v3-BoardPlan.md`, decks jury/RNCP et plans antérieurs : historique de narration, sans réimporter des chiffres anciens comme traction acquise.
- Échanges des 10–13 septembre : évolution du pitch jusqu'à la version finale à 11 slides, suppression d'une slide Pro et intro noire en trois révélations ; temps prévu, pas répétition chronométrée prouvée.
- Échange du 20 septembre : positionnement Book orienté bénéfice utilisateur et agrégation de toute la veille comme ambition ; propositions de texte, pas preuve de soumission ni de connecteurs sociaux livrés.

Les échanges sur l'utilisation générale d'OMP, les journaux de tools et les prompts bruts ne sont pas du contexte produit à partager. Aucun assistant n'a besoin de les récupérer pour lire ce dossier.

## Exclusions délibérées

| Exclu | Raison |
| --- | --- |
| `.env`, clés privées, tokens, mots de passe de démonstration, liens de reset, données utilisateur | Secrets et accès à transmettre séparément, jamais dans Git |
| Coordonnées privées de serveurs, chemins de clés et de postes personnels, infrastructure privée d'agent | Non nécessaires à la compréhension ; procédures utiles reformulées sans dépendance personnelle |
| Préférences personnelles (`feedback_autonomy`, `feedback_no_comments`, `feedback_no_self_qualifiers`, `no-em-dash`) et mémoire personnelle sans lien produit | Ne pas imposer les habitudes d'un propriétaire aux assistants de tout le groupe |
| Configurations d'agent, prompts/journaux bruts et artefacts temporaires | Bruit, données privées possibles et risque de traiter une ancienne instruction comme actuelle |
| Dépendances installées, builds, worktrees, exports de base et archives de recherche tierce | Pas du contexte ; volume et contenu non nécessaires à l'onboarding |
| Copies additionnelles de decks, images et documents natifs | Contexte de fabrication conservé ; les originaux éditables et les exports restent des livrables distincts, pas des fichiers textuels de contexte |

L'infrastructure de travail double poste/VPS était décrite dans `vps-syntheza-hermes.md`. La règle utile est conservée : travailler sur le workspace courant, synchroniser les sources par Git et ne pas modifier un autre environnement par effet de bord. Les détails privés de l'agent ne sont pas exportés.

## Limites de cette consolidation

Les références de code ont été inspectées localement sur les sujets décrits ; cela ne constitue pas un audit exhaustif de toutes les branches. Les PDF, médias, archives tierces et decks ne sont pas entièrement ré-audités à cette occasion. Les résultats de septembre sont conservés avec leur provenance, sans relancer les tests pour confirmer les observations utilisateur.

La propreté de **ce dossier** ne signifie pas que tous les anciens documents du dépôt ont fait l'objet d'un audit de confidentialité. Les documents existants sont volontairement préservés ; aucun secret provenant d'une source privée ne doit être ajouté par cette consolidation.
