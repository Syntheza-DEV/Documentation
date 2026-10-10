# Produit et roadmap

**Consolidation : 2026-10-09.** Lire ce document pour distinguer la proposition de valeur, le produit observable et les hypothèses commerciales. **Observé** signifie lu dans le code local, pas testé ni vérifié en production aujourd’hui. **Rapporté** désigne un événement daté ; **prévu / exploratoire**, une intention sans preuve de livraison.

## Proposition de valeur

Syntheza veut transformer l’information dispersée en veille stratégique utilisable : **capter, digérer, expliquer**, pour décider sans consacrer sa journée au tri. La signature la plus récente retrouvée dans les échanges de septembre est **« Moins de flux. Plus de recul. »**

- **Vision réaffirmée le 20 septembre 2026 :** agréger différents types de sources, dont actualités, réseaux sociaux et newsletters. Ne pas réduire la raison d’être du projet au seul RSS.
- **Périmètre implémenté observé :** collecte RSS, déduplication, classement, résumés IA et signal de corroboration. La vision multi-format ne prouve pas l’existence de connecteurs sociaux ou newsletter.
- **Décision du 30 juin :** la valeur payante vient de la combinaison *couverture × digestion*, pas d’un agrégateur supplémentaire. Le contenu dérivé, la sélection et la compréhension doivent justifier le prix.
- **Prudence de communication :** « Ne rien rater, partout » était la promesse de juin. Ce n’est ni une mesure d’exhaustivité ni une garantie. « La veille qui explique ses choix » a ensuite guidé les supports, avec une exigence de preuve visible.

La posture « Slow Content » des supports de septembre traduit cette préférence : moins de bruit et davantage de recul, pas une course au volume. La comparaison avec Google Alerts, Feedly, Notion AI ou les suites de veille d’entreprise sert au positionnement ; les affirmations anciennes « aucun concurrent ne fait cela » ne constituent pas un benchmark actuel démontré.

## Cibles et usages

| Cible / statut | Besoin à servir | Limite de preuve |
|---|---|---|
| Freelances, consultants, entrepreneurs : cible mise en avant depuis juillet | Suivre son secteur, ses concurrents et ses clients ; préparer une recommandation sans multiplier les outils | Le consultant indépendant en stratégie digitale proposé en septembre reste un segment à valider |
| Cadres et veilleurs : cible historique | Retrouver les informations utiles à une décision et conserver leurs sources | Le persona initial Alexandre Morel, directeur marketing, n’est pas une personne interviewée démontrée |
| Étudiants avancés et juniors : découverte / acquisition | Sourcer projets, mémoires et études de cas ; essayer le produit gratuitement | L’usage gratuit ne prouve pas une intention de payer |
| Équipes, PME, investisseurs : piste Business | Mutualiser la veille, disposer de sources dédiées et d’un service adapté | Dashboard équipe, API commerciale et SLA sont une offre envisagée, pas des engagements vendus établis |

Le passage du persona « directeur marketing » au consultant est rapporté dans les décisions de juillet, alors que certains PDF gardent l’ancien persona. Il n’existe pas, dans les éléments consolidés, de preuve suffisante pour annoncer un segment gagnant, un revenu acquis ou une base d’utilisateurs actifs chiffrée.

## Ce que le code permet de dire

| Sujet | Observé localement | Ne pas en déduire |
|---|---|---|
| Collecte | Le service d’ingestion parcourt les canaux actifs de type RSS et appelle la déduplication | Une collecte GDELT, Guardian, Bluesky, LinkedIn, X ou newsletter déjà raccordée au feed |
| Personnalisation | Le feed filtre sur les éditeurs suivis ; sans abonnement, le filtre reste ouvert. Tri par pertinence, date ou Trust, puis pagination | Une compréhension sémantique de tous les intérêts ou un assistant personnel autonome |
| Trust Factor V1 | Recherche d’articles chez d’autres éditeurs à partir de mots du titre, puis analyse IA de corroboration ; sans résultat, score neutre 50 avec explication explicite. Score, justification et provenance peuvent être conservés | Un fact-checking exhaustif, une probabilité scientifique de vérité ou la formule composite 50/30/20 des anciens slides |
| Résumés et digest | Le feed peut exposer une synthèse d’article ; le service de digest génère un résumé conservé par utilisateur à partir d’articles récents, sous garde de budget | Un digest déjà personnalisé par sujets : la sélection lue est globale. Ni l’envoi d’un e-mail ni l’exécution régulière des tâches en production ne découlent de cette seule lecture |
| Offre commerciale | Les supports décrivent Free, Pro et Business ; aucun raccordement Stripe n’a été trouvé dans la recherche ciblée du backend | Un paywall, une facturation ou les quotas du plan Free déjà appliqués |

