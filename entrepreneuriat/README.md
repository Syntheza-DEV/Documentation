# Entrepreneuriat : dossier de la séance de mentorat du 15/10/2026

Ce dossier rassemble les réponses de l'équipe aux actions demandées par le mentor le 18/09/2026 ([compte rendu](../note-reunion/2026-09%20Septembre/2026-09-18_reunion_note.md)). Il sert aussi de **registre des preuves** pour le jury.

> **Référence absolue : le guide officiel Tech 5** (*EIP Student Guide, Entrepreneurship Track*, reçu le 15/10). Tout ce qu'il cite devra être cité et prouvé le jour du jury. Chaque exigence du guide, avec son statut, sa preuve et son échéance, est dans **[conformite-attentes.md](./conformite-attentes.md)**. Le guide impose **fin décembre**, pas février : 20 bêta-testeurs fin novembre, deux tours de test en novembre et en décembre, un premier engagement écrit avant fin décembre, et un envoi des livrables 48 h avant chaque séance.

Livrables attendus à la séance d'octobre :

- [note de positionnement de l'équipe](./note-positionnement-equipe.md) (brouillon à compléter) ;
- [business plan révisé](./business-plan-revision.md) ;
- [hypothèses critiques](./hypotheses-critiques.md) ;
- [fiche ICP](./fiche-icp.md).

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
| 1–2 | Guide Tech 5 et alignement | Guide reçu le 15/10 ; chaque exigence est reportée dans [conformite-attentes.md](./conformite-attentes.md) avec statut, preuve et échéance | Fait (analyse) ; beaucoup d'exigences restent à exécuter |
| 3 | Bugs | Corrections continues, points ouverts listés | Continu |
| 4 | Fonctionnalités prioritaires | 6 chantiers P0, 3 P1 et 2 P2, chacun relié à une preuve attendue par le jury | Fait |
| 5 | Alimenter Instagram | 5 premiers contenus (S1–S5) rédigés et 15 visuels rendus à la charte, sources citées | Prêt, à relire puis publier dès le 20/10 |
| 6 | Second canal | LinkedIn (page et profils des fondateurs), justifié par des sources ; TikTok écarté | Fait |
| 7 | Calendrier éditorial | 61 publications du 20/10 au 26/02 : 41 LinkedIn, 20 Instagram | Fait |
| 8 | Identité graphique | Charte déclinée en gabarits ; générateur `syntheza-skills/insta` | Fait |
| 9 | KPI | Classeur hebdomadaire social et commercial, objectifs, définitions, graphiques | Prêt ; baseline relevée pour les abonnés Instagram (5) et la page LinkedIn (0, inexistante), statistiques Instagram encore à relever |
| 10 | 2–3 publications/semaine | Calendrier : LinkedIn 2–3/sem., Instagram 1–2/sem., Noël allégé | Démarre le 20/10 |
| 11 | Premiers prospects | 44 organisations qualifiables (40 P1), séquences de messages prêtes | Contacts à partir du 19/10 |
| 12 | Documenter les échanges | Pipeline du back-office, journal, règle de preuve | Prêt |
| 13 | Communautés et testeurs | 22 communautés et événements, avec dates vérifiées | Prêt |
| 14 | Validation marché pour février | Tunnel cible : 115 contactés, 10 démos, 3 pilotes, 1 contrat au jury | Objectifs fixés |

## 3. Point de départ (baseline) au 15/10

Saisi dans [kpi-suivi.xlsx](./kpi-suivi.xlsx), ligne `baseline` de la semaine 42. Rien n'est estimé : une case vide vaut mieux qu'un chiffre inventé.

- **Instagram `@syntheza.eip`**, le seul réseau existant : 5 abonnés, 6 publications, 0 abonnement (profil public, 15/10). Les 6 publications et la bio parlent de l'ancien positionnement grand public, et l'une d'elles affiche un chiffre sans source (« 1 à 2 h perdues chaque jour »). Audit et recommandations : [stratégie §1.1](./strategie-communication.md) (archiver 4 publications, nouvelle bio).
- **LinkedIn** : aucune page Syntheza, donc 0 abonné. **Page à créer avant le 22/10.**
- **Encore à relever** : impressions, reach et engagements Instagram (dans les statistiques du compte) ; abonnés des trois profils fondateurs ; tout échange déjà eu avec un professionnel, avec sa date réelle, à saisir dans le pipeline du back-office.

Ce point de départ quasi nul est en soi une donnée utile pour le jury : toute progression d'ici février sera mesurée à partir de lui.

## 4. Questions pour le mentor

1. **Bêta-testeurs** : le guide en demande 20 qualifiés et actifs fin novembre. Avec une cible stricte de professionnels du trading et de la gestion d'actifs, c'est irréaliste. Accepterez-vous une grille à trois niveaux : cœur de cible, cible voisine, et hors cible validé par écrit ([fiche-icp.md](./fiche-icp.md)) ?
2. **Pivot B2B** : comment le jury lira-t-il un changement de cible en octobre ? Faut-il le présenter comme un cycle « hypothèse → invalidation → pivot » ?
3. **Premier engagement** : un pilote gratuit accepté par écrit, avec 2 utilisateurs nommés et une vraie liste d'actifs suivis, suffit-il comme « first customer » si son coût pour le client est documenté ?
4. **Calendrier des séances** : dates de novembre et de décembre, pour envoyer les livrables 48 h avant.
5. **Réseau** : pouvez-vous nous présenter à des professionnels de la gestion, du trading ou de la trésorerie, ou à des alumni de ces métiers ?
6. **Second canal** : le guide demande « 2 main channels, chosen and justified by where your target actually is ». Instagram tient-il face à une cible B2B finance, ou vaut-il mieux une newsletter, qui servirait aussi de canal direct ?
7. **Jury** : date exacte, format, durée de la démo.

## 5. Ce qui démarre le lundi 19/10

- Prospection, semaine 43 : 44 comptes à qualifier, 15 invitations, messages aux communautés. Répartition par fondateur dans [prospection.md](./prospection.md) §12.
- Avant le 20/10 : créer la page LinkedIn Syntheza ; mettre à jour la bio de `@syntheza.eip` ; décider de l'archivage des 4 publications d'avant le pivot ([stratégie §1.1](./strategie-communication.md)).
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
| [conformite-attentes.md](./conformite-attentes.md) | **Chaque exigence du guide Tech 5** : statut, preuve, reste à faire, échéance |
| [note-positionnement-equipe.md](./note-positionnement-equipe.md) | Rôles, complémentarité, légitimité, manques, histoire (brouillon) |
| [business-plan-revision.md](./business-plan-revision.md) | Changements depuis juin, modèle de revenus, note de prix, coûts (factures à joindre), point mort, projection 12 mois, incohérences du pptx |
| [hypotheses-critiques.md](./hypotheses-critiques.md) | Hypothèses classées par fragilité, les 3 critiques, leurs tests et seuils |
| [fiche-icp.md](./fiche-icp.md) | Modèle B2B, ICP, critères de qualification des prospects et des testeurs, canaux |
