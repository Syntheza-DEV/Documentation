# Centre d'opérations — pivot B2B

**Mis à jour le 10 octobre 2026.** Suivi : issue [#114](https://github.com/EpitechPromo2027/G-EIP-600-NCE-6-1-eip-tristan.gauci/issues/114) du dépôt de rendu école. Source produit : *Syntheza_Business_Plan_Simple_185.pptx* (octobre 2026).

## Décision produit

La cible n'est plus le grand public mais les **décideurs professionnels** qui engagent de l'argent sur une info (B2B, licence par siège). Promesse : *de la news à la décision en moins de 60 s* — « Décider vite. Décider juste. » Six groupes de métiers sont visés : Trading, Gestion d'actifs (P1), Finance d'entreprise, Énergie & matières premières, Risque & conformité (P2), Médias & communication (P3).

La surface principale devient un **centre d'opérations configurable** ; le fil d'actualité reste disponible, en second plan (« Lecture »). Syntheza **informe, ne recommande jamais** (AMF) : aucune formulation d'achat ou de vente.

## Ce qui est livré (observé dans le code et en production le 10/10/2026)

| Surface | Contenu |
| --- | --- |
| Web `/ops` | Grille de widgets par espace : fil temps réel, pouls de la watchlist, alertes, radar rumeurs, brief 60 s, types d'événements, journal, santé des sources ; barre de focus par actif ; mode édition (ajout, ordre, largeur, hauteur, filtres) |
| Fiche décision | Horodatage publié/capté/latence, Trust Factor et provenance, raisons de l'impact, sources concordantes, consignation d'une décision |
| `/onboarding` | Première connexion après le pivot, **obligatoire une fois pour tous les comptes** (`User.onboardedAt` nul) : bienvenue, métier, actifs de départ (à cocher ou à ajouter), règles d'alerte, récapitulatif. `/ops/setup` y redirige ; relancé depuis la watchlist, il crée un nouvel espace par défaut sans supprimer l'existant |
| Toutes les autres pages | Refonte du 10/10 sur un kit commun (`frontend-web/src/components/pro` : en-tête de page, panneaux, onglets, tuiles de chiffres, recettes de styles). Le fil, la recherche et l'article affichent le même signal que le centre d'opérations (impact, Trust Factor, sources, rumeur), le fil est trié par défaut du plus récent au plus ancien, et la colonne de droite a été supprimée. Le dégradé violet et les titres « wordmark » ont été retirés |
| `/ops/watchlist`, `/ops/alerts`, `/ops/journal` | Actifs et mots-clés, règles d'alerte, boîte d'alertes, journal en ajout seul exportable en CSV |
| API `/api/ops/*` | Voir le README du dépôt `backend` |
| Mobile | **Hors périmètre** de ce lot |

### Moteur de signal

Enrichissement **lexical et déterministe** (`backend/src/services/ops/signal.ts`), sans appel IA :

- **types d'événements** : banque centrale, macro, résultats, M&A, notation, géopolitique et sanctions, énergie et matières premières, régulation, défaillance, incident, marchés ;
- **impact** (faible / modéré / fort) avec ses raisons affichées ;
- **actifs touchés** : mots-clés de la watchlist. Sigles et tickers en majuscules, et termes courts à majuscule (« Fed »), sont recherchés à l'identique ; le libellé d'un actif ne sert de terme que s'il n'a aucun mot-clé ;
- **recoupement** : même sujet chez plusieurs éditeurs dans une fenêtre de 18 h, comparé à la première dépêche du sujet (pas de chaînage) ;
- **Trust Factor** : le score stocké prime. À défaut, une **estimation provisoire** (fiabilité de l'éditeur + recoupement), toujours marquée « ≈ » et expliquée comme telle.

Le journal de décisions est **en ajout seul** (aucune route de modification ni de suppression) et fige la vue de l'info au moment de la décision, pour servir de piste d'audit.

## Exploitation : ce qui a changé

- **Schéma** : le workflow backend lance `prisma db push` **sans** `--accept-data-loss` avant de recréer le conteneur. Constaté le 10/10 : « Your database is now in sync ». Un diff destructif ferait échouer le déploiement, et l'ancien conteneur continuerait de servir.
- **Disque plein** (10/10, 99 % de 24 Go) : chaque déploiement laissait l'image précédente. Les workflows backend et web purgent désormais les images inutilisées avant le pull ; on est passé à 30 % après la purge. Les deux scripts masquaient aussi un `pull` en échec derrière un `|| true` : c'est corrigé. Le web recrée désormais son conteneur de force.
- **Ingestion** : aucun flux n'avait été récupéré depuis le **11/09/2026 à 12:11 UTC**. Elle tourne de nouveau toutes les **2 minutes**, avec 6 éditeurs en parallèle et les flux d'un même éditeur en série. Les alertes sont évaluées après chaque passage.
- **Catalogue de sources pros** : 34 éditeurs et 41 flux (banques centrales, régulateurs, presse financière, énergie, crypto, cyber) sont créés au démarrage s'ils manquent. Rien d'existant n'est modifié. Le 10/10, 3 flux sur 57 étaient en erreur depuis l'hôte.
- **Coût IA nul** (décision du 10/10, aucun budget) : au premier démarrage du pivot, le budget IA journalier est passé à **0 USD**. Aucun appel Gemini ne peut partir ; résumés et digest retombent sur du texte non IA, et l'analyse Trust renvoie « budget épuisé » (constaté en prod). Ce réglage est appliqué une seule fois : ensuite, un admin peut rouvrir un budget dans *Admin → Système*.
- **Limite de requêtes** : les routes `/api/ops/*` disposent d'un budget dédié de 2000 requêtes par 15 min et par IP. Plusieurs widgets interrogent l'API à intervalles courts.

## Recette en production (10/10/2026, compte de recette USER dédié)

Le parcours suivant est passé sur `https://syntheza.ovh` et `https://api.syntheza.ovh` : connexion → onboarding Trading (10 actifs, 4 règles, 8 widgets) → fil enrichi sur données réelles → 2 alertes déclenchées et notifiées → fiche décision → décision consignée → journal et export CSV (`text/csv`, BOM, `;`). Une modification de décision renvoie bien 404. Un passage d'ingestion dure environ 4 s.

Limites connues :

- La **latence médiane** (≈ 2 h ce jour-là) inclut le rattrapage des nouveaux flux. Elle ne mesure donc pas encore le pipeline en régime établi.
- L'objectif « < 60 s » reste borné par la fréquence de publication RSS des éditeurs.
- La **vélocité** de la watchlist restera vide tant que la fenêtre précédente ne contient pas de données (24 h après la reprise).
- Le Trust Factor IA est **indisponible** tant que le budget IA reste à 0.
