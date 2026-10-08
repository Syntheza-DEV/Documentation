# Backend et moteur de veille

Lire cette page pour comprendre ce que le moteur fait réellement, pourquoi son modèle a changé et ce qu'une modification ne doit pas casser.

**Consolidation : 2026-10-09.** « Observé » désigne le code local consulté, pas une vérification de production. « Historique rapporté » conserve un événement daté. « Exploratoire » ne signifie ni livré ni engagé. Aucun test, build, appel IA ou contrôle de production n'a été exécuté pour cette consolidation.

## Repères immédiats

- Le backend est une API **TypeScript / Express 4 / Prisma 7.2 / PostgreSQL**, organisée en routes, contrôleurs et services. Le manifeste utilise pnpm 10.11 ; l'image actuelle est Node 20.20. Les anciens README ne suffisent pas à décrire son état.
- Le domaine partagé est **Publisher → Channel → Item → ItemOccurrence**. L'utilisateur s'abonne à un Publisher, pas à un flux technique.
- L'acquisition de veille automatisée observée est **RSS/Atom**. Gemini résume et analyse les recoupements ; il ne choisit pas les flux à interroger et ne calcule pas le ranking.
- **Trust et pertinence sont deux scores différents** : corroboration assistée par IA d'un côté, classement déterministe de l'autre.
- Les réglages persistants comptent autant que le code : un cron enregistré peut être désactivé ; le budget effectif vient de la base, pas d'un ancien montant écrit dans une note.

## Pourquoi Publisher / Channel / Item

**Historique rapporté, 17–18 mai 2026.** `Source` appartenait à un utilisateur : le même média pouvait être dupliqué pour chaque compte. Cette organisation empêchait de mutualiser proprement collecte, articles et abonnements. La refonte a séparé identité éditoriale, acquisition et contenu.

| Concept actuel | Rôle et invariant |
| --- | --- |
| `Publisher` | Média partagé ; nom et slug uniques ; plusieurs canaux possibles. |
| `Channel` | Point d'entrée d'un Publisher ; unicité `(publisherId, type, url)`. |
| `Item` | Contenu partagé ; unicité `(publisherId, fingerprint)` ; URL canonique facultative. |
| `ItemOccurrence` | Trace d'apparition sur un canal ; unicité `(channelId, externalId)`. |
| `PublisherSubscription` | Abonnement utilisateur–média ; unicité `(userId, publisherId)`. |

**Observé.** `Source` et `IngestedItem` ne sont plus des modèles Prisma. Les interactions, résumés et scores référencent `Item`. Les points d'entrée sont `/api/publishers`, `/api/channels` et `/api/publisher-subscriptions`, pas les anciennes routes sources/subscriptions.

Le DTO du feed expose encore `source`, avec un type `RSS` fixé dans le mapping, en plus de `publisher`. Ce champ de compatibilité ne prouve pas le canal réel de chaque article. `url` expose `canonicalUrl`. Les compteurs likes/commentaires viennent de Prisma, pas de valeurs fictives.

### La migration qui ne doit pas devenir une recette

**Historique rapporté, 17 mai 2026.** L'ancien modèle a été supprimé avant remappage des références. Des résumés, likes, commentaires et favoris pointaient encore sur `IngestedItem`, provoquant une violation de clé étrangère au démarrage. L'environnement scolaire a été rétabli par suppression des données concernées et réamorçage avec le seed.

Cette réparation destructive **n'est pas une stratégie de migration pour des données à conserver**. Les notes indiquent qu'un script de conversion existait mais n'a jamais été exécuté sur les vraies données. Ce script n'est plus présent dans l'arborescence Prisma locale consultée : ne pas promettre une migration réutilisable prête à lancer.

L'invariant de reprise est de conserver/remapper les identifiants référencés avant de supprimer l'ancien domaine. Le seed ne remplace ni cette conversion, ni une sauvegarde, ni une preuve de conservation des données.

## Acquisition et déduplication

**Observé.** Le job RSS sélectionne les Channels `isActive=true` et `type=RSS`, puis les traite séquentiellement avec `rss-parser`. Le parseur a un délai de 10 secondes. Les erreurs sont collectées par flux/article et reportées dans `lastError` ; `lastFetchedAt` est mis à jour même après un échec de collecte.

