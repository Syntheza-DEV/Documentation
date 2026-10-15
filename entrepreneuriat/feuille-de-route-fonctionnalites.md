# Fonctionnalités prioritaires jusqu'au jury de février 2027

**Rédigé le 15/10/2026.** Le mentor a demandé le 18/09 de « définir précisément les nouvelles fonctionnalités prioritaires ». Depuis, le produit a pivoté vers les décideurs pros (B2B). Les priorités ci-dessous ne sont plus choisies pour ajouter des fonctions. Elles servent à **obtenir les preuves que le jury attend**. Les faits viennent de [`contexte/centre-operations.md`](../contexte/centre-operations.md) (état du 10 et du 11/10) et du business plan d'octobre.

## Règle de priorisation

Une fonctionnalité entre dans la liste seulement si elle débloque l'une des quatre preuves du business plan ou la conversion d'un pilote :

| Preuve (business plan, slide 17) | Échéance | Ce qui la bloque aujourd'hui |
|---|---|---|
| **Temps réel** : une info en moins de 60 s | déc. 2026 | Latence mesurée le 10/10 : médiane de 4 min en régime normal, p90 de 22 min. Le délai vient surtout des éditeurs (caches CDN, flux RSS mis à jour en retard). Environ 45 flux répondent 403 depuis l'hôte OVH. |
| **Données** : API de news testées pendant 1 mois | déc. 2026 | Aucune API payante branchée. Pas d'AFP, de Reuters ni d'AP en flux public. |
| **Feu vert juridique** : presse et AMF | janv. 2027 | Aucun avis écrit. Le produit « informe, ne recommande jamais », mais ce n'est pas un avis juridique. |
| **Clients** : 3 pilotes, dont 2 payants | mars 2027 | Aucun pilote ouvert. L'outillage existe : page `/pilote`, pipeline de prospects, rapport de pilote, NPS à J+7 et à J+25. |

Tout le reste attend les retours des pilotes : on ne construit plus sur des hypothèses.

## Liste priorisée