Les résultats de recette historiques, leurs réserves et les corrections de septembre sont réunis dans [État et recette](./etat-et-recette.md). Ils ne sont pas remplacés par les anciens statuts de préparation du 1er septembre.

## Modèle commercial : hypothèses, pas traction

**Décision rapportée : Free / Pro à 4,99 € par mois / Business sur devis.** Le prix d’entrée a été abaissé après le mentorat d’avril, puis acté en juin pour réduire la friction. L’ancien plan Expert autour de 50 € a été abandonné. Les promesses « 3 synthèses par jour et 1 thème en Free », « illimité en Pro », « signaux faibles » ou « Trust détaillé » sont des variantes d’offre à ne pas présenter comme droits commerciaux déjà implémentés.

### Arithmétique à conserver

ARR signifie revenu récurrent mensuel annualisé, **pas chiffre d’affaires encaissé sur l’année**. Avec un Pro à 4,99 €/mois et un scénario Business à 1 800 €/an :

| Hypothèse | Calcul | ARR théorique |
|---|---|---|
| 15 Pro, issus de 300 comptes à 5 % de conversion | 15 × 4,99 × 12 | 898,20 € |
| 20 Pro | 20 × 4,99 × 12 | 1 197,60 € |
| 60 Pro + 1 Business | 60 × 4,99 × 12 + 1 800 | 5 392,80 € |
| 160 Pro + 4 Business | 160 × 4,99 × 12 + 7 200 | 16 780,80 € |
| 100 Pro | 100 × 4,99 × 12 | 5 988 €, **pas 100 000 €** |

Les scénarios de septembre « 1,2 k€ → 5,4 k€ → 16,8 k€ » sont compatibles avec ces hypothèses. L’ancien raccourci « 15 Pro = break-even atteint » ne découle pas du calcul : il faut d’abord définir les charges.

### Coûts et seuil de rentabilité

- Le coût de **0,40 €/Pro/mois** est une hypothèse de juin, non une comptabilité mesurée. Il donne 4,59 € de contribution, soit environ 92 % du prix ; pas une marge nette validée.
- Avec cette contribution, **21 Pro couvrent 96,39 €/mois de charges fixes**. Le seuil de 21 n’est donc pas universel. Pour 30 € d’infrastructure seule : 7 Pro ; pour 30 € plus 25 à 90 € de sourcing : 12 à 27 Pro, sous réserve de ne pas compter deux fois les mêmes coûts.
- L’ancien palier à 1 700 €/mois ne se finance pas avec 340 Pro après coûts variables : à 4,59 € de contribution, il faut 371 Pro. Ce palier appartient à l’étude de sourcing initiale, pas au dimensionnement retenu pour le projet étudiant.
- TVA éventuelle, frais de paiement, support, acquisition, travail de l’équipe et consommation des comptes gratuits ne sont pas suffisamment explicités. Prix fournisseurs, coûts IA et capacité annoncée d’un VPS restent des estimations datées, pas un engagement de capacité.

## Roadmap historique et raison de l’ordre

**Plan de juin repris dans les supports de septembre, non bilan d’avancement au 9 octobre.** Le temps écoulé ne transforme pas une phase en phase livrée.

| Phase prévue | Fenêtre annoncée | Intention et dépendance |
|---|---|---|
| P1 - MVP & bêta | Mai–juin 2026 | Rendre le parcours démontrable et recueillir des retours avant d’étendre le produit |
| P2 - Couverture | Juillet–septembre 2026 | Diversifier et mieux filtrer les sources avant de vendre une promesse trop large |
| P3 - Monétisation | Octobre–décembre 2026 | Tester Pro et le paiement après consolidation de la valeur ; cible historique d’environ 20 Pro |
| P4 - Scale | Janvier–mars 2027 | Acquisition organique et amélioration de la couche IA ; scénario de 60 Pro et un pilote Business |
| P5 - B2B & PGE5 | Avril–septembre 2027 | Explorer des contrats pilotes et préparer la défense finale ; scénario de 160 Pro et quatre Business |

