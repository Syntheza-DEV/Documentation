> Audit du 10/10/2026 (sources vérifiées en direct depuis un poste local ; l'hôte de prod en bloque certaines, voir centre-operations.md).

# Audit domaine : Médias & communication (MEDIA_COMMS)

Date des mesures : 10/10/2026. Tous les flux ci-dessous ont été récupérés avec `fetchFeed` (garde anti-SSRF de production), environ 600 URLs testées en 6 passes. "Age" = âge de l'item le plus récent au moment du test. Fichier de test jetable supprimé.

Verdict en une ligne : le préréglage actuel est un préréglage de **journaliste économique** (10 sources, toutes en rubrique économie ou monde) avec un moteur de signal **calibré finance**. Un journaliste généraliste ou un directeur de la communication n'y trouve ni politique, ni justice, ni santé, ni sport, ni culture, ni climat, ni veille de marque ; et les types d'événements, l'impact et le ton y sont faux ou muets sur l'essentiel de l'actualité non financière (preuves en F).

---

## A. Persona

Deux profils partagent le métier, avec des écrans différents. Il faut deux sous-modèles (ou un choix à l'onboarding) : "Rédaction" et "Communication".

### A1. Journaliste / rédacteur en chef / secrétaire de rédaction (desk web ou agence)
- **Décisions** : publier ou attendre ; quel angle ; quelle source citer ; "est-ce confirmé ?" ; quoi mettre en une ; quel sujet suivre en continu ; à qui rattacher un démenti.
- **Ce qui le fait bouger** : un communiqué d'une source primaire (Élysée, ministère, tribunal, préfecture, OMS, entreprise) avant la presse ; une dépêche reprise par 3 éditeurs en 10 minutes ; "selon nos informations" chez un concurrent (il faut confirmer ou suivre) ; un démenti ; un chiffre (morts, millions d'euros, pourcentage) ; la mort d'une personnalité ; une décision de justice ; un résultat d'élection.
- **Sa journée** : *pré-prise de poste (6 h-8 h)* : revue de ce qui s'est passé de nuit (international, Asie, États-Unis), agenda du jour (conseil des ministres, audiences, matchs, sorties). *Session* : fil continu filtré par rubrique, alertes sur ses sujets, vérification de rumeurs, suivi de la propagation d'une histoire (qui l'a sortie, qui l'a reprise, à quel délai). *Bouclage (17 h-19 h)* : synthèse de la journée, ce qui reste à suivre le soir et le lendemain. *Hebdo* : sujets qui ont duré, sources qui ont été premières le plus souvent.
- **Ce qui le ferait payer** : être prévenu en avance (latence réelle, pas 48 min de médiane), savoir si une info est confirmée ou isolée, et un fil **couvrant toutes les rubriques** sans le bruit des agrégateurs. Aujourd'hui, ne couvrant que l'économie, le produit ne lui sert pas.

### A2. Directeur / chargé de communication, relations presse, affaires publiques, communication de crise
- **Décisions** : répondre ou se taire ; publier un communiqué ou un droit de réponse ; activer la cellule de crise ; alerter le dirigeant ; ajuster le calendrier d'annonces (ne pas publier le jour d'une crise) ; saisir un régulateur.
- **Ce qui le fait bouger** : une mention de sa marque ou de son dirigeant en ton négatif ; une enquête ou une plainte ; un rappel produit ; une fuite de données ; un appel au boycott ; la communication d'un concurrent (communiqué, lancement, nomination) ; un régulateur (CNIL, DGCCRF, Autorité de la concurrence, Arcom, Défenseur des droits, FTC) ; l'actualité de l'industrie des médias (changement de ligne éditoriale, acquisition d'un titre, audiences).
- **Sa journée** : *pré-ouverture* : revue de presse (marque, concurrents, secteur, dirigeants) ; *session* : veille et alertes de crise, réponse ; *clôture* : brief de ce qui a circulé (à envoyer au comité de direction) ; *hebdo* : baromètre de ton, part de voix vs concurrents, rapport à la direction.
- **Ce qui le ferait payer** : **mentions de sa marque** réellement fiables (sans homonymes), détection de ton négatif et de pic de volume, comparaison concurrents, brief envoyable au comité. Le tout sans abonnement à une plateforme de veille à plusieurs milliers d'euros.

---

## B. Sources

### B1. Préréglage actuel (`SOURCE_PRESETS.MEDIA_COMMS`, `professions.ts:154`) : 10 slugs
`le-monde`, `le-figaro`, `franceinfo`, `bbc-news`, `the-guardian`, `france-24`, `challenges`, `la-tribune`, `al-jazeera`, `bfm-economie`.

