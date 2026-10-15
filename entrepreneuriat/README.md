# Entrepreneuriat : dossier de la séance de mentorat du 15/10/2026

Ce dossier rassemble les réponses de l'équipe aux actions demandées par le mentor le 18/09/2026 ([compte rendu](../note-reunion/2026-09%20Septembre/2026-09-18_reunion_note.md)). Il servira ensuite de **registre des preuves de validation marché** jusqu'au jury de février 2027.

## Ordre du jour proposé (14 h – 15 h)

| Durée | Sujet | Support |
|---|---|---|
| 10 min | Ce qui a changé depuis le 18/09 : le pivot B2B | §1 ci-dessous, business plan d'octobre |
| 15 min | Stratégie de communication, second canal, calendrier, KPI | [strategie-communication.md](./strategie-communication.md), [calendrier-editorial.xlsx](./calendrier-editorial.xlsx), [kpi-suivi.xlsx](./kpi-suivi.xlsx), [contenus/](./contenus/linkedin-s1-s5.md) |
| 15 min | Prospection et offre pilote | [prospection.md](./prospection.md), [prospects-cibles.xlsx](./prospects-cibles.xlsx) |
| 10 min | Fonctionnalités prioritaires et guide Tech 5 | [feuille-de-route-fonctionnalites.md](./feuille-de-route-fonctionnalites.md), [conformite-attentes.md](./conformite-attentes.md) |
| 10 min | Questions au mentor | §4 |

## 1. Depuis le 18/09

- **Pivot B2B (10/10/2026).** La cible est désormais les décideurs pros qui engagent de l'argent sur une info : P1 trading et gestion d'actifs, P2 finance d'entreprise, énergie et matières premières, risque et conformité. Le modèle de revenus est la licence par siège : Pro 185 €/mois, Équipe 140 €/siège dès 5 sièges, Entreprise API et SLA. Un pilote gratuit de 30 jours précède tout contrat. Toutes ces offres restent des hypothèses du business plan d'octobre. Ce pivot répond au constat du 18/09, selon lequel le produit grand public demandait un gros volume de contenu avant de séduire. Il sert aussi l'« offre B2B déjà réfléchie » évoquée ce jour-là.
- **Produit reconstruit autour du pivot**, en production, détails dans [centre-operations.md](../contexte/centre-operations.md) :
  - tableaux de bord par métier ;
  - signal d'impact et Trust Factor V2 déterministe (sans IA) ;
  - alertes, brief et journal de décisions scellé ;
  - offres et organisations ;
  - **outillage de pilote** : page `/pilote`, pipeline de prospects, NPS à J+7 et J+25, rapport de pilote PDF ;
  - API Entreprise, vérifiée en production le 11/10 ;
  - intégrations Slack et Teams, 2FA et SSO.
- **Volume** : 71 commits sur `backend` et 50 sur `frontend-web` livrés sur `main` entre le 18/09 et le 15/10 (mesuré par `git rev-list`).
- **Le blocage « contenu sans coût » est levé.** Le catalogue compte 317 éditeurs et 677 flux. Le signal et le Trust Factor ne font appel à aucune IA, et le budget IA est à 0 $/jour.
- **Ce qui n'a pas avancé** :
  - aucun client, aucun revenu, aucun pilote ouvert ;
  - aucun échange commercial consigné ;
  - pas encore de calendrier de publication appliqué.

  Les quatre audits par métier du 10/10 ont été menés **en se mettant à la place** de chaque métier. Ce ne sont pas des entretiens terrain.

## 2. Réponse aux 14 actions du 18/09

| # | Action | Réponse | Statut |
|---|---|---|---|
| 1–2 | Guide Tech 5 et alignement | Guide introuvable hors intranet ; matrice d'alignement prête, à compléter dès réception | **Bloqué : guide à récupérer** |
| 3 | Bugs | Corrections continues, points ouverts listés | Continu |
| 4 | Fonctionnalités prioritaires | 6 chantiers P0, 3 P1 et 2 P2, chacun relié à une preuve attendue par le jury | Fait |
| 5 | Alimenter Instagram | 5 premiers contenus (S1–S5) rédigés et 15 visuels rendus à la charte, sources citées | Prêt, à relire puis publier dès le 20/10 |
| 6 | Second canal | LinkedIn (page et profils des fondateurs), justifié par des sources ; TikTok écarté | Fait |
| 7 | Calendrier éditorial | 61 publications du 20/10 au 26/02 : 41 LinkedIn, 20 Instagram | Fait |
| 8 | Identité graphique | Charte déclinée en gabarits ; générateur `syntheza-skills/insta` | Fait |
| 9 | KPI | Classeur hebdomadaire social et commercial, objectifs, définitions, graphiques | Prêt, **baseline à relever aujourd'hui** |
| 10 | 2–3 publications/semaine | Calendrier : LinkedIn 2–3/sem., Instagram 1–2/sem., Noël allégé | Démarre le 20/10 |
| 11 | Premiers prospects | 44 organisations qualifiables (40 P1), séquences de messages prêtes | Contacts à partir du 19/10 |
| 12 | Documenter les échanges | Pipeline du back-office, journal, règle de preuve | Prêt |
| 13 | Communautés et testeurs | 22 communautés et événements, avec dates vérifiées | Prêt |
| 14 | Validation marché pour février | Tunnel cible : 115 contactés, 10 démos, 3 pilotes, 1 contrat au jury | Objectifs fixés |