**Non livrés par simple mention dans la roadmap :** détection de signaux faibles et tendances transverses, Trust V2 composite, agent autonome de veille, extension des collecteurs, monétisation Stripe et offre d’équipe. Des travaux Hermès et de communication existent par ailleurs ; ils ne prouvent pas qu’un agent autonome alimente déjà le produit.

Le plan préparatoire de deck de juillet contient d’autres dates et cibles, parfois jusqu’à janvier 2028. Ces variantes contradictoires ne valent ni nouvelle décision validée ni replanification actuelle.

## Sourcing : évolution des arbitrages

1. **Mai 2026, exploration :** benchmark très large, Apify initialement privilégié et paliers Newsroom / intelligence économique. Utile pour identifier les coûts cachés, pas comme commande d’achat actuelle.
2. **Fin juin–début juillet, recentrage rapporté :** socle RSS élargi, puis APIs publiques ciblées ; budget indicatif de sourcing 25–90 €/mois et brique payante seulement si un besoin client le justifie. Apify est écarté au profit d’une extension maison/Hermès envisagée.
3. **Contrainte durable :** qualité et diversité priment sur quantité. Accès public ne signifie pas libre réutilisation ; droits, conditions des plateformes et RGPD restent à examiner. Le scraping nominatif LinkedIn a été écarté dans le cadrage de juin.
4. **Septembre, vision réaffirmée :** réseaux sociaux et newsletters restent dans l’ambition globale. Ce retour de vision n’annule pas implicitement les limites juridiques, économiques et techniques précédentes.

GDELT, Hacker News, Guardian et Bluesky étaient des candidats, pas des connexions démontrées. Les prix et règles d’accès de Reddit, X ou des agrégateurs ont varié entre les documents : ne pas réutiliser leurs anciens tarifs sans les dater.

## Acquisition et validation à ne pas confondre

Le plan organique de juin privilégiait le réseau Epitech/professionnel et LinkedIn, complétés par communautés, contenu SEO et lancement Product Hunt. Instagram a fourni une première exécution visuelle. L’entonnoir **300 comptes → 15 Pro** est un scénario à 5 %, non un résultat ; le modèle explorait aussi 2–3 % et une variante de prix à 6,99 €.

L’apprentissage recherché est : le visiteur comprend-il la valeur, revient-il et accepte-t-il de payer ? Les vingt entretiens et le formulaire diffusé sont des éléments historiques, mais leur méthode, les résultats dépouillés et deux cycles d’itération mesurés n’ont pas été établis par les sources de jury. Un compte créé, une démo ou un lot de posts ne remplacent pas activation, rétention, conversion et churn. Voir [Jury et livrables](./jury-et-livrables.md).

## Sources et réserves

- **Code lu :** [ingestion RSS](https://github.com/Syntheza-DEV/backend/blob/af162cd726d5b1e98b8184d26cc0f491e5d5d4c9/src/services/rssIngestionService.ts), [feed](https://github.com/Syntheza-DEV/backend/blob/af162cd726d5b1e98b8184d26cc0f491e5d5d4c9/src/services/feedService.ts), [Trust Factor](https://github.com/Syntheza-DEV/backend/blob/af162cd726d5b1e98b8184d26cc0f491e5d5d4c9/src/services/trustFactorService.ts), [digest](https://github.com/Syntheza-DEV/backend/blob/af162cd726d5b1e98b8184d26cc0f491e5d5d4c9/src/services/dailyDigestService.ts). Les révisions de base ne sont pas des versions de production certifiées.
- **Décisions extraites :** `business-roadmap-sourcing.md` (juin–1er juillet 2026), `sourcing_scraping_strategy.md` (mai 2026, datation par le comparatif associé), `jury_blanc_decisions.md` (mai–1er juillet), `da_skills_syntheza.md` (2 juillet), `jury-greenlight-jul2026.md` (13 juillet).
- **Supports facultatifs :** [comparatif sourcing de mai](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/veille_scraping_comparatif.md), [Plan d’action](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/Syntheza-Plan-Action.pdf), `Syntheza-Deck-v3-BoardPlan.md` (juillet, plan préparatoire contradictoire), `generate-warmup-deck.js` (septembre).
- **Mises à jour rapportées :** échanges du 10 et du 20 septembre 2026 sur le pitch final et le Book ; provenance consolidée dans [Sources](./sources.md). Aucun dépôt du Book, résultat commercial ou avancement de phase n’est déduit de ces échanges.