1. Normaliser l'entrée RSS : identifiant externe issu du GUID, sinon du lien, sinon d'un titre horodaté ; contenu RSS ou extrait disponible.
2. Normaliser l'URL : retirer une liste explicite de paramètres de suivi, le fragment et le slash terminal non racine. Ce n'est pas une résolution de l'URL canonique publiée par le site.
3. Calculer un SHA-256 sur le titre normalisé et les 500 premiers caractères du contenu, normalisés après découpe : retrait HTML, espaces regroupés, minuscules, séparateur `|`.
4. Chercher, **dans le même Publisher**, un Item avec cette empreinte ou cette URL non nulle. Une URL absente n'est pas un critère de correspondance.
5. Réutiliser l'Item et mettre à jour/créer son occurrence ; sinon créer l'Item puis son occurrence.

### Limites qui changent l'interprétation

- La déduplication est **intra-Publisher**, pas sémantique et pas mondiale : deux médias reprenant une dépêche restent deux Items, ce qui permet aussi leur recoupement.
- Retrouver un Item ne rafraîchit pas son titre ou son contenu : le chemin doublon actualise surtout l'occurrence. Un article édité n'est donc pas systématiquement réimporté.
- `canonicalUrl` est indexé, mais non unique. Recherche, création d'Item et création d'occurrence ne sont pas une transaction unique ; ne pas supposer une ingestion concurrente exactement une fois.
- `refreshInterval` et `maxItems` existent sur Channel, mais la boucle RSS actuelle n'en tient pas compte. Elle ne filtre pas non plus sur le statut du Publisher : `BLACKLISTED` seul n'est pas un arrêt du polling.
- La protection RSS vérifie protocole et nom d'hôte littéral. Cette lecture ne démontre pas une défense complète contre DNS rebinding ou redirections vers un réseau privé.

### RSS livré, social à distinguer

**Observé.** L'enum conserve `TWITTER`, `API`, `WEBHOOK`, `EMAIL`, `SCRAPER`, mais ce catalogue n'est pas une liste de connecteurs opérationnels. `twitterStubService.ts` retourne explicitement des données fictives et conserve une ancienne signature Source : ce n'est pas une ingestion X/Twitter réelle.

Le backend contient aussi des modèles et une intégration de **publication sortante Instagram pilotée par Discord**. Cela ne transforme pas le pipeline RSS en collecte de réseaux sociaux. Les contraintes éditoriales de cette intégration sont distinctes du moteur de veille.

**Exploratoire, cadrage juillet 2026.** Recherche web/Agent Search, Reddit/X et connecteurs plus larges étaient envisagés pour élargir le sourcing. Accès aux plateformes, conditions d'utilisation, qualité des sources et coûts restent des sujets séparés du choix du LLM.

### Publication Instagram : un circuit éditorial distinct

**Observé dans le service et documenté dans les contrats du module.** Le backend importe un manifeste déjà généré ; il ne génère pas lui-même ce contenu éditorial. Le parcours HTTP est réservé aux administrateurs : importer, fournir les PNG attendus, relire images/légende/sources/textes alternatifs, approuver, puis demander la publication. L'approbation n'est jamais remplacée par la simple présence d'un fichier.

Le circuit Discord est une autre entrée vers **les mêmes validations et le même service de publication**, pas un contournement :

1. Hermes fournit un message marqué `SYNTHEZA_INSTAGRAM_BUNDLE_V1` avec exactement `manifest.json` et `slide-1.png` à `slide-N.png`, au maximum cinq slides, sans trou ni fichier supplémentaire.
2. L'humain autorisé répond littéralement `GO` à ce message précis. Ce reply vaut **approbation explicite et demande de publication du bundle joint**, pas autorisation générale de publier les prochains contenus.
3. Le bot vérifie séparément serveur, salon, auteur source et auteur du GO. Le relecteur associé doit aussi correspondre à un utilisateur interne `ADMIN` ; un identifiant Discord n'est pas un `User.id`.
4. Les images sont validées/persistées avant création transactionnelle du post et de ses lignes d'images, puis `approve` et `publish` utilisent le parcours commun.