## 3. Relevés de départ (baseline) à faire le 15/10

À saisir dans [kpi-suivi.xlsx](./kpi-suivi.xlsx), ligne `baseline` de la semaine 42. Ne rien estimer : une case vide vaut mieux qu'un chiffre inventé.

- Instagram : abonnés, nombre de publications, impressions, reach et engagements disponibles, meilleure publication.
- LinkedIn : la page Syntheza existe-t-elle ? Si oui, relever ses abonnés ; relever aussi les abonnés et relations des trois fondateurs.
- Commercial : tout échange déjà eu avec un professionnel, avec sa date réelle, saisi dans le pipeline du back-office.

## 4. Questions pour le mentor

1. **Guide Tech 5** : où le récupérer ? Les objectifs complémentaires B (stratégie et vision) et E (croissance et expansion) choisis en juillet restent-ils valables, alors que le 18/09 a reformulé O1 et O2 ?
2. **Pivot B2B** : comment le jury lira-t-il un changement de cible quatre mois avant février ? Faut-il le présenter comme un cycle « hypothèse → invalidation → pivot » de l'objectif O1 ?
3. **Preuve de traction** : si aucun client ne paie d'ici février, des pilotes en cours, des rapports de pilote et des lettres d'intention suffisent-ils ?
4. **Réseau** : pouvez-vous nous présenter à des professionnels de la gestion, du trading ou de la trésorerie, ou à des alumni de ces métiers ?
5. **Instagram** : faut-il le garder en canal secondaire pour une cible B2B, ou concentrer l'effort sur LinkedIn ?
6. **Jury de février** : date exacte, format, durée de la démo.

## 5. Ce qui démarre le lundi 19/10

- Prospection, semaine 43 : 44 comptes à qualifier, 15 invitations, messages aux communautés. Répartition par fondateur dans [prospection.md](./prospection.md) §12.
- Première publication le mardi 20/10 (S1), après relecture croisée.
- Chantiers produit P0-1, P0-2, P0-3 et P0-6 : parcours de pilote, e-mails en production, tableau de démo, origine des demandes de pilote.
- Relevé des KPI chaque lundi.

## Fichiers

| Fichier | Contenu |
|---|---|
| [strategie-communication.md](./strategie-communication.md) | Objectifs SMART, personas (hypothèses), justification des canaux, piliers, horaires, ton, conformité AMF, identité graphique, processus, KPI, sources |
| [calendrier-editorial.xlsx](./calendrier-editorial.xlsx) | Une ligne par publication (canal, compte, pilier, persona, objectif, CTA, UTM, responsable, statut, résultats) |
| [kpi-suivi.xlsx](./kpi-suivi.xlsx) | Suivi hebdomadaire social et tunnel commercial, objectifs, définitions, graphiques |
| [contenus/](./contenus/linkedin-s1-s5.md) | Textes S1–S5, visuels PNG 1080×1350, PDF de carrousel LinkedIn |
| [prospection.md](./prospection.md) | ICP, sources, séquences, guide d'entretien, script de démo, offre pilote, objections, CNIL, preuves, plan des semaines 43–44 |
| [prospects-cibles.xlsx](./prospects-cibles.xlsx) | 44 comptes, 22 communautés et événements, modèle de journal (sans données personnelles) |
| [feuille-de-route-fonctionnalites.md](./feuille-de-route-fonctionnalites.md) | Fonctionnalités prioritaires reliées aux preuves du jury |
| [conformite-attentes.md](./conformite-attentes.md) | Attentes du mentor face aux livrables, preuves et statuts ; guide Tech 5 à intégrer |