Problèmes (vérifiés dans `proSourceCatalog.ts`) :
- Chaque slug ne porte qu'**une ou deux rubriques** : Le Monde = économie + international ; Le Figaro = économie seule ; franceinfo = économie seule ; BBC = business + world ; Guardian = `uk/business` seule ; France 24 = `eco-tech` seule ; BFM = économie. **Aucun flux politique, société, justice, santé, sport, culture, climat, science.**
- `challenges`, `la-tribune`, `bfm-economie` n'ont pas leur place dans un préréglage "rédaction" ; ils sont acceptables pour un journaliste économique seulement. Garder `la-tribune` et `challenges` dans un sous-préréglage "Éco/finance", les sortir du préréglage généraliste.
- Aucune source primaire (Élysée, tribunaux, régulateurs de la communication, OMS), aucune presse média/PR, aucun vérificateur de faits, aucune presse régionale.
- Les tech (Verge, Numerama, Ars, TechCrunch, Register, TechRadar, MIT TR, Engadget, VentureBeat, Guardian technology, BBC technology, Le Monde technologies) existent **en base de production** (hérités d'avant le pivot) mais **pas dans le catalogue** : ils ne sont donc dans aucun préréglage et n'ont pas de slug. Les rattacher au catalogue (slugs `the-verge`, `numerama`...) coûte une ligne chacun.

### B2. Défaut de modèle à trancher AVANT d'ajouter des flux (P0)
Les sources d'un espace sont choisies **par éditeur** (`Workspace.publisherIds`, `schema.prisma:527`) et le recoupement compte les **éditeurs distincts** (`clusterStories`, clé `publisherId`).
- Ajouter 15 flux de rubriques au slug `le-monde` (politique, sport, culture...) les ajoute aussi aux préréglages Trading, Gestion d'actifs et Finance d'entreprise qui lisent `le-monde` : du bruit sport et culture dans un poste de trader.
- Créer un second éditeur "Le Monde (actualité)" règle le bruit mais **gonfle le recoupement** (Le Monde x2 = "2 sources" pour une seule rédaction) et fausse la confiance et les "sources concordantes".
- **Recommandation** : sélection **au niveau du flux (Channel)** dans les préréglages et les réglages de sources (`sourceFeeds`: `{slug, feedUrls?}`), le recoupement restant au niveau éditeur. Repli acceptable : un éditeur de catalogue unique par marque, et une propriété `section` sur le Channel filtrable par préréglage. À défaut, ne pas ajouter les flux de rubrique aux slugs existants mais créer des slugs distincts ET dédupliquer le recoupement par domaine racine (`lemonde.fr`).

### B3. Nouvelles sources vérifiées (toutes : le flux s'analyse, a des items, item le plus récent < 7 j sauf mention "institutionnel faible fréquence" < 30 j)

Catégories existantes : INSTITUTIONS, GENERAL_PRESS, FINANCIAL_PRESS, CYBER. **Catégories nouvelles justifiées** (les 6 catégories actuelles ne permettent pas de filtrer sport, culture, santé, média) : `SPORT`, `CULTURE`, `SCIENCE_HEALTH`, `TECH`, `MEDIA_INDUSTRY`, `FACT_CHECK`. Le type `SourceCategory` et le sélecteur du réglage de sources les acceptent par simple extension d'union (à vérifier côté front).

Notation `section:items/âge`. Les trustScore suivent la grille du brief : primaire/officiel 90+, grande presse 80-88, spécialisée 70-80, agrégateur ou partisan 50-70.

#### B3.1 Presse générale française : étendre ou créer
| Nom | slug | domaine | catégorie | trust | flux (items/âge) |
|---|---|---|---|---|---|
| franceinfo (étendre `franceinfo`) | franceinfo | francetvinfo.fr | GENERAL_PRESS | 84 | `titres.rss` 28/0,3h ; `politique.rss` 19/1,1h ; `monde.rss` 20/0,8h ; `faits-divers.rss` 20/0,1h ; `societe.rss` 20/0,3h ; `sante.rss` 20/2,5h ; `sports.rss` 20/1,0h ; `culture.rss` 20/0,1h ; `sciences.rss` 20/0,1h ; `environnement.rss` 20/0,4h ; `internet.rss` 20/0,8h ; `france.rss` 20/1,7h ; `elections.rss` 17/1,1h ; `monde/europe.rss` 20/2,8h ; `monde/proche-orient.rss` 20/1,5h ; `monde/usa.rss` 20/1,6h ; `monde/russie.rss` 20/2,5h ; `monde/asie.rss` 20/0,8h ; `monde/afrique.rss` 20/67h (faible fréquence, OK) ; `culture/cinema.rss` 20/6,9h ; `culture/musique.rss` 20/0,2h ; `decouverte.rss` 20/2,6h. (Préfixe `https://www.francetvinfo.fr/`.) |
| Le Monde (étendre `le-monde`) | le-monde | lemonde.fr | GENERAL_PRESS | 88 | `rss/une.xml` 16/0,2h ; `politique/rss_full.xml` 20/2,6h ; `societe` 20/2,1h ; `planete` 20/5,9h ; `sciences` 20/0,6h ; `culture` 20/0,6h ; `sport` 20/0,4h ; `idees` 20/3,1h ; `pixels` 20/3,1h ; `justice` 20/12,1h ; `sante` 20/2,1h ; `les-decodeurs` 20/7,6h ; `actualite-medias` 20/2,7h ; `climat` 20/36h. Rejeté : `m-actu` (dernier item il y a 4395 h). |
| Le Figaro (étendre `le-figaro`) | le-figaro | lefigaro.fr | GENERAL_PRESS | 82 | `figaro_flash-actu.xml` 20/0,0h ; `figaro_actualites-a-la-une.xml` 20/1,1h ; `figaro_politique` 20/2,2h ; `figaro_international` 20/2,2h ; `figaro_actualite-france` 20/2,6h ; `figaro_sante` 20/5,7h ; `figaro_culture` 20/22,8h ; `figaro_sport` 20/0,3h ; `figaro_football` 20/0,3h ; `figaro_rugby` 20/0,6h ; `figaro_tennis` 20/1,6h ; `figaro_sciences` 20/9,9h ; `figaro_environnement` 20/9,9h ; `figaro_high-tech` 20/1,1h ; `figaro_medias` 20/22,5h ; `figaro_cinema` 20/1,2h ; `figaro_musique` 20/3,2h ; `figaro_livres` 20/2,3h ; `figaro_vox` (tribunes) 20/22,2h ; `figaro_elections` 20/22,7h ; `figaro_faits-divers` 50/73,6h ; `figaro_flash-eco` 20/0,2h. Rejeté : `figaro_histoire` (6504 h), `figaro_secteur_medias`/`figaro_actualite-monde` (404). |
| Libération | liberation | liberation.fr | GENERAL_PRESS | 80 | `arc/outboundfeeds/rss-all/collection/accueil-une/?outputType=xml` 20/1,1h. Les flux de rubrique (`politique`, `societe`, `culture`, `international`, `sports`...) répondent 500 (testés à 2 reprises) : rejetés. |
| Ouest-France | ouest-france | ouest-france.fr | GENERAL_PRESS | 78 | `rss-en-continu.xml` 10/0,1h ; `rss/une` 10/0,1h ; `rss/france` 10/0,4h ; `rss/monde` 10/0,9h ; `rss/sport` 200/0,0h. Autres rubriques en 404. 10 items seulement par flux : relevé fréquent requis. |
| 20 Minutes | 20-minutes | 20minutes.fr | GENERAL_PRESS | 72 | `feeds/rss-une.xml` 30/2,3h ; `rss-monde` 20/0,5h ; `rss-politique` 20/2,6h ; `rss-sport` 20/0,4h ; `rss-high-tech` 20/1,4h ; `rss-sante` 20/4,6h ; `rss-societe` 20/4,9h ; `rss-economie` 20/4,5h ; `rss-planete` 20/8,4h ; `rss-culture` 20/8,6h ; `rss-people` 20/3,1h ; `rss-sciences` 20/45h. 403 : `rss-actu-france`, `rss-justice`, `rss-police`, `rss-medias`. |
| BFMTV (réutiliser le slug `bfm-economie` ou nouvel éditeur `bfmtv`) | bfmtv | bfmtv.com | GENERAL_PRESS | 76 | `rss/news-24-7/` 30/0,1h ; `rss/politique/` 30/0,4h ; `rss/international/` 30/0,1h ; `rss/police-justice/` 30/0,7h ; `rss/societe/` 30/0,6h ; `rss/tech/` 30/0,2h ; `rss/sante/` 30/7,1h ; `rss/people/` 30/9,6h. `rss/sport/` 404. |
| RMC Sport | rmc-sport | rmcsport.bfmtv.com | SPORT | 74 | `rss/fil-sport/` 30/0,3h ; `rss/football/` 30/0,0h ; `rss/rugby/` 30/6,9h |
| TF1 Info | tf1-info | tf1info.fr | GENERAL_PRESS | 78 | `feeds/rss-une.xml` 100/0,1h |
| L'Express | l-express | lexpress.fr | GENERAL_PRESS | 76 | `arc/outboundfeeds/rss/alaune.xml` 100/1,5h ; `monde.xml` 100/1,5h ; `politique.xml` 100/35,7h ; `societe.xml` 100/45h ; `culture.xml` 100/9,7h |
| L'Obs | nouvel-obs | nouvelobs.com | GENERAL_PRESS | 76 | `rss.xml` 200/4,1h |
| Mediapart | mediapart | mediapart.fr | GENERAL_PRESS | 80 | `articles/feed` 10/0,9h (teasers : titre et chapô seulement, paywall ; c'est le signal "scoop" qui compte) |
| Europe 1 | europe-1 | europe1.fr | GENERAL_PRESS | 70 | `rss.xml` 50/0,3h |
| France Inter | france-inter | radiofrance.fr | GENERAL_PRESS | 84 | `franceinter/rss` 20/0,2h (l'ancienne URL `franceinter.fr/rss` dépasse 2 Mo : rejetée) |
| France Culture | france-culture | radiofrance.fr | CULTURE | 84 | `franceculture/rss` 20/0,2h |
| RFI | rfi | rfi.fr | GENERAL_PRESS | 84 | `fr/rss` 23/0,6h ; `fr/europe/rss` 30/7,9h ; `fr/moyen-orient` 30/0,9h ; `fr/ameriques` 30/8,2h ; `fr/asie-pacifique` 30/4,8h ; `fr/afrique` 23/4,5h ; `fr/france` 30/8,6h ; `fr/economie` 30/5,1h ; `fr/science` 30/6,5h ; `fr/sport` 15/0,7h ; `fr/culture` 15/4,2h ; `fr/environnement` 30/30h. Rejeté : `fr/technologies` (échec). |
| France 24 (étendre `france-24`) | france-24 | france24.com | GENERAL_PRESS | 82 | `fr/rss` 24/0,3h ; `fr/monde/rss` 24/0,4h ; `fr/europe/rss` 30/0,3h ; `fr/france/rss` 30/1,6h ; `fr/moyen-orient/rss` 30/4,3h ; `fr/ameriques/rss` 30/2,9h ; `fr/asie-pacifique/rss` 30/3,7h ; `fr/afrique/rss` 30/7,0h ; `fr/culture/rss` 30/5,5h ; `en/rss` 24/0,1h ; `en/europe/rss` 30/7,4h. Rejetés : `fr/planete` (14 398 h), `fr/sports` (12 282 h). |
| Euronews | euronews | euronews.com | GENERAL_PRESS | 78 | `fr.euronews.com/rss` 50/0,3h ; `fr.euronews.com/rss?level=vertical&name=my-europe` 50/1,1h ; `euronews.com/rss` 50/0,1h ; `euronews.com/rss?level=theme&name=sport` 50/0,2h. Rejetés : thème `culture` (73 000 h), `green`, `health`. |
| Courrier international | courrier-international | courrierinternational.com | GENERAL_PRESS | 80 | `feed/all/rss.xml` 20/1,9h (presse étrangère traduite) |
| Slate.fr | slate-fr | slate.fr | GENERAL_PRESS | 68 | `rss.xml` 22/0,2h |
| HuffPost France | huffpost-fr | huffingtonpost.fr | GENERAL_PRESS | 66 | `feeds/index.xml` 20/0,2h |
| L'Humanité | l-humanite | humanite.fr | GENERAL_PRESS | 72 | `feed` 20/0,9h (ligne éditoriale marquée : l'indiquer) |
| CNews | cnews | cnews.fr | GENERAL_PRESS | 60 | `rss.xml` 100/0,1h (ligne éditoriale marquée, utile à surveiller en comm' ; ne pas mettre dans le préréglage par défaut) |
| Le JDD | le-jdd | lejdd.fr | GENERAL_PRESS | 60 | `rss.xml` 50/1,4h (idem) |
| Valeurs actuelles | valeurs-actuelles | valeursactuelles.com | GENERAL_PRESS | 50 | `feed` 10/2,1h (idem) |
| Paris Match | paris-match | parismatch.com | GENERAL_PRESS | 62 | `rss.xml` 50/0,0h (people, utile pour la communication de personnalités) |
| The Conversation FR | the-conversation-fr | theconversation.com | SCIENCE_HEALTH | 82 | `fr/articles.atom` 50/25h (analyses d'universitaires) |

#### B3.2 Presse régionale (tous GENERAL_PRESS, trust 72-76 ; indispensable pour "être le premier" sur un fait divers local)
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Sud Ouest | sud-ouest | sudouest.fr | 74 | `rss.xml` 20/0,4h |
| La Dépêche du Midi | la-depeche | ladepeche.fr | 74 | `rss.xml` 100/0,0h |
| Midi Libre | midi-libre | midilibre.fr | 74 | `rss.xml` 100/0,0h |
| L'Indépendant | l-independant | lindependant.fr | 72 | `rss.xml` 100/0,5h |
| Nice-Matin | nice-matin | nicematin.com | 72 | `rss` 50/0,1h |
| DNA (Dernières Nouvelles d'Alsace) | dna | dna.fr | 74 | `rss` 20/0,1h |
| L'Alsace | l-alsace | lalsace.fr | 72 | `rss` 20/0,1h |
| Le Progrès | le-progres | leprogres.fr | 74 | `rss` 20/0,2h |
| Le Dauphiné libéré | le-dauphine | ledauphine.com | 74 | `rss` 20/0,7h |
| L'Est républicain | l-est-republicain | estrepublicain.fr | 72 | `rss` 20/0,2h |
| La Voix du Nord | la-voix-du-nord | lavoixdunord.fr | 74 | `rss.xml` 10/0,1h |
| actu.fr (réseau local) | actu-fr | actu.fr | 70 | `rss.xml` 30/0,1h |
Rejetés (erreur) : Le Télégramme (403), La Dépêche `/rss/une.xml` (404), La Croix (boucle de redirections), L'Opinion (404), Marianne (403), RTL (404), LCI (404), L'Équipe (404 sur 3 URLs), Les Échos (404/refus connu), Le Point (404/403), Le Parisien (404 sur les 6 rubriques testées ; seule la structure `outboundfeeds` ne répond plus), Le Soir/La Libre/RTBF (invalide ou 404), Swissinfo (410), Stratégies (404).

#### B3.3 Presse internationale
| Nom | slug | domaine | catégorie | trust | flux (items/âge) |
|---|---|---|---|---|---|
| BBC News (étendre `bbc-news`) | bbc-news | bbc.co.uk | GENERAL_PRESS | 86 | `news/rss.xml` 28/0,0h ; `news/politics` 62/5,0h ; `news/health` 51/9,8h ; `news/science_and_environment` 41/2,5h ; `news/technology` 21/0,2h ; `news/entertainment_and_arts` 27/4,3h ; `news/world/europe` 25/1,1h ; `news/world/us_and_canada` 23/0,2h ; `sport/rss.xml` 64/0,4h ; `sport/football/rss.xml` 77/0,4h (préfixe `https://feeds.bbci.co.uk/`) |
| The Guardian (étendre) | the-guardian | theguardian.com | GENERAL_PRESS | 84 | `world/rss` 45/0,2h ; `international/rss` 113/0,2h ; `uk/rss` 143/0,1h ; `politics/rss` 21/4,4h ; `environment/rss` 25/1,6h ; `science/rss` 25/23h ; `uk/culture/rss` 65/4,1h ; `uk/sport/rss` 49/0,0h ; `media/rss` 13/3,0h ; `media/mediabusiness/rss` 20/5,6h ; `us-news/rss` 33/0,3h ; `world/france/rss` 20/6,4h |
| New York Times | new-york-times | nytimes.com | GENERAL_PRESS | 88 | `rss.nytimes.com/services/xml/rss/nyt/HomePage.xml` 17/0,4h ; `World.xml` 58/0,1h ; `Politics.xml` 20/0,4h ; `Health.xml` 20/1,7h ; `Climate.xml` 46/13h. (`Media.xml` 404.) |
| Washington Post | washington-post | washingtonpost.com | GENERAL_PRESS | 86 | `feeds.washingtonpost.com/rss/world` 16/7,1h ; `rss/politics` 40/3,4h |
| NPR | npr | npr.org | GENERAL_PRESS | 82 | `feeds.npr.org/1001/rss.xml` 10/4,1h ; `1004/rss.xml` 10/4,4h |
| Sky News | sky-news | news.sky.com | GENERAL_PRESS | 78 | `feeds.skynews.com/feeds/rss/home.xml` 10/1,8h ; `world.xml` 7/9,2h |
| Deutsche Welle | deutsche-welle | dw.com | GENERAL_PRESS | 82 | `rss.dw.com/rdf/rss-en-all` 139/1,1h (flux FR : invalide) |
| Politico Europe | politico-eu | politico.eu | GENERAL_PRESS | 80 | `feed/` 10/1,4h |
| Axios | axios | axios.com | GENERAL_PRESS | 76 | `feeds/feed.rss` 100/3,3h |
| El País | el-pais | elpais.com | GENERAL_PRESS | 80 | `feeds.elpais.com/mrss-s/pages/ep/site/elpais.com/portada` 146/0,4h |
| ANSA | ansa | ansa.it | GENERAL_PRESS | 80 | `sito/ansait_rss.xml` 28/0,2h (agence italienne) |
| The Telegraph | the-telegraph | telegraph.co.uk | GENERAL_PRESS | 74 | `rss.xml` 120/0,5h |
| The Independent | the-independent | independent.co.uk | GENERAL_PRESS | 76 | `news/world/rss` 43/0,1h |
| The Economist | the-economist | economist.com | GENERAL_PRESS | 86 | `international/rss.xml` 299/90h ; `the-world-this-week/rss.xml` 300/51h (hebdomadaire : faible fréquence, < 7 j) |
| Foreign Policy | foreign-policy | foreignpolicy.com | GENERAL_PRESS | 78 | `feed/` 25/19h |
| South China Morning Post | scmp | scmp.com | GENERAL_PRESS | 76 | `rss/91/feed` 50/1,0h |
| The Japan Times | japan-times | japantimes.co.jp | GENERAL_PRESS | 76 | `feed/` 30/11h |
| CNA (Singapour) | channel-news-asia | channelnewsasia.com | GENERAL_PRESS | 78 | `rssfeeds/8395986` 20/0,7h |
| ABC News (Australie) | abc-news-au | abc.net.au | GENERAL_PRESS | 82 | `news/feed/51120/rss.xml` 25/5,7h |
| CBC | cbc-news | cbc.ca | GENERAL_PRESS | 82 | `webfeed/rss/rss-topstories` 20/2,0h |
| Kyiv Independent | kyiv-independent | kyivindependent.com | GENERAL_PRESS | 74 | `news-archive/rss/` 15/1,1h |
| Financial Times (étendre `financial-times`) | financial-times | ft.com | FINANCIAL_PRESS | 88 | `rss/world` 25/2,6h |
| Al Jazeera | al-jazeera | aljazeera.com | GENERAL_PRESS | 76 | déjà au catalogue (25/0,4h confirmé) |
| TASS (média d'État russe) | tass | tass.com | GENERAL_PRESS | 35 | `rss/v2.xml` 57/0,1h. **Hors préréglage**, à proposer en option signalée "média d'État" : utile pour savoir ce que Moscou dit, jamais comme confirmation. |
Rejetés (erreur) : CNN (hôte injoignable), Reuters (`feeds.reuters.com` n'existe plus, `reutersagency.com/feed` 404), AP (403), AFP (aucun RSS public), Bloomberg (0 item), Politico US (403), Times of Israel (403), Middle East Eye/TRT/Al Arabiya (404/403), RT (domaine introuvable), NHK (dernier item il y a 1514 h), The Times (404), Spiegel international (50 h, acceptable mais 20 items en anglais seulement : non retenu), Le Soir et RTBF (invalides).
**Les trois grandes agences (AFP, Reuters, AP) n'ont aucun flux public exploitable.** Pour un journaliste c'est la première lacune de couverture, et elle est structurelle (pas de donnée sous licence). Mitigation honnête : l'afficher clairement dans la page Sources (voir E12) et proposer Google Actualités (B3.9).

#### B3.4 Sources primaires et officielles (INSTITUTIONS) : "ne jamais manquer le communiqué avant la presse"
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Élysée | elysee | elysee.fr | 96 | `feed` 50/47,4h (institutionnel) |
| Sénat (presse) | senat | senat.fr | 94 | `rss/presse.xml` 5/71h (institutionnel faible fréquence, < 30 j) |
| Conseil d'État | conseil-etat | conseil-etat.fr | 95 | `rss/actualites-rss` 20/88h (faible fréquence). Rejetés : `avis-rss` (1912 h), `seances-publiques-rss` (2416 h). |
| Ministère de la Justice | justice-gouv | justice.gouv.fr | 92 | `rss.xml` 10/29,7h |
| CNIL | cnil | cnil.fr | 94 | `rss.xml` 10/28,1h (régulateur clé pour la comm' : sanctions de données) |
| Autorité de la concurrence | autorite-concurrence | autoritedelaconcurrence.fr | 94 | `rss.xml` 10/387h = 16 j (institutionnel faible fréquence) |
| Défenseur des droits | defenseur-des-droits | defenseurdesdroits.fr | 92 | `rss.xml` 10/457h = 19 j (faible fréquence) |
| Insee | insee | insee.fr | 96 | `fr/flux/1` (tous nationaux) 360/49h ; `fr/flux/3` (conjoncture) 108/105h ; `fr/flux/2` (publications) 47/49h. Chiffres de société : inflation, emploi, démographie. |
| Commission européenne (salle de presse) | commission-europeenne | ec.europa.eu | 95 | `commission/presscorner/api/rss?language=en` 10/25h |
| Parlement européen / Conseil UE | conseil-ue | consilium.europa.eu | 95 | déjà au catalogue ; **les items n'ont aucune date de publication** dans mon relevé (champs `pubDate` et `isoDate` absents), à vérifier : sans date, l'ordre et la fraîcheur sont faux. FR : `fr/rss/pressreleases.ashx` idem. |
| EDPB (protection des données) | edpb | edpb.europa.eu | 92 | `rss.xml` 10/31,7h |
| Direction numérique UE (DSA/DMA) | ue-numerique | digital-strategy.ec.europa.eu | 92 | `en/rss.xml` 10/32,9h |
| ONU Info FR (étendre `un-news`) | un-news | news.un.org | 90 | `fr/news/all/rss.xml` 30/28,1h ; `en/news/region/europe` 30/28,1h |
| OMS (version française) | oms | who.int | 94 | `rss-feeds/news-french.xml` 25/81h. Version anglaise `news-english.xml` : dernier item il y a 5446 h, **rejetée** (flux mort) ; Disease Outbreak News : introuvable (4 URLs en 404). |
| ECDC | ecdc | ecdc.europa.eu | 94 | `en/taxonomy/term/1307/feed` 10/100h ; `term/1310/feed` 10/120h (faible fréquence) |
| EMA (médicaments) | ema | ema.europa.eu | 94 | `en/news.xml` 10/26,7h |
| FDA | fda | fda.gov | 92 | `about-fda/contact-fda/stay-informed/rss-feeds/press-releases/rss.xml` 20/123h |
| PAHO / OPS | paho | paho.org | 88 | `en/rss.xml` 10/20h |
| Santé publique France | sante-publique-france | santepubliquefrance.fr | - | `rss/actualites.xml` 30 items mais **dernier item il y a 1638 h** : rejeté. `communiques-de-presse.xml` : items sans date : rejeté. |
| Gouvernement britannique | gov-uk | gov.uk | 92 | `search/news-and-communications.atom` 20/0,3h ; `government/organisations/prime-ministers-office-10-downing-street.atom` 20/9,8h ; `.../department-of-health-and-social-care.atom` 20/25,5h |
| FTC | ftc | ftc.gov | 92 | `feeds/press-release.xml` 10/52h |
| OMC | wto | wto.org | 92 | `library/rss/latest_news_e.xml` 10/64h |
| NASA | nasa | nasa.gov | 92 | `feed/` 10/1,8h |
| ESA | esa | esa.int | 92 | `rssfeed/TopNews` 9/7,1h |
| NOAA | noaa | noaa.gov | 90 | `rss.xml` 10/120h |
| GIEC | giec | ipcc.ch | 94 | `feed/` 10/31,6h |
| Eurostat | eurostat | ec.europa.eu | 94 | `eurostat/api/dissemination/catalogue/rss/en/statistics-update.rss` 2385/7,1h : **rejeté pour le préréglage** (2385 items, mises à jour de jeux de données, inondation du fil) ; utilisable seulement en source optionnelle avec filtre de mots-clés. |
Rejetés (erreur) : Matignon/gouvernement.fr (403), Assemblée nationale (404 et redirections en boucle sur toutes les URLs), Conseil constitutionnel (404 sur 5 URLs), Cour de cassation (4 URLs invalides), Légifrance/JO (403), Insee `informations-rapides` (500), Cour des comptes (404), Banque de France (403/404), ANSM (0 item), HAS (invalide), Arcom (`rss.xml` : dernier item il y a 5839 h), ARCEP (invalide), CRE (404), ministère des Armées/Intérieur/Éducation/Diplomatie (403 ou 404), Météo-France (404), CDC (`tools.cdc.gov` 1844 items, dernier il y a 440 h), EP et FR du Parlement européen (invalide), IMF/OCDE/Banque mondiale/FAO/ILO/OTAN/UNESCO/UNICEF (403/404), Maison-Blanche (404), DOJ (404), Ofcom (403), FCC (invalide), CPI/CIJ/CEDH (404/403), Service-public (404), HATVP (0 item).
Les **Parlement, tribunaux et préfectures n'ont donc aucun flux exploitable** sur leurs URLs publiques ; seule une extraction HTML (hors périmètre RSS) les couvrirait.

#### B3.5 Climat, science, santé (catégorie SCIENCE_HEALTH)
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Carbon Brief | carbon-brief | carbonbrief.org | 82 | `feed/` 12/34h |
| Climate Home News | climate-home | climatechangenews.com | 76 | `feed/` 40/35h |
| Reporterre | reporterre | reporterre.net | 70 | `rss` 60/11h |
| Nature | nature | nature.com | 90 | `nature.rss` 75/40h (le flux `news.rss` : 404) |
| Science (AAAS) | science-news | science.org | 90 | `rss/news_current.xml` 10/28,6h |
| The Lancet | the-lancet | thelancet.com | 90 | `rssfeed/lancet_current.xml` 27/16h |
| STAT News | stat-news | statnews.com | 80 | `feed/` 20/19h |
| Sciences et Avenir | sciences-et-avenir | sciencesetavenir.fr | 72 | `rss.xml` 50/4,1h |
| Futura-Sciences | futura-sciences | futura-sciences.com | 68 | `rss/actualites.xml` 50/0,1h |
Rejetés : Copernicus (1444 h et 3824 h), UNFCCC (0 item), CIDRAP (flux mort), CNES (2719 h), IRSN (11 024 h), Copernicus climat (1444 h), WMO (404).

#### B3.6 Tech et numérique (catégorie TECH ; les 6 premiers sont déjà en base)
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Numerama | numerama | numerama.com | 76 | `feed/` 15/0,1h (déjà actif en prod) |
| Next (ex-Next INpact) | next | next.ink | 80 | `feed/` 50/4,5h |
| ZDNet France | zdnet-fr | zdnet.fr | 70 | `feeds/rss/actualites/` 50/4,0h |
| 01net | 01net | 01net.com | 66 | `feed/` 30/3,1h |
| Clubic | clubic | clubic.com | 66 | `feed/news.rss` 50/0,3h |
| Frandroid | frandroid | frandroid.com | 64 | `feed/` 15/0,1h |
| Le Monde Informatique | le-monde-informatique | lemondeinformatique.fr | 70 | `flux-rss/thematique/toutes-les-actualites/rss.xml` 20/26h |
| Silicon.fr | silicon-fr | silicon.fr | 70 | `feed` 50/26,6h |
| Korben | korben | korben.info | 60 | `feed` 36/26h |
| The Verge / TechCrunch / Ars Technica / The Register / Wired | the-verge, techcrunch, ars-technica, the-register, wired | - | 76-80 | 10/1,2h ; 20/1,2h ; 20/4,9h ; 50/5,9h ; `wired.com/feed/rss` 50/4,1h |
| Meta (salle de presse) | meta-newsroom | about.fb.com | 90 (primaire) | `news/feed/` 10/47h |
| Google (blog) | google-blog | blog.google | 90 (primaire) | `rss/` 20/41,7h |
Rejetés : OpenAI `news/rss.xml` (1287 items : inondation), X/Twitter blog (403), TikTok (invalide), Snap (404).

#### B3.7 Industrie des médias, PR, communication (catégorie MEDIA_INDUSTRY) : **la veille propre au métier, absente aujourd'hui**
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Poynter | poynter | poynter.org | 80 | `feed/` 10/28,6h |
| Nieman Lab | nieman-lab | niemanlab.org | 80 | `feed/` 30/42,5h |
| Columbia Journalism Review | cjr | cjr.org | 78 | `feed` 10/30,2h |
| Press Gazette | press-gazette | pressgazette.co.uk | 80 | `feed/` 10/24h |
| Journalism.co.uk | journalism-co-uk | journalism.co.uk | 70 | `rss/` 15/26h |
| Committee to Protect Journalists | cpj | cpj.org | 86 | `feed/` 10/23,4h |
| PRWeek (US) | prweek | prweek.com | 78 | `feeds.feedburner.com/PrweekUsNews` 20/21h ; `PrweekInsider` 20/47h. (`PRWeekColumns` : dernier item il y a 2425 h : rejeté ; `prweek.com/uk/rss` invalide.) |
| PRSA PRsay | prsay | prsay.prsa.org | 70 | `feed/` 10/48h |
| PR Daily | pr-daily | prdaily.com | 72 | `feed/` 25/25,5h |
| PR News | pr-news | prnewsonline.com | 68 | `feed/` 10/43h |
| CB News | cb-news | cbnews.fr | 74 | `rss.xml` 10/36h |
| Influencia | influencia | influencia.net | 74 | `feed/` 10/36h |
| Offremedia | offremedia | offremedia.com | 74 | `feed/` 20/42h |
| Maddyness | maddyness | maddyness.com | 70 | `feed/` 10/24h |
| Journal du Net | journal-du-net | journaldunet.com | 68 | `rss/` 60/25,5h |
| OJIM (Observatoire du journalisme) | ojim | ojim.fr | 50 | `feed/` 12/9,3h (militant : à signaler ; utile en veille de communication) |
| Livres Hebdo | livres-hebdo | livreshebdo.fr | 72 | `rss.xml` 100/25h (édition) |
| Rubriques médias des grands titres | - | - | - | déjà couvertes par `lemonde.fr/actualite-medias/rss_full.xml` 20/2,7h, `figaro_medias` 20/22,5h, `theguardian.com/media/rss` 13/3h et `media/mediabusiness/rss` 20/5,6h |
Rejetés : Stratégies (404), RSF (404), IPI (invalide), EJC (404), Institut Reuters (dernier item il y a 796 h), La Revue des médias/INA (404), Arcom (flux mort), Satellifax (404).

#### B3.8 Vérification des faits et désinformation (catégorie FACT_CHECK)
| Nom | slug | domaine | trust | flux (items/âge) |
|---|---|---|---|---|
| Les Décodeurs (Le Monde) | (dans `le-monde`) | lemonde.fr | 88 | `les-decodeurs/rss_full.xml` 20/7,6h |
| Les Surligneurs | les-surligneurs | lessurligneurs.eu | 78 | `feed/` 10/24h |
| EUvsDisinfo | euvsdisinfo | euvsdisinfo.eu | 85 | `feed/` 10/31,6h |
| Snopes | snopes | snopes.com | 75 | `feed/` 20/17,8h |
Rejetés : AFP Factuel (`factuel.afp.com/rss.xml` : dernier item il y a 29 305 h), franceinfo Vrai ou faux (404), Libération CheckNews (500/404), Full Fact (échec), PolitiFact (échec), EDMO (0 item).

#### B3.9 Communiqués d'entreprises et veille concurrents (communication) et culture/sport
| Nom | slug | domaine | catégorie | trust | flux (items/âge) |
|---|---|---|---|---|---|
| ActusNews (communiqués d'émetteurs FR) | actusnews | actusnews.com | FINANCIAL_PRESS | 70 | `fr/rss` 20/23,2h |
| GlobeNewswire (sociétés cotées) | globenewswire | globenewswire.com | FINANCIAL_PRESS | 66 | `RssFeed/orgclass/1/feedTitle/GlobeNewswire%20-%20News%20about%20Public%20Companies` 20/3,5h. **Contredit le commentaire du catalogue** ("GlobeNewswire ... refused automated access") : cette URL précise répond via `fetchFeed`. |
| PR Newswire UK | pr-newswire-uk | prnewswire.co.uk | FINANCIAL_PRESS | 64 | `rss/news-releases-list.rss` 20/47,7h |
| Business Wire, PR Newswire US/FR, Zonebourse | - | - | - | - | rejetés (échec) |
| Télérama | telerama | telerama.fr | CULTURE | 80 | `rss/une.xml` 33/0,1h ; `rss/cinema.xml` 30/2,1h |
| Les Inrocks | les-inrocks | lesinrocks.com | CULTURE | 72 | `feed/` 10/6,1h |
| AlloCiné | allocine | allocine.fr | CULTURE | 68 | `rss/news.xml` 20/0,1h |
| Variety / Deadline / Hollywood Reporter | variety, deadline, hollywood-reporter | - | CULTURE | 78-80 | 10/0,1h ; 12/1,7h ; 10/0,7h |
| Pitchfork | pitchfork | pitchfork.com | CULTURE | 74 | `feed/feed-news/rss` 30/18h |
| Cineuropa | cineuropa | cineuropa.org | CULTURE | 70 | `rss` 50/25,5h |
| ESPN | espn | espn.com | SPORT | 76 | `espn/rss/news` 42/(date future : `-0,8h`, horloge d'édition en avance) ; `rss/soccer/news` 20/idem |
| Footmercato | footmercato | footmercato.net | SPORT | 60 | `flux-rss` 50/0,0h (rumeurs de transferts) |
| Rugbyrama | rugbyrama | rugbyrama.fr | SPORT | 74 | `rss.xml` 100/0,1h |
| Cyclingnews | cyclingnews | cyclingnews.com | SPORT | 74 | `feeds.xml` 50/0,2h |
| Basket Session | basket-session | basketsession.com | SPORT | 62 | `feed` 20/8,2h |
Rejetés : L'Équipe, Eurosport, So Foot (injoignable), Sports.fr (403), UEFA (invalide), FIFA (invalide), CIO (404), Sky Sports (items sans date), Tennis Actu (redirections), Premiere (404), Actualitté (invalide).
Note sur les dates : ESPN renvoie un item daté dans le futur (jusqu'à +0,8 h) ; le pipeline doit borner `publishedAt` à `now` pour ne pas faire remonter ces items en tête et fausser latence et "première dépêche".

#### B3.10 Agrégateurs (usage personnel, hors catalogue public)
- **Google Actualités en RSS** : `https://news.google.com/rss/search?q=<requête>&hl=fr&gl=FR&ceid=FR:fr` renvoie **97 items pour `Renault`** (testé), **70 items par rubrique** pour `headlines/section/topic/WORLD|NATION|ENTERTAINMENT` et 43-49 pour `HEALTH|SPORTS`. C'est le **moyen de veille de marque et de couverture agences** (le titre porte " - Reuters", " - AFP"). L'opérateur `when:1d` avec une requête guillemetée ne renvoie rien (0 item) : documenter. Réserves : les liens sont des redirections `news.google.com/rss/articles/...`, les conditions d'utilisation de Google limitent l'usage automatisé, la déduplication éditeur est à faire à partir de la balise source. **Proposition : pas dans le catalogue ; l'autoriser comme "source RSS personnelle" avec un gabarit "Veille de marque" (voir E2).**
- **Bing Actualités RSS** (`bing.com/news/search?q=...&format=rss&setmkt=fr-FR`) : 12 items, 9,5 h. Moins riche : secours.

---

## C. Actifs et mots-clés par défaut

Rappel des règles de correspondance (`signal.ts:323-362`) : titre et chapô seulement ; terme tout en majuscules jusqu'à 5 caractères, ou terme à majuscule jusqu'à 3 caractères = recherché à l'identique ; sinon mot entier sans accent ni casse ; le libellé ne sert que sans mots-clés. **Il n'existe aucun champ d'exclusion** (`WatchEntity` = label, kind, symbol, keywords, `schema.prisma:537`), d'où la colonne "faux positifs" ci-dessous, qui ne peut aujourd'hui être traitée que par le choix de termes plus longs.

Les `kind` disponibles sont financiers (EQUITY, INDEX, FX, RATE, COMMODITY, CRYPTO) ; pour une rédaction on n'utilise que COMPANY, COUNTRY, INSTITUTION, THEME. Il manque `PERSON` (voir E3) ; les personnes sont provisoirement des THEME.

### C1. Modèle "Rédaction" : 24 entités

| # | Libellé | kind | symbol | Mots-clés FR + EN | Faux positifs / exclusions |
|---|---|---|---|---|---|
| 1 | Exécutif | INSTITUTION | - | Élysée, Matignon, Premier ministre, conseil des ministres, remaniement, gouvernement, Macron, prime minister, French government | "gouvernement" (très générique, ok), "Élysée" (Champs-Élysées : exclusion à créer) |
| 2 | Parlement | INSTITUTION | - | Assemblée nationale, Sénat, députés, sénateurs, projet de loi, proposition de loi, motion de censure, 49.3, National Assembly | "députés européens" relève de l'UE ; "Sénat" (US Senate : préférer "Senate" séparé) |
| 3 | Élections et sondages | THEME | - | élections, législatives, présidentielle, municipales, scrutin, second tour, sondage, Rassemblement national, La France insoumise, Les Républicains, election, poll | **Ne pas utiliser `RN`** (route nationale), `PS` (PlayStation, post-scriptum), `LR`, `EELV`. Utiliser les noms complets. "sondage" ordinaire (sondage commercial) |
| 4 | Justice et procès | THEME | - | procès, mis en examen, garde à vue, parquet, tribunal, cour d'appel, Cour de cassation, Conseil constitutionnel, Conseil d'État, verdict, condamné, relaxé, mandat d'arrêt, trial, indicted, sentenced | "tribunal de commerce" (économique), "procès-verbal" (stationnement) |
| 5 | Faits divers et sécurité | THEME | - | attentat, fusillade, meurtre, terroriste, parquet national antiterroriste, prise d'otages, stabbing, shooting, terror attack | "attentat à la pudeur", "tirs" sportifs |
| 6 | Catastrophes et météo | THEME | - | séisme, inondations, canicule, tempête, vigilance rouge, vigilance orange, feu de forêt, incendie, tsunami, ouragan, wildfire, earthquake, hurricane, flood | "séisme politique", "tempête médiatique" (métaphores très fréquentes), "incendie" d'un bâtiment (volontairement gardé) |
| 7 | Santé publique | THEME | - | épidémie, pandémie, OMS, Santé publique France, ANSM, vaccin, grippe, rougeole, Covid, outbreak, WHO | `WHO` en majuscules seulement ; "virus" omis (informatique) ; "vaccin" (marketing) |
| 8 | Climat et environnement | THEME | - | réchauffement climatique, GIEC, COP30, COP31, émissions de CO2, climate change, biodiversité, sécheresse, IPCC, pollution | **Ne pas utiliser `COP`** seul (police) ; "climat social" |
| 9 | Ukraine et Russie | COUNTRY | - | Ukraine, Russie, Poutine, Zelensky, Kremlin, Kiev, Moscou, Putin, Russia | "Moscou" (Moscow Idaho, négligeable) |
| 10 | Proche-Orient | THEME | - | Gaza, Israël, Hamas, Hezbollah, Liban, Cisjordanie, Iran, Houthis, Israel, Lebanon | "Liban" dans un nom de marque |
| 11 | États-Unis | COUNTRY | - | Maison-Blanche, Trump, Congrès américain, Pentagone, Cour suprême américaine, White House, Supreme Court | "Washington" seul (acteur, ville) : omis ; "Cour suprême" seul (autres pays) |
| 12 | Union européenne | INSTITUTION | - | Commission européenne, Parlement européen, Conseil européen, von der Leyen, European Commission, Brussels | "Bruxelles" seul : tout fait divers belge |
| 13 | Chine et Asie | COUNTRY | - | Chine, Pékin, Xi Jinping, Taïwan, China, Beijing, Taiwan | "chine" (porcelaine, brocante) : minuscule non distingué, accepter |
| 14 | Afrique | THEME | - | Sahel, Algérie, Maroc, Sénégal, Côte d'Ivoire, Afrique de l'Ouest, Mali, Niger | **"Niger" et "Mali" sont aussi des prénoms et noms propres** ; "Maroc" (marque) |
| 15 | Économie du quotidien | THEME | - | inflation, pouvoir d'achat, chômage, retraites, réforme des retraites, budget, déficit, impôts, SMIC, prix de l'énergie | "budget" (budget d'un film/club : très bruyant) |
| 16 | Social et mobilisations | THEME | - | grève, manifestation, syndicats, CGT, intersyndicale, mobilisation, blocage, SNCF, strike | "grève de la faim", "manifestation sportive", "blocage" informatique |
| 17 | Société et éducation | THEME | - | Éducation nationale, baccalauréat, immigration, logement, laïcité, enseignants | "bac" seul (bac à sable) ; "logement" ordinaire |
| 18 | Numérique et IA | THEME | - | intelligence artificielle, IA, AI, ChatGPT, OpenAI, cyberattaque, TikTok, Instagram, Facebook, Digital Services Act | **Pas `Meta`** (méta-analyse, "meta description") : écrire "Meta Platforms" ; `AI` en capitales seulement |
| 19 | Culture et cinéma | THEME | - | Festival de Cannes, Palme d'or, César, Oscars, Goncourt, Molière, cinéma, musée, Louvre | "César" (prénom, Jules César), "Louvre" (Louvre-Lens, hôtel) |
| 20 | Sport | THEME | - | Ligue 1, Ligue des champions, Coupe du monde, Jeux olympiques, Tour de France, Roland-Garros, Top 14, XV de France, équipe de France, PSG, Champions League | `OM` (mot de 2 lettres, ok en capitales) ; "coupe du monde" (non sportive) ; "match" |
| 21 | Décès et hommages | THEME | - | mort de, décès de, meurt à, disparition de, obsèques, hommage national, dies at, death of, obituary | Exclusions nécessaires : "mort de rire", "mort-né", "mort cérébrale", "morts" (bilans de guerre) |
| 22 | Presse et audiovisuel | INSTITUTION | - | Arcom, France Télévisions, Radio France, audiovisuel public, liberté de la presse, Reporters sans frontières, pluralisme, journalistes | `AFP` apparaît dans presque toutes les signatures ("avec AFP") : **ne pas l'inclure**, ou seulement en tant qu'organisation avec "agence AFP" |
| 23 | Désinformation et démentis | THEME | - | infox, désinformation, fake news, deepfake, rectificatif, dément formellement, hoax, misinformation, debunked | "dément" seul est un faux positif massif (voir D) |
| 24 | Exclusivités et enquêtes | THEME | - | selon nos informations, révèle, révélations, enquête de, a appris, EXCLUSIF, exclusive, scoop, Panama Papers | "révèle" ordinaire (sondage qui "révèle") |

### C2. Modèle "Communication / PR" : à instancier par l'utilisateur (le produit ne peut pas deviner la marque)
Le modèle devrait **demander à l'onboarding** : nom de la marque (et variantes), dirigeants, 3 concurrents, secteur, produits. Entités générées :
| Libellé | kind | Mots-clés | Notes |
|---|---|---|---|
| Ma marque | COMPANY | `<marque>`, variantes, produits phares, sigle | **Homonymes** : la marque "Orange", "Total", "Free", "Carrefour"... exige un contexte (voir E1) ; sans exclusion, "Orange" matche le fruit, la couleur et la météo |
| Mes dirigeants | THEME (-> PERSON) | prénom + nom complet, "PDG de <marque>", fonction | prénom+nom entre guillemets, pas de nom de famille seul |
| Concurrent 1..3 | COMPANY | idem | alerte "concurrent mentionné avec une enquête ou un incident" |
| Secteur | THEME | termes du secteur (5-10) | |
| Crise et bad buzz | THEME | polémique, scandale, boycott, appel au boycott, bad buzz, backlash, excuses, indignation, accusations, plainte, enquête ouverte, rappel de produits, fuite de données, cyberattaque, harcèlement, lanceur d'alerte, whistleblower, outrage | **Doit être conjoint à la marque** (ET) ; seul, il matche toute polémique politique ou sportive |
| Régulateurs | INSTITUTION | CNIL, DGCCRF, Autorité de la concurrence, Arcom, ARPP, Défenseur des droits, Cour des comptes, HATVP, FTC, Ofcom, Information Commissioner | "ICO" omis (crypto) ; "CNIL" (ok) |
| RSE et greenwashing | THEME | greenwashing, écoblanchiment, devoir de vigilance, CSRD, loi Sapin II, Pacte Vert | |
| Marque employeur | THEME | plan social, PSE, grève, harcèlement moral, burn-out, Glassdoor, démission en masse | |
| Plateformes et réseaux | THEME | TikTok, Instagram, YouTube, LinkedIn, Reddit, Threads, algorithme | "X" omis |

### C3. Problèmes de précision dans le gabarit actuel (mesurés : sorties réelles de `compileTerms`, `classifyEvents`, `computeTone`)
| Entité actuelle | Problème observé |
|---|---|
| **AMF** (`AMF`, "Autorité des marchés financiers") | `AMF` = aussi l'**Association des maires de France**, très présente dans l'actualité locale ([INFERENCE] : non testé sur un titre réel). Le terme est en capitales et sera apparié à l'identique ; "Communiqués de l'AMF" est un terme plus sûr. L'alerte "Communiqués de l'AMF" exige `REGULATION_LEGAL`, ce qui limite l'effet sur l'alerte, mais l'entité sera polluée. |
| **Démentis** (`dément`, `démenti`, `denies`, `denied`) | Matche **tout démenti** : "Un manifestant dément avoir lancé un pavé" -> match (testé). "denied bail/entry/access" en anglais : faux positifs. L'alerte est nommée "Démentis **officiels**" alors qu'elle n'a aucun filtre sur l'émetteur. |
| **Rumeurs de marché** | Le libellé dit "de marché" mais la liste est générique : "Selon nos informations, le gouvernement prépare un remaniement" -> match. Nom trompeur pour le journaliste généraliste (et utile par ailleurs : à renommer "Rumeurs et informations non confirmées"). |
| **Crises de réputation** (`polémique`, `scandale`, `boycott`, `backlash`) | Non rattaché à une marque : toute polémique sportive ou politique matche. Pour le comm', il faut le ET avec la marque ; pour le journaliste c'est un suivi de sujet. |
| **CAC 40 - sociétés** (INDEX, `grandes entreprises françaises`) | Inadapté à un généraliste ; "grandes entreprises françaises" est quasi-inexistant dans les titres. |
| **Banques centrales** (`Fed`, `Lagarde`, `banque centrale`) | Spécifique au journaliste financier ; à retirer du modèle généraliste. |
| **Fusions-acquisitions** (`rachat`, `OPE`) | "rachat" matche aussi "rachat de points de retraite" ; `OPE` ok en capitales. |
| Domaine | `audience: 'Journalistes financiers, communication de crise, affaires publiques'` et `criticalInfo: 'Rumeurs, mentions, fiabilité de la source'`, `decision: 'Publier ou répondre vite'` : cadrage financier, 8 entités dont 4 sont financières. |

---

## D. Modèles d'alerte et agencement de widgets

### D1. Alertes actuelles : problèmes (mesurés avec `computeImpact`)
- **"Crise de réputation - impact fort"** (`minImpact: 'HIGH'` = score >= 60) : un item sans type d'événement démarre à **10 points** (`computeImpact`, `signal.ts:225`). Le titre "Boycott historique : Danone visé par un appel massif, la polémique enfle" ne fait que **35 (MEDIUM)** avec une source et **50 (MEDIUM)** avec 4 sources. **Cette alerte ne se déclenchera pratiquement jamais** car aucun type d'événement ne couvre la réputation.
- **"Rumeurs de marché"** (`minImpact: 'MEDIUM'` = 35) : une rumeur politique sans type d'événement vaut 10 à 30 : **ne se déclenche pas** sauf corroboration.
- **"Démentis officiels"** (`minTrust: 60`, aucun filtre) : se déclenche sur tout démenti de n'importe quelle partie.
- **"Communiqués de l'AMF"** : correct pour un journaliste financier, hors sujet pour les autres.

### D2. Alertes proposées (modèle Rédaction)
Format : nom | entité ou mots-clés | types d'événements | plancher d'impact | plancher de confiance. Les types marqués (*) n'existent pas encore (voir F).
1. **Dernière minute** | tout (`scope: all`) | - | HIGH | 70 | + déclenché si au moins 3 éditeurs concordants en 15 min (cf. E5).
2. **Information non confirmée sur mes sujets** | entités 1, 2, 3, 4 | rumeur (RUMOR) | LOW | aucune | (la rumeur est le sujet).
3. **Décès d'une personnalité** | entité 21 | DEATH(*) | MEDIUM | 60
4. **Justice : décisions majeures** | entité 4 | JUSTICE_CRIME(*) | MEDIUM | 70
5. **Attentat, catastrophe, bilan humain** | entités 5 et 6 | SECURITY(*), DISASTER(*) | MEDIUM | 60
6. **Alerte sanitaire** | entité 7 | HEALTH(*) | MEDIUM | 80 (source primaire exigée)
7. **Communiqué d'une source primaire** | `publisher in (elysee, conseil-etat, justice-gouv, cnil...)` | - | LOW | 90 : nécessite un filtre par catégorie de source (E7).
8. **Premier sur un sujet que je suis** (je publie avant les autres) | entité au choix | - | - | - : utilise la "première dépêche" de `/sujets` pour signaler un sujet qui vient de décoller dans un seul média (scoop concurrent à confirmer).
9. **Désinformation sur mes sujets** | entité 23 | DISINFO(*) | LOW | 70.
10. **Résultats d'élection ou de scrutin** | entité 3 | ELECTIONS(*) | MEDIUM | 70.

### D3. Alertes proposées (modèle Communication)
1. **Ma marque : ton négatif** | entité "Ma marque" | REPUTATION_CRISIS(*) ou ton NEGATIVE | MEDIUM | aucune (voir E4 : le ton n'est pas un champ de règle).
2. **Ma marque : source primaire ou presse majeure** | "Ma marque" | - | LOW | 80
3. **Mon dirigeant cité avec enquête, plainte, polémique** | "Mes dirigeants" | JUSTICE_CRIME(*), REPUTATION_CRISIS(*) | LOW | 60
4. **Concurrent en difficulté** | Concurrent 1..3 | JUSTICE_CRIME(*), DEFAULT_RESTRUCTURING, INCIDENT, REPUTATION_CRISIS(*) | MEDIUM | 60
5. **Concurrent : annonce** | Concurrent 1..3 | M_AND_A, EARNINGS, MEDIA_INDUSTRY(*) | LOW | 70
6. **Régulateur : décision ou sanction** | entité Régulateurs | REGULATION_LEGAL | LOW | 90 (le trust 90 correspond à une source officielle)
7. **Rappel produit ou fuite de données dans mon secteur** | Secteur | INCIDENT | MEDIUM | 60
8. **Pic de volume** : alerte quand les mentions dépassent x fois la moyenne (la vélocité existe sur la watchlist, voir `centre-operations.md` ; elle n'est pas exposée comme condition d'alerte) : proposition E4.

### D4. Widgets (types existants : LIVE_WIRE, WATCHLIST_PULSE, ALERTS, RUMOR_RADAR, BRIEFING, EVENT_MIX, DECISION_JOURNAL, SOURCE_HEALTH ; filtres `scope`, `entityIds`, `eventTypes`, `minTrust`, `minImpact`, `hours`)
**Rédaction** (grille 12 colonnes) :
- `LIVE_WIRE` 8x2, `scope: all`, `minImpact: LOW`, `hours: 2` : le fil global.
- `ALERTS` 4x2.
- `RUMOR_RADAR` 6x2, `scope: all` (les rumeurs sont des "à confirmer").
- `WATCHLIST_PULSE` 6x2 (pouls des 24 entités : qui accélère).
- `BRIEFING` 4x2, `hours: 12` (la veille).
- `LIVE_WIRE` 6x2 avec `eventTypes: [JUSTICE_CRIME, SECURITY]` (un deuxième fil dédié, déjà possible avec plusieurs instances du même widget si le front l'autorise : à vérifier).
- `EVENT_MIX` 4x1 : **inadapté tant que les types sont financiers** (voir F).
- `SOURCE_HEALTH` 4x1 (utile pour savoir quelles sources sont en panne).
- Remplacer `DECISION_JOURNAL` (6x2 dans le gabarit actuel) par un "journal de publication" : les actions `PUBLISH`, `ESCALATE`, `CLIENT_ALERT` existent déjà dans `DECISION_ACTIONS` ; il manque les libellés rédactionnels (voir E9).
**Communication** :
- `ALERTS` 4x2 en tête, `LIVE_WIRE` 8x2 filtré sur les entités "Ma marque + Mes dirigeants", `WATCHLIST_PULSE` 6x2 (marque vs concurrents), `RUMOR_RADAR` 6x2, `BRIEFING` 4x2, `SOURCE_HEALTH`.
Nouveaux widgets à créer (voir E) : "À la une" (rang dans les flux Une), "Propagation d'un sujet" (timeline), "Part de voix" (marque vs concurrents), "Baromètre de ton".

---

## E. Lacunes fonctionnelles

Priorités : P0 = bloquant pour ce métier ; P1 = différenciant ; P2 = confort. Faisabilité = RSS seul, sans IA, sans donnée sous licence.

| # | Besoin | État actuel | Proposition | Prio | Faisabilité |
|---|---|---|---|---|---|
| E1 | **Mots-clés d'exclusion et contexte** : "Orange" fruit/opérateur, "Total", "Free", "AMF"... | Aucun champ d'exclusion ; `WatchEntity` = label, kind, symbol, keywords (`schema.prisma:537`) ; la sourdine est globale et par éditeur ou mot-clé (Réglages > Sourdine), pas par actif ni par règle | Champs `excludeKeywords String[]` et `requireKeywords String[]` (ET) sur `WatchEntity` et `AlertRule` ; syntaxe `"phrase exacte"`, `-mot`, `(a OU b)` ; aperçu en direct du nombre de correspondances sur les 24 dernières heures avant l'enregistrement | **P0** | Oui : extension de `compileTerms` (`signal.ts:329`) |
| E2 | **Veille de marque / concurrents** par requête | Une "source RSS personnelle" à la main (20 max) ; aucun gabarit | Gabarit "Veille de marque" qui fabrique l'URL Google Actualités ou Bing Actualités (voir B3.10) ; chaque requête devient un éditeur privé ; les items passent par le garde SSRF existant. Limite : un seul éditeur privé par requête, recoupement trivial | **P0** (comm') | Oui (Google/Bing RSS testés) ; réserve CGU Google à valider |
| E3 | Entités "personne" et homonymes | kinds financiers ; pas de PERSON | Ajouter `PERSON` et `ORGANISATION` à `ENTITY_KINDS` (types en double : `backend/src/types/opsTypes.ts` et le miroir front) ; icônes et regroupement dans `/actifs` | P1 | Oui |
| E4 | **Ton et détection de crise** comme condition de règle, volume et vélocité | Le ton (`computeTone`) est calculé mais **ne peut pas être filtré** par `AlertRule` (champs : entité, mots-clés, types, minTrust, minImpact) ; vélocité affichée mais pas déclencheur | `tone: NEGATIVE` et `minVelocity` (ex. x3 vs moyenne 24 h) dans `AlertRule` ; alerte "pic de mentions négatives de ma marque" | **P0** (comm') | Oui (déjà calculé) |
| E5 | **Importance éditoriale** (impact) au-delà du lexique | Un item sans type d'événement vaut 10 points (voir D1, F) ; le recoupement plafonne à +15 | Ajouter des signaux RSS-natifs : (a) **apparition dans un flux "Une"** (Le Monde `rss/une.xml` 16 items, Libération `accueil-une`, franceinfo `titres.rss`, TF1 Info `une`) = +20 et badge "À la une" ; (b) **vitesse de reprise** (3 éditeurs en moins de 15 min) = +20 ; (c) source primaire officielle = +15 ; (d) rang dans le flux (les 3 premiers items) | **P0** | Oui |
| E6 | **Suivi de propagation d'une histoire** | `/sujets` classe les sujets par dynamique, première dépêche, nombre d'éditeurs et délai (`centre-operations.md`) | Fiche de sujet : frise (qui, quand, dans quel ordre), courbe du nombre d'éditeurs, "reprise par des sources primaires", épingler un sujet pour recevoir une alerte quand il atteint N éditeurs ; **clustering FR/EN** (un même événement en anglais et en français n'est pas rapproché : les tokens significatifs diffèrent) | P1 | Oui sauf le FR/EN (partiel : noms propres, nombres) |
| E7 | **Source primaire vs presse** : "le ministère l'a publié avant la presse ?" | Catégories INSTITUTIONS vs GENERAL_PRESS existent au catalogue mais ne sont ni filtre de règle, ni badge dans le fil | Filtre `sourceCategory` sur les règles et les widgets ; badge "source primaire" ; mesure "délai primaire -> première reprise" | P1 | Oui |
| E8 | **Filtres par rubrique** (politique, justice, société, santé, climat, sport, culture, tech, international) | Aucun filtrage par rubrique ; le type d'événement est financier ; les rubriques de flux (section) ne sont pas conservées | Ajouter `section` au Channel (déduite de l'URL du flux, ex. `politique.rss` -> POLITIQUE) et une facette "Rubrique" dans `/flux` et dans les filtres de widgets. La rubrique du flux est un signal plus fiable que tout lexique | **P0** | Oui (c'est la structure même des flux) |
| E9 | **Journal de publication / décision** | `DECISION_ACTIONS` : WATCH, HEDGE, REDUCE_EXPOSURE, INCREASE_EXPOSURE, CLIENT_ALERT, PUBLISH, ESCALATE, NO_ACTION, OTHER ; HEDGE, REDUCE_EXPOSURE, INCREASE_EXPOSURE n'ont aucun sens en rédaction | Profil d'actions par métier : Rédaction = Publier, Attendre confirmation, Suivre, Escalader au chef d'édition, Ne pas traiter ; Comm' = Répondre, Préparer un communiqué, Alerter le dirigeant, Activer la cellule de crise, Surveiller. Libellés par métier (l'énumération stockée peut rester) | P1 | Oui (libellés) |
| E10 | **Partage et export** | Journal exportable en CSV ; "Copier le brief" en texte brut et impression | Brief au format email et Markdown ; **lien de partage** d'un brief ou d'une fiche (lecture seule, expirant) ; export CSV/JSON d'un sujet ; webhook ou email sur alerte ; **canaux Slack/Teams** pour la cellule de crise | P1 (webhook/email P0 pour comm') | Oui |
| E11 | **Notifications** | Cloche, son, notifications de bureau, compteur d'onglet ; Réglages > Notifications | Email d'alerte et résumé horaire ; web-push ; webhooks ; plages de silence (nuit) ; niveau d'urgence par règle ; **astreinte** | P1 | Oui |
| E12 | **Transparence des limites de couverture** : pas d'AFP, Reuters, AP ; pas de Parlement ni tribunaux | Pas de page de couverture ; le journaliste ne sait pas ce qu'il ne voit pas | Page "Couverture" listant les familles de sources couvertes, manquantes et pourquoi ; badge "non vu : agences" | P1 | Oui |
| E13 | **Préréglages par sous-profil** : Rédaction générale, Rédaction économique, Communication, Affaires publiques | Un seul préréglage MEDIA_COMMS | Onboarding à deux questions (rédaction ou comm ? généraliste ou éco ?) qui choisit préréglage de sources, entités et règles. Les "tableaux de bord" pourraient aussi être dupliqués depuis un modèle | P1 | Oui |
| E14 | **Surveiller la concurrence éditoriale** : "mon concurrent a-t-il l'info avant moi ?" | `/sujets` donne la première dépêche par éditeur | Marquer "mon média" (un éditeur du catalogue) et afficher "vous êtes 1er / Nème sur ce sujet" ; alerte "un autre média a sorti ce sujet, pas vous" | P1 | Oui |
| E15 | **Calendrier / agenda** : conseil des ministres, audiences, matchs, sorties | Non livré (voir `centre-operations.md`) ; l'absence est assumée pour la finance | Agenda manuel par l'utilisateur (événements datés, rappel) ; pas de source externe fiable | P2 | Oui (saisie manuelle) |
| E16 | **Ton nuancé** : "hausse" est classé POSITIF pour la santé, la délinquance, le chômage | `POSITIVE_WORDS`/`NEGATIVE_WORDS` financiers (`signal.ts:436-441`) | Voir F5 | P1 | Oui |
| E17 | **Notes, annotations, tags** sur un item ou un sujet, partagés dans l'équipe | Aucun ; le journal est en ajout seul | Notes privées sur item ; étiquettes ; commentaire d'équipe (multi-utilisateur : hors périmètre actuel `userId`) | P2 | Oui pour le privé |
| E18 | **Multilingue** : filtres de langue, titres EN à côté de FR | Aucune langue portée par l'item | `language` du Channel (connue) ; filtre FR/EN ; clustering inter-langues limité | P2 | Oui |
| E19 | **Heures de relevé** : Ouest-France ne publie que 10 items par flux, Le Monde 20 | Relevé toutes les 15 s si l'éditeur est lu | Rien à faire sauf surveiller la perte d'items entre deux relevés sur les flux à 10 items (alerte de santé source) | P2 | Oui |
| E20 | **Personnalisation de la disposition** : plusieurs widgets du même type, filtres par widget, tableaux de bord multiples | Mode édition (ajout, ordre, largeur, hauteur, filtres) livré | Autoriser plusieurs `LIVE_WIRE` avec filtres distincts (vérifier) ; nommer les widgets | P1 | Oui |

---

## F. Types d'événements : adéquation à une rédaction, et types à ajouter

### F1. Constat : `EVENT_TYPES` (`opsTypes.ts:28-40`) = 11 types, **9 sont financiers** (CENTRAL_BANK, MACRO, EARNINGS, M_AND_A, RATINGS, GEOPOLITICS_SANCTIONS, ENERGY_COMMODITIES, DEFAULT_RESTRUCTURING, MARKETS) ; REGULATION_LEGAL et INCIDENT sont mixtes. **Aucun type ne couvre** : politique, élections, justice pénale, sécurité et terrorisme, santé publique, climat, catastrophes, décès, culture, sport, société, médias, désinformation, réputation.

Sorties réelles du classifieur sur des titres de rédaction (test jetable) :
| Titre | Types détectés | Attendu |
|---|---|---|
| Le président Macron annonce la dissolution de l'Assemblée nationale | (aucun) | POLITIQUE |
| Le gouvernement Barnier censuré par l'Assemblée nationale | (aucun) | POLITIQUE |
| Elections législatives : le RN arrive en tête au premier tour | (aucun) | ÉLECTIONS |
| La Cour pénale internationale émet un mandat d'arrêt | (aucun) | JUSTICE |
| Le Conseil constitutionnel censure une partie de la loi immigration | (aucun) | JUSTICE/POLITIQUE |
| Un adolescent de 15 ans mis en examen pour meurtre | (aucun) | JUSTICE/FAIT DIVERS |
| Attentat à Strasbourg : le parquet antiterroriste saisi | (aucun) | SÉCURITÉ |
| Mort du pape François à 88 ans | (aucun) ; impact **10/LOW**, 25/LOW même avec 4 sources | DÉCÈS, impact fort |
| Épidémie de rougeole : l'OMS alerte sur la hausse des cas | (aucun) ; **ton POSITIF** ("hausse") | SANTÉ ; ton négatif |
| Canicule : 40 départements en vigilance orange | (aucun) | MÉTÉO / CATASTROPHE |
| Les pompiers luttent contre un feu de forêt en Gironde | (aucun) | CATASTROPHE |
| Wildfire forces evacuation of 10,000 people in California | (aucun), impact 10/LOW | CATASTROPHE |
| La Russie lance une offensive majeure en Ukraine | (aucun) ; impact 10/LOW | CONFLIT, impact fort |
| Man arrested over stabbing in London, police say | (aucun) | FAIT DIVERS |
| Le PSG bat l'OM 3-1 en Ligue 1 ; Tour de France : Pogacar remporte la 12e étape | (aucun) | SPORT |
| Cannes : la Palme d'or décernée au film de Justine Triet | (aucun) | CULTURE |
| COP30 : accord a minima sur le climat à Belém | (aucun) | CLIMAT |
| L'Arcom met en demeure la chaîne CNews | (aucun) | RÉGULATION/MÉDIAS |
| Le Figaro cède 10 % de son capital | (aucun, "cède" absent de M_AND_A) | M_AND_A |
| Trump wins the election, rally across markets | (aucun) ; ton POSITIF | ÉLECTIONS |

**Faux positifs mesurés** :
- `stake` dans M_AND_A : "Democracy is at stake in the election, warns senator" -> **M_AND_A** (impact 30).
- `fusion\w*` dans M_AND_A : "Fusion nucléaire : record de durée pour un réacteur chinois" -> **M_AND_A + ENERGY**, impact **58 MEDIUM / 73 HIGH avec 4 sources** (alors que c'est de la physique). Aussi `record` compte comme "vocabulaire de choc".
- `strikes?` dans INCIDENT : "Israel strikes Gaza overnight, dozens killed" -> **INCIDENT (25 points)** au lieu de géopolitique ; `strike` = grève ou frappe sans distinction.
- `record\w*` (INTENSITY) : un record sportif ("Duplantis") obtient +20 d'impact comme un krach : 30 points, 45 avec 4 sources.
- Le mot **séisme** donne INCIDENT (poids 25) : bilan de 2000 morts en Turquie = **25 LOW** (40 MEDIUM avec 4 sources). Un séisme meurtrier n'est jamais "fort".
- **Ton** : `hausse` est positif (`POSITIVE_WORDS`), donc "hausse de 40 % des cas de rougeole" = POSITIVE ; "wins" positif pour toute victoire électorale ou sportive.

### F2. Impact : cause structurelle (à corriger en même temps que les types)
`computeImpact` part de 10 sans type (`signal.ts:225`) et ne monte que par lexique de choc (+20 à +30, mots financiers : chute, krach, plonge...), `BREAKING` en majuscules seulement, % >= 5 (+10), recoupement (jusqu'à +15). Un fait majeur non financier (décès d'un chef d'État, dissolution, offensive) plafonne à 10-40 : **aucune de ces infos n'atteint HIGH (>= 60)** sauf avec des mots financiers. Les alertes "impact fort" ne se déclencheront donc que pour de l'économie.

### F3. Types à ajouter (lexiques FR/EN ; les termes restent des mots entiers sans accent sur le texte normalisé ; les acronymes en capitales séparés comme `acronyms`)
| Type proposé | Poids (impact de base) | Exemples de titres | Lexique FR | Lexique EN | Acronymes (sensibles à la casse) |
|---|---|---|---|---|---|
| `POLITICS_GOVERNMENT` | 30 | "Le Premier ministre démissionne", "Remaniement : trois nouveaux ministres" | gouvernement, remaniement, démission, démissionne, dissolution, motion de censure, 49.3, ministre, Premier ministre, Élysée, Matignon, conseil des ministres, nomination, projet de loi, vote, adopté, rejeté, coalition, majorité, cohabitation | prime minister, cabinet reshuffle, resigns, resignation, no confidence, snap election, coalition, legislation, bill | - |
| `ELECTIONS` | 35 | "Législatives : le RN en tête", "Trump wins the election" | élections, législatives, présidentielle, municipales, européennes, scrutin, premier tour, second tour, résultats, sondage, abstention, candidat, campagne, urnes, référendum | election, elections, ballot, vote count, exit poll, polls, runoff, referendum, primary | - |
| `JUSTICE_CRIME` | 30 | "Procès Sarkozy", "Mis en examen pour meurtre" | procès, mis en examen, mise en examen, garde à vue, perquisition, parquet, verdict, condamné, relaxé, acquitté, mandat d'arrêt, tribunal correctionnel, cour d'assises, cour d'appel, Cour de cassation, Conseil constitutionnel, Conseil d'État, instruction, détention provisoire, plainte, procureur, meurtre, homicide, agression, interpellé | trial, indicted, charged with, sentenced, convicted, acquitted, arrest warrant, arrested, prosecutor, supreme court, murder, stabbing | PNF, PNAT, CPI, CEDH, CJUE |
| `SECURITY_TERRORISM` | 40 | "Attentat à Strasbourg", "Fusillade dans un lycée" | attentat, terroriste, terrorisme, fusillade, prise d'otages, explosion, attaque, vigilance attentat, Vigipirate, état d'urgence, tirs, poignardé | terror attack, shooting, gunman, hostage, bombing, mass shooting, stabbing | PNAT, DGSI |
| `CONFLICT_WAR` | 40 | "Offensive majeure en Ukraine", "Frappes sur Gaza" | offensive, bombardement, frappes, drones, missiles, front, cessez-le-feu, trêve, otages, civils, troupes, armée, mobilisation, escalade, invasion | offensive, shelling, airstrikes, drone attack, ceasefire, hostages, troops, escalation, front line, strikes on | OTAN (déjà), ONU |
| `DISASTER_WEATHER` | 40 | "Séisme de 7,8 en Turquie", "Canicule : vigilance rouge" | séisme, tremblement de terre, tsunami, inondations, crue, canicule, vague de chaleur, tempête, ouragan, cyclone, éruption, glissement de terrain, feu de forêt, incendie de forêt, vigilance rouge, vigilance orange, sécheresse, avalanche, bilan, naufrage, déraillement, accident, effondrement | earthquake, tsunami, flood, heatwave, storm, hurricane, typhoon, wildfire, volcano eruption, landslide, drought, death toll, derailment, plane crash | - |
| `HEALTH_PUBLIC` | 30 | "Épidémie de rougeole", "L'OMS déclare une urgence de santé publique" | épidémie, pandémie, alerte sanitaire, OMS, vaccin, vaccination, grippe, rougeole, Covid, variole, virus, contamination, rappel, pénurie de médicaments, ARS, hôpital, urgences, essai clinique, autorisation de mise sur le marché | outbreak, epidemic, pandemic, vaccine, public health emergency, bird flu, clinical trial, drug approval, recall | OMS, WHO, ANSM, EMA, FDA, ECDC, HAS, ARS |
| `CLIMATE_ENVIRONMENT` | 25 | "COP30 : accord a minima", "Marée noire en Bretagne" | réchauffement climatique, changement climatique, GIEC, COP, émissions, CO2, neutralité carbone, biodiversité, pollution, marée noire, énergies renouvelables, déforestation, glaciers, fonte, record de chaleur | climate change, global warming, emissions, carbon, biodiversity, pollution, oil spill, deforestation, heat record | GIEC, IPCC, COP30, COP31 |
| `DEATH_OBITUARY` | 40 | "Mort de Alain Delon", "Décès du pape François" | mort de, décès de, meurt, est mort, décédé, disparition de, obsèques, hommage national, funérailles, s'est éteint, nous a quittés | dies, died, death of, passed away, obituary, funeral, state funeral | - |
| `CULTURE_ENTERTAINMENT` | 15 | "Palme d'or", "Goncourt" | festival, Palme d'or, César, Oscars, Goncourt, Molière, box-office, sortie, tournée, concert, exposition, musée, cinéma, série, album, prix littéraire, rentrée littéraire | award, oscars, box office, premiere, tour, album, exhibition, festival | - |
| `SPORT` | 15 | "Le PSG bat l'OM", "Pogacar remporte l'étape" | victoire, défaite, finale, demi-finale, but, match, championnat, Coupe du monde, Jeux olympiques, transfert, sélection, record du monde, Tour de France, Roland-Garros, Ligue 1, Top 14, forfait, blessure | wins, defeat, final, semi-final, world cup, olympics, transfer, injury, record, championship | PSG, OM, OL, JO, NBA, NFL, UEFA, FIFA |
| `MEDIA_INDUSTRY` | 25 | "L'Arcom met en demeure CNews", "Rachat d'un groupe de presse" | audiences, Médiamétrie, Arcom, mise en demeure, licenciements en rédaction, grève de la rédaction, rachat d'un titre, droit de réponse, diffamation, liberté de la presse, pluralisme, audiovisuel public, annonceurs, publicité, éditeur de presse, paywall | newsroom layoffs, press freedom, defamation, ratings, media merger, broadcaster, publisher, paywall | AFP, CSA, CPPAP |
| `REPUTATION_CRISIS` | 35 | "Boycott historique : Danone visé", "Polémique autour de Nike" | polémique, scandale, boycott, appel au boycott, bad buzz, indignation, excuses publiques, accusations, affaire, enquête interne, harcèlement, lanceur d'alerte, retire sa publicité, mise à pied, démission forcée, #MeToo, pétition | backlash, boycott, scandal, outrage, apology, allegations, whistleblower, controversy, resigns after | - |
| `DISINFORMATION` | 25 | "Les Décodeurs : cette vidéo est fausse", "Deepfake d'un ministre" | infox, désinformation, fausse information, canular, hoax, fake news, deepfake, manipulation, démenti, dément formellement, rectificatif, vérification, faux | disinformation, misinformation, hoax, deepfake, debunked, fact check, false claim, denies reports | - |
| `SOCIAL_MOVEMENT` | 25 | "Grève SNCF", "Manifestation contre la réforme" | grève, manifestation, mobilisation, blocage, intersyndicale, syndicats, préavis de grève, piquet, cortège, gilets jaunes | strike action, walkout, protest, demonstration, union, picket | CGT, CFDT, FO, SUD |
Notes de conception :
- **Scinder INCIDENT** : aujourd'hui "grève" + "cyberattaque" + "panne" + "incendie" + "séisme" + "rappel de produits" partagent un seul type (poids 25). Garder INCIDENT pour cyber/panne/rappel produit ; sortir la grève (SOCIAL_MOVEMENT), les catastrophes (DISASTER_WEATHER) et la sécurité.
- **Scinder REGULATION_LEGAL** : ce type mélange amende, antitrust, MiFID (finance/régulation) et "tribunal", "condamn*", "proces" (justice pénale). Garder REGULATION_LEGAL pour le régulatoire ; JUSTICE_CRIME pour le pénal.
- **GEOPOLITICS_SANCTIONS** : correct pour guerre/sanctions mais `strikes?` en anglais est en INCIDENT ; ajouter les verbes de frappe à CONFLICT_WAR et retirer `strikes?` d'INCIDENT.
- **Poids et seuil HIGH** : pour les types de sécurité, décès, conflit et catastrophe, mettre un poids 40 puis "mots de bilan" (morts, tués, blessés, victimes, bilan, evacuated) en intensité : +20. Les nombres de victimes ("2000 morts") sont déjà extraits en chiffres clés ; ajouter un déclencheur "nombre de morts >= 10".
- **Faux positifs à corriger** : retirer `stake` seul (utiliser `stake in`, `takes a stake`, `prise de participation`), `fusion\w*` seul (utiliser `fusion-acquisition`, `fusion de`, `fusionner`, avec exclusion `fusion nucléaire|fusion froide|fusion thermonucléaire`), `record\w*` comme "choc" (garder seulement avec `historique`, `record de baisse`, `plus bas`, `plus haut`).
- **Ton** : le ton lexical sans contexte est faux hors finance. Proposition : désactiver `POSITIVE_WORDS` sur les types non financiers, ou ne pas afficher de ton sur les items sans type financier, plutôt qu'afficher un faux positif sur une épidémie. Pour la communication, introduire un lexique négatif de **réputation** (polémique, scandale, accusation, plainte, boycott, excuses...) qui marque NEGATIVE à lui seul.
- **EVENT_MIX** et alertes : tout nouveau type doit être ajouté à `EVENT_TYPES` (back et miroir front `frontend-web/src/services/ops/opsTypes.ts`), à `EVENT_LEXICON` (le type `Record<EventType, EventLexicon>` oblige à compléter), au widget `EVENT_MIX`, aux filtres et aux règles ; les règles stockent `eventTypes String[]` donc aucune migration de schéma. Les métiers financiers verraient ces types dans leurs filtres : prévoir un masque de types par métier (liste de types proposés selon le préréglage).

### F4. Synthèse de priorités F
- **P0** : POLITICS_GOVERNMENT, ELECTIONS, JUSTICE_CRIME, SECURITY_TERRORISM, DISASTER_WEATHER, HEALTH_PUBLIC, DEATH_OBITUARY, REPUTATION_CRISIS (ces 8 débloquent l'impact et les alertes d'une rédaction et d'une direction de la communication).
- **P1** : CONFLICT_WAR, CLIMATE_ENVIRONMENT, DISINFORMATION, SOCIAL_MOVEMENT, MEDIA_INDUSTRY, correction des faux positifs (stake, fusion, strikes, record), ton non financier.
- **P2** : SPORT, CULTURE_ENTERTAINMENT.

---

## G. Plan de livraison suggéré (ordre de valeur)
1. Décider B2 (flux par channel vs éditeur) : conditionne l'ajout des flux de rubrique.
2. Étendre `PRO_SOURCE_CATALOG` avec B3.1 à B3.4 (une trentaine de slugs, plus les nouveaux flux sur les slugs existants) et remplacer `SOURCE_PRESETS.MEDIA_COMMS` par deux préréglages (Rédaction généraliste : franceinfo, Le Monde, Le Figaro, Libération, Ouest-France, 20 Minutes, BFMTV, TF1 Info, France 24, RFI, Euronews, BBC, Guardian, NYT, DW, Al Jazeera, Courrier international, Mediapart + sources primaires élysée, justice, conseil d'État, OMS, ECDC, CNIL, Commission européenne, ONU ; Communication : même socle presse + CNIL, Autorité de la concurrence, Défenseur des droits, EDPB, FTC + presse média/PR B3.7 + communiqués B3.9).
3. E1 (exclusions), E8 (rubriques), E5 (importance éditoriale) et les 8 types F4-P0.
4. E4 (ton et vélocité dans les règles), E2 (veille de marque), E10/E11 (email et webhook).
5. Corriger les alertes (D1) et refondre `MEDIA_COMMS` (C, D).