**Activation fermée par défaut.** L'intégration optionnelle reste inerte si l'une de ses six variables de configuration manque ou est malformée. L'API continue à servir ; il n'existe pas de mode partiellement autorisé. Les messages hors contrat sont ignorés, les mentions automatiques désactivées, et le contenu Discord reste une donnée JSON validée, jamais une instruction exécutée. Le webhook Discord est une notification, pas une entrée de commande.

#### États, doublons et résultat incertain

- L'import produit `NEEDS_REVIEW`. L'approbation exige le jeu exact d'images ; un verrou de ligne protège les changements de média et la revue. Seul `APPROVED` peut passer atomiquement à `PUBLISHING`.
- Les identifiants du message source et du GO sont uniques en base. Un second événement ou un redémarrage ne crée pas un nouveau post pour le même bundle ; un post complet encore `NEEDS_REVIEW`/`APPROVED` peut reprendre le parcours existant.
- `media_publish` est l'appel non idempotent : une seule tentative, sans retry aveugle. Le conteneur Meta est enregistré avant cet appel ; un timeout ou une issue ambiguë devient `PUBLISH_UNCONFIRMED`, **pas un échec certain**.
- La réconciliation consulte Meta en lecture seule, sans second `media_publish` ni retour à `APPROVED`. Elle couvre aussi un `PUBLISHING` abandonné depuis plus de 15 minutes. Un échec certain se reprend avec nouveau post/bundle et nouvelle approbation, jamais en assimilant une incertitude à cet échec.
- La notification Discord suit la confirmation Meta. Son échec ne transforme pas un post publié en échec ; un permalink manquant peut être complété séparément. Un statut seul ne remplace pas l'identifiant média ou le contrôle de réconciliation.

#### Images et réseau

Les pièces jointes Discord sont limitées aux deux hôtes CDN autorisés, en HTTPS, sans identifiants URL ni redirection ; délai de 10 secondes et limites sur taille annoncée **et octets réellement lus** : manifeste 256 Kio, slide 8 Mio. Les URL du manifeste ne sont pas téléchargées comme des sources arbitraires.

Le stockage partagé contrôle signature PNG, dimensions et budget de pixels, décode/réencode les images et génère ses propres noms de fichiers. Meta reçoit uniquement des URL d'images stockées sous le chemin prévu et l'origine HTTPS explicitement configurée par `INSTAGRAM_PUBLIC_MEDIA_ORIGIN` : un domaine public quelconque ou une adresse locale ne suffit pas.

**Limite de preuve.** Aucun GO, téléchargement réel Discord ou appel Meta n'a été exécuté pour cette consolidation ; présence du code et documentation ne prouvent pas l'activation du bot ni une publication réelle. La mention ancienne de `db push` au déploiement dans le guide Instagram ne remplace pas le contrat de démarrage actuel décrit plus bas.