| # | Fonctionnalité / chantier | Preuve servie | Fenêtre | Responsable | Critère de fin vérifiable |
|---|---|---|---|---|---|
| P0-1 | **Parcours de pilote de bout en bout** : demande sur `/pilote` → prospect → organisation pilote → onboarding métier → rapport PDF à J+30. Exécuté une fois sur un compte de test, captures horodatées à l'appui. | Clients | sem. 43–44 | Tristan (backend), Raphaël (web) | Un pilote test va jusqu'au rapport PDF en production, et le NPS de J+7 est reçu par e-mail |
| P0-2 | **E-mails sortants en production** (invitation, alertes, brief, NPS) : vérifier le SMTP en prod, avec une réception constatée dans une vraie boîte. | Clients | sem. 43 | Tristan | Un e-mail de chaque type est reçu dans une boîte Gmail |
| P0-3 | **Tableau de bord de démonstration** par métier P1 (Trading, Gestion d'actifs) : organisation « Démo », actifs et règles préréglés, sans donnée réelle de client. Il sert aux démos de prospection et au jury. | Clients, jury | sem. 43–44 | Raphaël | La démo de 15 min (script dans [prospection.md](./prospection.md)) se déroule sans préparation manuelle |
| P0-4 | **Latence : sources primaires** (banques centrales, régulateurs, communiqués d'émetteurs). WebSub là où il existe, relevé rapproché des sources officielles, réévaluation des 45 flux 403 avec le nouveau User-Agent. Mesure de la latence **par éditeur**. | Temps réel | oct.–déc. | Ilhan | Médiane < 60 s sur les sources officielles suivies, mesurée sur 7 jours et exportée |
| P0-5 | **Tableau de latence public dans la démo** : médiane et p90 par source, sur la fiche d'une info. La donnée existe déjà dans le back-office : il s'agit de l'exposer et de la dater. | Temps réel, crédibilité | nov. | Raphaël | Capture datée utilisable sur une slide du jury |
| P0-6 | **Origine des demandes de pilote** : la page `/pilote` lit `utm_source`, `utm_medium` et `utm_campaign` et les transmet avec la demande. Le formulaire ajoute « Comment avez-vous connu Syntheza ? ». Le pipeline du back-office les affiche. Aujourd'hui, le modèle `Lead` ne stocke aucune de ces informations et le web n'a aucun outil de mesure (vérifié dans le code le 15/10). Le changement de schéma est appliqué explicitement, puisqu'il n'existe pas de migrations versionnées. | KPI de communication (O1) | sem. 43 | Tristan (backend), Raphaël (web) | Une demande test arrivée par un lien UTM affiche sa source dans le back-office, en production |
| P1-1 | **Essai d'une API de news payante** pendant 1 mois (candidates du BP : NewsAPI.ai, Mediastack) via les connecteurs `API` existants, avec mesure de couverture, de latence et de coût. | Données | nov.–déc. | Ilhan | Rapport d'essai : couverture, latence, coût au mois, décision d'achat oui ou non |
| P1-2 | **Ouvrir le budget IA, mais seulement pour les pilotes** : résumés et Trust IA limités aux organisations en pilote. Le budget global reste à 0 €. | Clients | dès le 1er pilote | Tristan | Coût IA journalier par organisation visible dans le cockpit |
| P1-3 | **Retours des pilotes → backlog** : chaque demande d'un pilote devient une issue du board 1858, liée au compte rendu d'entretien. | Validation (O1) | continu | toute l'équipe | Au moins 2 cycles « retour → changement → mesure » documentés avant le jury |
| P2-1 | **Proposition de sources par les clients** : idée du 18/09 (vote communautaire), adaptée au B2B. Un client propose un flux, l'admin le valide pour tout le catalogue. Les sources privées (20 par compte) et les connecteurs existent déjà. | Couverture | après 2 pilotes | à décider | Seulement si au moins 2 pilotes le demandent |
| P2-2 | **Alertes push mobile** pour les décideurs en déplacement. Le mobile est hors du périmètre du pivot. | Clients | après retours | Ilhan | Seulement si les pilotes le demandent |

## Ce qui est volontairement écarté avant février

- **Vote communautaire sur les sources** sous sa forme du 18/09 : c'est un mécanisme grand public. Une cible de quelques dizaines de desks ne produit pas assez de votes pour qu'il soit utile. Il est remplacé par P2-1.
- **Paiement en ligne et site vitrine** (décision du propriétaire du 10/10) : les premiers contrats B2B sont annuels, sur devis et facturés hors plateforme.
- **Nouvelles cibles P2 et P3** : on ouvre le groupe suivant seulement quand P1 est prouvé (business plan, slide 8).

## Réponse au blocage « alimenter la plateforme sans faire exploser les coûts » (18/09)

Le blocage est levé par le pivot. Le **catalogue compte 317 éditeurs et 677 flux** (10/10), choisis flux par flux selon le métier. Le signal (type d'événement, impact, actifs touchés, recoupement) et le **Trust Factor V2 sont déterministes, sans appel IA**. Le budget IA journalier est à **0 $** depuis le 10/10. Le coût variable du contenu est donc quasi nul. Le coût fixe reste le serveur, estimé à 55 €/mois la première année dans le business plan (hypothèse à confirmer sur facture). Deux limites demeurent : pas d'agences de presse (AFP, Reuters, AP) en flux public, et environ 45 flux bloqués depuis l'hôte. P0-4 et P1-1 traitent ces deux points.

## Corrections de bugs (action 3 du 18/09)

Elles continuent au fil de l'eau, avec une preuve en production pour chaque correction : 71 commits sur `backend` et 50 sur `frontend-web` entre le 18/09 et le 15/10, mesurés par `git rev-list --count --since=2026-09-18 origin/main`. Ce chiffre compte les changements livrés, pas uniquement des correctifs. Points ouverts et connus :

- 2 tests backend en échec, présents avant ces travaux (`authMiddleware` et `aiService`, voir `AGENTS.md` §4) ;
- réserves de parité sur le mobile (vidage du cache au logout, liste d'exclusion de l'intercepteur) ;
- environ 45 flux en 403 depuis l'hôte (P0-4).
