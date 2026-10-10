# Centre d'opérations - pivot B2B

**Mis à jour le 10 octobre 2026.** Suivi : issue [#114](https://github.com/EpitechPromo2027/G-EIP-600-NCE-6-1-eip-tristan.gauci/issues/114) du dépôt de rendu école. Source produit : *Syntheza_Business_Plan_Simple_185.pptx* (octobre 2026).

## Décision produit

La cible n'est plus le grand public mais les **décideurs professionnels** qui engagent de l'argent sur une info (B2B, licence par siège). Promesse : *de la news à la décision en moins de 60 s* - « Décider vite. Décider juste. » Six groupes de métiers sont visés : Trading, Gestion d'actifs (P1), Finance d'entreprise, Énergie & matières premières, Risque & conformité (P2), Médias & communication (P3).

La surface principale devient un **centre d'opérations configurable** ; le fil d'actualité reste disponible, en second plan (« Lecture »). Syntheza **informe, ne recommande jamais** (AMF) : aucune formulation d'achat ou de vente.

## Ce qui est livré (observé dans le code et en production le 10/10/2026)

| Surface | Contenu |
| --- | --- |
| Navigation (refonte du 10/10, soir) | **Plus de colonne à gauche.** Une barre du haut porte les onglets d'espaces (renommer, définir par défaut, supprimer, « + » vers l'onboarding), les six destinations, la palette de commandes (`Ctrl+K`, `/`), les séances, la cloche (tiroir d'alertes, touche `a`) et le menu avatar (réglages, règles, sources, poste, thème, back-office, déconnexion). Sur mobile, une barre basse remplace la barre du haut. Raccourcis `g o/f/s/a/b/j` |
| Web `/ops`, `/ops/w/:id` | Grille de widgets par espace, choisi par l'onglet : fil temps réel, pouls de la watchlist, alertes, radar rumeurs, brief 60 s, types d'événements, journal, santé des sources ; barre de focus par actif ; mode édition (ajout, ordre, largeur, hauteur, filtres). L'espace actif s'applique à **toutes** les pages : hors de `/ops`, un clic sur un onglet change d'espace sans quitter la page |
| `/ops/w/:id/reglages` et `/ops/w/:id/reglages/sources` | Réglages de l'espace. **Sources par espace** : tant qu'il n'est pas personnalisé, l'espace lit le préréglage de son métier, résolu en direct depuis le catalogue (Trading 17 sources, Gestion d'actifs 16, Finance d'entreprise 17, Énergie 9, Risque et conformité 11, Médias 10, au 10/10). Un clic applique le préréglage d'un métier ; on coche ou décoche ensuite source par source, avec enregistrement automatique. Une **source RSS personnelle** s'ajoute par son URL : vérification, ingestion immédiate, visible par son seul auteur (`Publisher.ownerUserId`), 20 au maximum. Elle passe par un garde anti-SSRF (`backend/src/utils/feedUrlGuard.ts`) : adresses publiques seulement, contrôle fait au moment de la connexion, redirections revalidées, 8 s, 2 Mo. Les alertes ne se déclenchent que sur l'union des sources des espaces de l'utilisateur. L'ancienne page Sources (abonnements de l'ancien fil) est supprimée et `/reglages/sources` redirige ici |
| `/flux` | Fil plein écran : Mes actifs, Tout (toutes les sources de l'espace), Favoris ; filtres rapides dans l'URL, séparateur lu/non lu, `j/k/Entrée/c/x/m` |
| `/sujets` (nouveau) | Suivi des histoires (`GET /api/ops/stories`) classées par dynamique : qui a sorti l'info en premier, combien d'éditeurs l'ont reprise et à quel délai. Les infos isolées, non reprises, sont masquées par défaut |
| `/actifs`, `/actifs/:id`, `/actifs/regles` | Liste des actifs à gauche et fiche à droite (mentions, fil, alertes, décisions), ajout, import de liste ; gestion des règles d'alerte |
| `/brief` (nouveau) | Point de pré-ouverture ou de clôture : fenêtre « depuis la clôture » (Euronext 17 h 30, week-end sauté), 12 h ou 24 h ; à la une par actif ; « Copier le brief » en texte brut, impression |
| `/journal`, `/alertes` | Journal en ajout seul exportable en CSV ; boîte d'alertes avec un onglet Système qui remplace l'ancienne page Notifications |
| `/reglages/*` | Compte (ex-Profil), Poste (thème, densité, son, séances), Sourdine (éditeurs et mots-clés), Notifications, Confidentialité, Sécurité, Données |
| Pages retirées | Fil d'actualité, Synthèse IA (budget IA à 0, remplacée par Brief), Découvrir, Notifications, Profil, Paramètres, Watchlist : **toutes les anciennes URL redirigent** vers leur remplaçante |
| Fiche décision | Horodatage publié/capté/latence, Trust Factor et provenance, raisons de l'impact, sources concordantes, consignation d'une décision |
| `/onboarding` | Première connexion après le pivot, **obligatoire une fois pour tous les comptes** (`User.onboardedAt` nul) : bienvenue, métier, actifs de départ (à cocher ou à ajouter), règles d'alerte, récapitulatif. `/ops/setup` y redirige ; relancé par le « + » des onglets d'espaces, il crée un nouvel espace par défaut sans supprimer l'existant |
| Toutes les autres pages | Refonte du 10/10 sur un kit commun (`frontend-web/src/components/pro` : en-tête de page, panneaux, onglets, tuiles de chiffres, recettes de styles). Le fil, la recherche et l'article affichent le même signal que le centre d'opérations (impact, Trust Factor, sources, rumeur), le fil est trié par défaut du plus récent au plus ancien, et la colonne de droite a été supprimée. Le dégradé violet et les titres « wordmark » ont été retirés |
| API `/api/ops/*` | Voir le README du dépôt `backend` |
| Mobile | **Hors périmètre** de ce lot |

### Moteur de signal

Enrichissement **lexical et déterministe** (`backend/src/services/ops/signal.ts`), sans appel IA :

- **types d'événements** : banque centrale, macro, résultats, M&A, notation, géopolitique et sanctions, énergie et matières premières, régulation, défaillance, incident, marchés ;
- **impact** (faible / modéré / fort) avec ses raisons affichées ;
- **actifs touchés** : mots-clés de la watchlist, cherchés **dans le titre et le chapô seulement** (une mention au fil du texte ne marque plus l'info). Sigles et tickers en majuscules, et termes courts à majuscule (« Fed »), sont recherchés à l'identique ; le libellé d'un actif ne sert de terme que s'il n'a aucun mot-clé ;
- **recoupement** : même sujet chez plusieurs éditeurs dans une fenêtre de 18 h, comparé à la première dépêche du sujet (pas de chaînage) ; chaque info indique la première dépêche et la taille du sujet ;
- **ton du texte** (positif / négatif / mitigé / neutre, lexical) : ce n'est jamais une direction de marché ni une recommandation. Il s'affiche ▲ vert, ▼ rouge, ◆ ambre ; l'infobulle précise « vocabulaire de la dépêche, pas une direction de marché » ;
- **chiffres clés** (%, points de base, montants) extraits du titre et du chapô, trois au plus, rien d'inventé. Une variation signée s'affiche telle qu'écrite : `+` en vert, `-` ou `−` en rouge ; un montant sans signe reste neutre ;
- **couleurs du signal** (10/10) : on n'utilise les couleurs sémantiques de la charte que pour des états réels. Impact fort : point et pastille rouges, avec un fond rouge très léger sur la ligne ; impact modéré : point ambre. Confiance faible en rouge, moyenne en ambre. Recoupement par au moins 3 sources (`×3`) en vert. Compteurs « Impact fort » en rouge et « Rumeurs » en ambre, seulement s'ils sont non nuls. Accélération des mentions à ×2 ou plus en ambre. Le reste demeure monochrome ;
- **Trust Factor** : le score stocké prime. À défaut, une **estimation provisoire** (fiabilité de l'éditeur + recoupement), toujours marquée « ≈ » et expliquée comme telle.

### Poste trader (audit du 10/10/2026)

- **Ligne d'info lisible d'un regard**, identique partout : actifs en tête, titre en gras si l'impact est au moins modéré, ton, chiffres clés, fiabilité affichée seulement si elle pose problème, rumeur fraîche ou « à confirmer » après 6 h, sources, éditeur. Densité compacte ou confort.
- **Séances** Paris, Francfort, Londres et NYSE en tête d'écran. Les jours fériés sont calculés par règle, et les clôtures anticipées comme l'heure d'été sont gérées.
- **Alertes** : son et notifications de bureau (activés dans Paramètres, onglet Poste), compteur dans l'onglet du navigateur. Sans plancher explicite, une règle exige un impact au moins modéré.
- **Fil temps réel** : filtres rapides conservés dans l'URL et séparateur « nouvelles depuis », partagé entre appareils. Raccourcis j, k, Entrée, c (copier pour chat), x (masquer l'éditeur), m (tout marquer vu) et ? (aide).
- **Page actif** `/ops/assets/:id`, import en masse de la watchlist, **sourdine** par éditeur ou mot-clé (Paramètres, onglet Sourdine). La sourdine s'applique au centre d'opérations, aux alertes et au fil, mais pas à la recherche.
- **Aucune cotation** : actualités uniquement, faute de licence de données de marché.
- **Calendrier économique** non livré : sans source fiable, une date fausse coûterait plus qu'une absence.

Le journal de décisions est **en ajout seul** (aucune route de modification ni de suppression) et fige la vue de l'info au moment de la décision, pour servir de piste d'audit.

## Exploitation : ce qui a changé

- **Schéma** : le workflow backend lance `prisma db push` **sans** `--accept-data-loss` avant de recréer le conteneur. Constaté le 10/10 : « Your database is now in sync ». Un diff destructif ferait échouer le déploiement, et l'ancien conteneur continuerait de servir.
- **Disque plein** (10/10, 99 % de 24 Go) : chaque déploiement laissait l'image précédente. Les workflows backend et web purgent désormais les images inutilisées avant le pull ; on est passé à 30 % après la purge. Les deux scripts masquaient aussi un `pull` en échec derrière un `|| true` : c'est corrigé. Le web recrée désormais son conteneur de force.
- **Ingestion** : aucun flux n'avait été récupéré depuis le **11/09/2026 à 12:11 UTC**. Depuis le 10/10 au soir, le relevé se fait **flux par flux** (`Channel.nextFetchAt`), avec un tic toutes les **5 s**. Un flux est relevé toutes les **15 s** si son éditeur est lu par au moins un espace, et toutes les **10 min** sinon : au 10/10, 30 flux chauds et 27 froids. Chaque relevé est une requête conditionnelle (`ETag`, `Last-Modified`) : un `304` coûte une requête sans analyse. Il passe par le garde anti-SSRF, avec un recul progressif en cas d'erreur (jusqu'à 30 min) et le respect de `Retry-After` (429/503). Un tic sans relevé n'écrit aucun `CronRun` ; l'historique est purgé à 7 jours. Un ancien horaire stocké `*/2 * * * *` est effacé au démarrage. Les alertes sont évaluées après chaque tic qui a créé des infos.
- **Temps réel** : `GET /api/ops/stream` (SSE, `Authorization: Bearer`) pousse `ingest` (éditeurs et ids des nouvelles infos), `alert` et un ping toutes les 25 s. Le web garde une connexion par onglet : le fil est rafraîchi dès l'événement, les agrégats 1,5 s plus tard. Les nouvelles lignes apparaissent en tête avec une surbrillance ; si l'utilisateur lit plus bas, une pastille « N nouvelles infos » s'affiche. Le polling ne sert plus que de secours. Mesuré le 10/10 : une info apparaît en tête du Flux 3,3 s après l'événement, dont 2,2 s d'attente serveur ; d'où la mise en cache de l'analyse par info (800 infos : 71 ms -> 23 ms à chaud). Horloges des séances, horloge LIVE et âges mis à jour chaque seconde.
- **Latence affichée** : médiane et p90 sur les 2 dernières heures, sur 24 h s'il y a moins de 10 infos. La médiane sur 24 h affichait 48 min le 10/10 à cause des rattrapages après les coupures d'ingestion ; elle était de 4 min en régime normal (p90 22 min). Le reste du délai vient des éditeurs : caches CDN (Le Figaro 10 min, La Tribune 5 min) et articles ajoutés au flux après leur date (Le Figaro, Investing).
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