Détails facultatifs : [publication Instagram](https://github.com/Syntheza-DEV/backend/blob/main/docs/instagram-publishing.md), [contrat Discord](https://github.com/Syntheza-DEV/backend/blob/main/docs/instagram-discord-publisher.md) et [machine d'états actuelle](https://github.com/Syntheza-DEV/backend/blob/main/src/services/instagramPublishingService.ts).

## Trust : corroboration, pas certificat de vérité

**Observé.** `findSimilarItems` extrait les mots du titre de plus de quatre caractères, utilise au maximum les cinq premiers pour chercher des titres contenant l'un d'eux, chez d'autres Publishers, et retient au maximum dix Items. Ce n'est ni une recherche externe, ni un index sémantique ; aucune fenêtre temporelle n'est appliquée à cette sélection.

- Sans résultat : **50/100 neutre**, sans appel Gemini, avec motif explicite « aucune source de recoupement ».
- Avec résultats : Gemini 2.5 Flash compare l'affirmation du titre et les contenus trouvés. Le prompt français définit une grille, deux exemples et une consigne anti-injection ; cela ne garantit pas l'exactitude du jugement.
- Le score est borné à 0–100. Une valeur non finie revient à 50 ; un JSON malformé peut passer par une extraction regex du score. Un score neutre ne signifie donc pas toujours qu'une corroboration a réussi.
- L'analyse et sa provenance sont désormais persistées dans `Item.metadata.trustAnalysis` avec le score : motif, date, type `ai` ou `no-corroboration`, nombre de résultats et listes corroboration/contradiction.
- La lecture restitue l'explication seulement si elle est structurellement valide et cohérente avec `Item.trustScore`. Un ancien score sans explication reste explicitement sans analyse ; le GET ne relance pas une dépense IA.

**Historique rapporté, septembre 2026.** La conservation/relecture de l'explication faisait partie des corrections de recette. Le code actuel confirme ce mécanisme ; la note de juillet qui présentait cette persistance comme future est donc dépassée. L'historique de recette est consolidé dans [État et recette](./etat-et-recette.md).

### Ce qui reste une cible

La formule « recoupement 50 / métadonnées 30 / whitelist 20 » n'est **pas** l'algorithme actuel. L'écart entre anciens livrables et code a été requalifié les 1–2 juillet 2026 : **V1 = corroboration Gemini ; V2 envisagée = score composite explicable**.

Réputation du Publisher, corroboration quantifiée, métadonnées, embeddings/clustering et historique complet des évaluations restent des pistes. `Publisher.trustScore` existe, mais n'entre pas dans le calcul actuel du Trust d'un Item. Les listes de sources sont maintenant stockées ; cela n'est pas encore un historique versionné ni une preuve indépendante de leur fiabilité.

## Ranking et feed

**Observé.** Le ranking est déterministe, sans LLM : **fraîcheur 35 % + engagement 25 % + Trust 25 % + complétude 15 %**, stocké dans `Item.relevanceScore`.

| Entrée | Calcul actuel |
| --- | --- |
| Fraîcheur | `exp(-0,029 × âge en heures)` ; âge de 168 h si `publishedAt` absent. |
| Engagement | `log2(1 + likes + 2 × commentaires) / 10`, plafonné à 1. |
| Trust | `trustScore / 100`, ou 0,5 si absent. |
| Complétude | 1 si contenu présent, sinon 0,3. |

Le recalcul automatique cible les Items **créés dans les sept derniers jours**, alors que la fraîcheur utilise leur date de publication. Cette fenêtre du job n'est pas une limite d'affichage du feed.

- Le feed filtre sur les Publishers suivis s'il existe au moins un abonnement. Sans abonnement, il retourne le catalogue global, pas un feed vide.
- Les tris observés utilisent publication, Trust ou pertinence ; les valeurs nulles sont placées à la fin, puis départagées par `createdAt` et `id`. La pertinence est le défaut du contrôleur.
- La pagination par défaut est de 20, avec une limite serveur de 50. Il n'y a plus de filtre glissant 48 h ni de quota par Publisher dans ce service.
- Contenu nettoyé de ses balises, résumé `ARTICLE`, compteurs réels et états like/bookmark sont exposés. Ne pas confondre « résumé absent » avec « contenu absent ».
- `MatchingService` contient une expérimentation basée sur les catégories des derniers likes, avec tags et types de source vides. Le contrôleur du feed appelle `FeedService`, pas ce moteur : ne pas présenter ce matching comme la personnalisation livrée.

**Historique rapporté, septembre 2026.** Le choix de conserver abonnements et pertinence tout en retirant les limites 48 h/quotas répondait aux trous de contenu observés pendant la recette. Ne pas réintroduire ces limites en les prenant pour des invariants produit.

## Résumés et digest

**Observé.** Les résumés d'articles sont partagés : `Summary.userId=null`, `type=ARTICLE`, rattachés à l'Item. Le cron prend jusqu'à 20 articles non résumés avec contenu, les plus récemment créés d'abord. Ne pas réintroduire l'ancien `userId=1` arbitraire.

Le digest est un autre objet : un `Summary` de type `DAILY_DIGEST`, lié à l'utilisateur, sans `itemId`. Son contenu conserve un JSON avec synthèse et points clés.

- Le batch cible `emailDigest=true` et `digestFrequency != never`. Il ne distingue pas réellement une cadence hebdomadaire d'une cadence quotidienne.
- Chaque génération prend les 50 Items les plus récents créés sur environ 24 h avec contenu, **sans filtrer sur les abonnements**, puis appelle Gemini. La génération se répète par utilisateur éligible.
- Le service supprime les digests du jour de cet utilisateur avant d'en créer un nouveau. C'est une intention « un par jour », pas une unicité garantie par transaction/contrainte contre des appels concurrents.
- La lecture retourne le dernier digest du jour seulement si `emailDigest` est activé. Les bornes du jour utilisent l'heure du processus, pas `UserPreference.timezone`.
- Le service consulté stocke le digest, sans envoi SMTP. La notification admin dit « envoyé », mais ne prouve pas la réception d'un email. **Le digest peut consommer des tokens** ; l'ancienne note le disant gratuit est incorrecte.

**Historique rapporté.** Le propriétaire a validé le digest lors de la recette de septembre, avec trois essais rapportés. Ce constat fonctionnel n'annule pas les limites du contrat observé ci-dessus et n'est pas une nouvelle exécution au 9 octobre.

## IA et budget : raison des garde-fous

**Historique rapporté, juin 2026.** Une facture Gemini d'environ 16 € a révélé deux défauts : tarifs de Gemini 2.0 utilisés pour des appels 2.5, et compteur RAM réinitialisé à chaque redémarrage. Une API sans erreur ne prouvait pas le respect du plafond. La correction a introduit persistance, alerte et visibilité admin.

### Contrat actuel observé

- Modèle appelé : `gemini-2.5-flash`. Le service budgétaire valorise les tokens à **0,30 $ entrée / 2,50 $ sortie par million**. Ce sont les constantes locales, pas une vérification des tarifs fournisseur du jour.
- `AiUsage` cumule en base les tokens et appels par date UTC ; cache de 30 secondes ; alerte admin au seuil 80 %, mémorisée par `alerted80`.
- Le budget vient de **`AppConfig`, singleton id=1**, modifiable par `PATCH /api/admin/ai-budget`. Défaut code et schéma : **0,11 $/jour**. L'ancien défaut 0,05 $ et le pilotage annoncé par `AI_DAILY_BUDGET_USD` ne décrivent plus ce service. La valeur réelle en base n'a pas été lue.
- `canSpend` vérifie la dépense déjà enregistrée ; ce n'est pas une réservation atomique du coût de l'appel suivant. Des appels simultanés, les caches et le coût du dernier appel empêchent d'en faire un plafond de facturation strict.
- Les compteurs utilisent les métadonnées de réponse `promptTokenCount` et `candidatesTokenCount`. Absence de métadonnées, timeout ou erreur avant comptage limitent leur portée : les confronter à la facture reste nécessaire, sans inventer un coût réel à partir des seules notes.

### Dégradation et erreurs

Les requêtes fournisseur ont un délai de 45 secondes avec annulation. Les prompts bornent chaque contenu à 4 000 caractères, mais un digest agrège jusqu'à 50 contenus. `AI_ENABLED=false` désactive les générations réelles.

Résumé/digest peuvent retourner un extrait ou une synthèse mécanique quand l'IA est désactivée ; les services batch s'arrêtent en amont si le budget est épuisé. Un résumé de repli stocké en `ARTICLE` peut ensuite être considéré comme déjà résumé : présence d'une ligne ne prouve pas une génération Gemini.

L'analyse Trust avec sources, elle, renvoie une erreur si IA indisponible ou budget épuisé ; timeout/fournisseur/indisponibilité sont distingués en HTTP. Le score neutre sans source reste un chemin déterministe sans appel IA. Aucun benchmark ancien de prix ou de volume ne vaut mesure actuelle.

## Admin et crons

**Pourquoi ces évolutions.** Le panel V1 de mai 2026 donnait une interface aux opérations utilisateurs, Publishers et statistiques déjà nécessaires à la démo et à la gestion. La V2 de juin répondait à la facture IA ; la V3 du 24 juin ajoutait déclenchement manuel, fréquences modifiables et historique pour rendre l'exécution observable.

**Observé.** Cinq jobs sont enregistrés au démarrage de l'API. Les horaires ci-dessous sont les défauts du code, pas une preuve de leur réglage en base ni de leur heure locale effective.

| Job | Défaut / variable | Travail |
| --- | --- | --- |
| `rss-ingestion` | `*/30 * * * *` / `RSS_CRON_SCHEDULE` | Collecte RSS seule, sans Gemini direct. |
| `auto-summarize` | `*/30 * * * *` / `SUMMARIZE_CRON_SCHEDULE` | Jusqu'à 20 résumés, sous budget. |
| `auto-trust-score` | `0 */2 * * *` / `TRUST_CRON_SCHEDULE` | Items sans score et avec contenu ; batch par défaut 30 via `TRUST_BATCH_SIZE`. |
| `auto-ranking` | `30 * * * *` / `RANKING_CRON_SCHEDULE` | Recalcul déterministe, sans IA. |
| `daily-digest` | `0 7 * * *` / `DIGEST_CRON_SCHEDULE` | Digests des utilisateurs éligibles, potentiellement payants. |

- `CronConfig.enabled` persiste la pause ; `schedule` persiste un override réappliqué au démarrage. Le défaut `enabled=true` ne remplace pas un état existant désactivé.
- Un lancement manuel ignore la pause. **Contrat actuel : HTTP 202 avec `runId` et `RUNNING`**, puis résultat à lire dans l'historique ; ce n'est plus le retour synchrone final décrit dans la note V3.
- `CronRun` garde trigger, statut, dates, durée, erreur et métriques métier. La route d'historique retourne les 20 derniers runs. Un statut global réussi peut coexister avec des erreurs d'articles absorbées dans un batch.
- Le scheduler tourne dans le processus API, sans verrou distribué ou protection de chevauchement visible dans `CronManager`. Une pause n'annule pas un travail déjà parti ; multiplier les instances ne garantit pas une exécution unique.
- Les routes admin sont protégées côté serveur. Le test de notification `SYSTEM` cible **tous les utilisateurs**, pas seulement l'admin qui clique.

**Historique rapporté, 24 juin.** Les cinq crons étaient laissés en pause après une désactivation destinée à couper les coûts le 2 juin. Ce n'était pas un scheduler cassé. Cela ne décrit pas leur état en octobre : aucune lecture de `CronConfig` en production n'a été faite ici.

## Auth et invariants Prisma

### Contrats à ne pas confondre

- Access token : JWT de 15 minutes ; refresh : 7 jours, secrets distincts `JWT_SECRET` / `REFRESH_TOKEN_SECRET`. Le login renvoie les jetons dans le corps et pose les cookies httpOnly pour permettre les usages web et mobile.
- Les routes protégées lisent le cookie access, sinon `Authorization: Bearer`. Le refresh lit **cookie refresh, `X-Refresh-Token` ou `body.refreshToken`** : la note historique parlant de Bearer pour le refresh est périmée.
- Les rôles `USER < MEMBER < MODO < ADMIN` sont résolus depuis l'utilisateur en base lors de l'authentification. Un rôle affiché en cache client n'accorde pas les droits serveur. L'accès aux comptes d'autrui reste soumis à ownership/ADMIN.
- Les corrections historiques de hash de mot de passe, séparation des secrets et chargement d'environnement avant les services sont des régressions à éviter, pas des fonctionnalités facultatives.

### Contraintes de schéma

- Prisma 7 lit `DATABASE_URL` via `prisma.config.ts` pour la CLI ; le runtime instancie explicitement `PrismaPg`. Le schéma déclare PostgreSQL sans URL. Ne pas réintroduire une configuration v5/v6 copiée d'un ancien guide.
- `Summary.userId` et `Summary.itemId` sont facultatifs pour séparer résumé global et digest utilisateur. La suppression d'un Item met la référence Summary à null ; likes/commentaires/bookmarks dépendent de l'Item et suivent ses cascades.
- `Item.relevanceScore` est indispensable au SQL du ranking. Sa disparition du schéma avait été détectée pendant la refonte ; changer code, schéma ou client généré isolément peut recréer ce défaut.
- Les anciens conseils « migrate deploy sans migrations », « skip-generate » et « accepter toute perte de données » ne sont pas des procédures universelles. Aucun dossier de migrations versionnées n'a été trouvé dans l'arborescence Prisma consultée.
- **Contradiction datée réconciliée :** juin signalait une migration manuelle, juillet un `db push` + seed au boot ; le Compose et le Dockerfile actuels lancent seulement `pnpm start`. Ne pas présumer une mutation automatique de la base. L'exploitation détaille le flux opérationnel ; ici, l'exigence est la compatibilité du schéma et la préservation des références.

## Ce que cette page ne certifie pas

Le budget actif, les crons activés, la volumétrie, la facture, les performances et la version réellement déployée ne sont pas connus par une lecture de code. Les anciens totaux de modèles, routes, articles ou tests ne sont pas repris comme compteurs actuels. Les recettes historiques restent datées et distinctes des vérifications de cette consolidation.

## Sources et approfondissement facultatif

Cette page contient le contexte nécessaire sans accès à une mémoire privée. Les références ci-dessous servent à retrouver le détail ; [Sources](./sources.md) explique les révisions de base et les limites de preuve.

### Code et documentation partagés

- [Schéma Prisma](https://github.com/Syntheza-DEV/backend/blob/main/prisma/schema.prisma), [config CLI](https://github.com/Syntheza-DEV/backend/blob/main/prisma.config.ts) et [client runtime](https://github.com/Syntheza-DEV/backend/blob/main/src/prisma.ts).
- [Collecte RSS](https://github.com/Syntheza-DEV/backend/blob/main/src/services/rssIngestionService.ts), [déduplication](https://github.com/Syntheza-DEV/backend/blob/main/src/services/dedupService.ts), [création d'Item](https://github.com/Syntheza-DEV/backend/blob/main/src/services/itemService.ts) et [spécification de refonte](https://github.com/Syntheza-DEV/backend/blob/main/docs/specs/2026-05-17-publisher-channel-item-data-model-design.md).
- [Trust](https://github.com/Syntheza-DEV/backend/blob/main/src/services/trustFactorService.ts), [ranking](https://github.com/Syntheza-DEV/backend/blob/main/src/services/rankingService.ts), [feed](https://github.com/Syntheza-DEV/backend/blob/main/src/services/feedService.ts) et [digest](https://github.com/Syntheza-DEV/backend/blob/main/src/services/dailyDigestService.ts).
- [Service IA](https://github.com/Syntheza-DEV/backend/blob/main/src/services/aiService.ts), [budget](https://github.com/Syntheza-DEV/backend/blob/main/src/services/aiBudgetService.ts), [CronManager](https://github.com/Syntheza-DEV/backend/blob/main/src/jobs/cronManager.ts), [admin](https://github.com/Syntheza-DEV/backend/blob/main/src/controllers/adminController.ts) et [auth](https://github.com/Syntheza-DEV/backend/blob/main/src/controllers/userController.ts).
- [Documentation technique exhaustive, juin 2026](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/backend_documentation.md) et [Moteur de veille, livrable juillet 2026](https://github.com/Syntheza-DEV/Documentation/blob/main/Documents/Syntheza-Moteur-Veille.pdf). Leur âge impose les corrections explicites de cette page, sans réécrire ces références.

### Historique nettoyé utilisé

- `backend-architecture.md` et `publisher_channel_refacto.md` — état rapporté des 17–18 mai 2026 ; `resolved-issues.md` — incidents/correctifs mars–mai 2026.
- `admin_panel_v1.md` — 24–25 mai 2026 ; `admin_panel_v2_system.md` et `gemini_budget_lessons.md` — juin 2026 ; `admin_cron_v3.md` — 24 juin 2026.
- `trust-factor-ia-architecture.md` — cadrage et corrections des 1–2 juillet 2026 ; `reference_prisma_v7_gotchas.md` — rapprochement Syntheza daté du 20 juillet 2026.
- `feedback_prod_db_migration.md` — incident du 2 juin et mise à jour du 1er juillet 2026, tous deux replacés face aux fichiers actuels.
- Corrections et recette du 9 septembre 2026 : substance reprise ci-dessus ; provenance consolidée dans [État et recette](./etat-et-recette.md) et [Sources](./sources.md), sans dépendance aux conversations privées.
