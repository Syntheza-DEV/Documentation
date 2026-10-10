> Audit du 10/10/2026 (sources vérifiées en direct depuis un poste local ; l'hôte de prod en bloque certaines, voir centre-operations.md).

# Audit domaine : Trading + Énergie & matières premières

Auteur : AuditTrading. Date des mesures : samedi 10/10/2026 vers 18 h (Paris), depuis le poste de travail via `fetchFeed` (même garde SSRF que la production). Le week-end, les institutions sont au calme : les « âges » ci-dessous sont des maxima, relire en semaine. Fichier de test jetable `zz-audit-trading.test.ts` supprimé. Aucun fichier source modifié, aucun git.

Limite de méthode : un flux qui répond depuis le poste peut être refusé depuis l'hôte OVH (adresse de datacenter). Preuve en prod au 10/10 : `amf .../display/21` renvoie 403, `cisa` 403, `usine-nouvelle` et `venturebeat` 429 (`GET /api/channels`, champ `lastError`), alors que l'AMF 21 et l'Usine Nouvelle répondent OK depuis le poste. Tout nouveau flux doit être revérifié via `SOURCE_HEALTH` après le premier déploiement.

---

## A. Personas

### A1. Trader multi-actifs (salle de marché / prop / sales trader), pré-ouverture 7 h 30

- **Qui** : trader actions Paris/Europe/US (indices et grandes valeurs), taux et banques centrales, change, matières premières, crypto. Souvent 2 à 4 écrans, Bloomberg/Refinitiv (licence) déjà en place : Syntheza ne les remplace pas, il doit être **le filtre qui évite de rater le fait qui change la position** et la preuve datée de ce qui était connu à quelle heure.
- **Décisions** : garder, réduire, couvrir une position ; renoncer à une entrée ; prévenir un client (sales trader) ; décaler un ordre avant une annonce.
- **Ce qui le fait bouger** (par ordre de poids) : (1) banque centrale (décision, minutes, discours : BCE, Fed, BoE, BoJ, SNB, PBoC), (2) chiffres macro publiés (emploi US, inflation, PMI) et surprises, (3) résultats et avertissements sur résultats, M&A/OPA, notation, (4) géopolitique et douane (en ce moment : Moyen-Orient/Ormuz, Russie-diesel, droits de douane US), (5) dépêches de régulateurs (AMF, ESMA, SEC : enquêtes, suspensions, sanctions), (6) rumeurs « selon des sources » sur des valeurs suivies, (7) crypto : ETF, régulation, hacks, liquidations.
- **Sa journée**
  - 6 h 45 - 7 h 30 pré-ouverture : « qu'est-ce qui s'est passé depuis 17 h 30 hier et pendant la séance Asie ? » Il veut une page : (a) faits officiels, (b) faits repris par au moins 2 éditeurs, (c) rumeurs notées, (d) ce qui est attendu aujourd'hui (agenda). Le `/brief` actuel (fenêtre depuis la clôture, par actif, copie texte) répond à (a)-(c) ; il manque (d).
  - 9 h - 17 h 30 séance : fil temps réel filtré sur ses actifs, alertes sonores sur un nombre très restreint de règles, ouverture US à 15 h 30 (chiffres macro 14 h 30), clôtures anticipées.
  - 17 h 30 clôture : brief inverse (« ce qui reste ouvert cette nuit »), journal de décisions (piste d'audit).
  - Hebdo : agenda banque centrale, résultats de la semaine, revue des règles qui ont sonné pour rien.
- **Ce qui lui fait payer** (par siège, B2B) : latence de quelques secondes sur sources officielles ; **zéro bruit** (une alerte fausse coûte plus qu'une absence) ; exactitude des horodatages ; dédoublonnage par histoire ; couverture « il ne faut manquer aucune source » (officiel d'abord) ; export/partage pour le desk (copier pour chat, brief) ; piste d'audit.
- **Ce qui le fait fuir** : alerte sur « inflation » générique, 40 lignes sur la même histoire, dates fausses, ton « acheter/vendre » (déjà proscrit).

### A2. Trader / acheteur énergie & matières premières

- **Qui** : trader gaz/électricité/pétrole d'un énergéticien, acheteur industriel (couverture énergie, métaux, agricole), trader de matières premières agricoles ou métaux, responsable supply chain / fret.
- **Décisions** : acheter ou couvrir maintenant ou attendre ; déclencher une couverture avant une décision OPEP+ ; rouler un contrat ; arbitrer fret/pétrolier.
- **Ce qui le fait bouger** : OPEP+ (décisions, quotas), stocks hebdomadaires (EIA/API pour le brut, stockage de gaz GIE/AGSI), arrêts de réacteurs et indisponibilités (EDF, RTE), météo (canicule, vague de froid, ouragans Golfe du Mexique, niveau du Rhin, hydraulicité), sanctions et plafonds de prix (Russie, Iran), chokepoints (Ormuz, mer Rouge, Suez, Panama), GNL (Qatar, USA, terminaux), grèves (raffineries, ports), politique agricole et USDA, taux de fret (BDI), prix du carbone (EU ETS).
- **Sa journée** : 7 h 00 (lecture : nuit Asie, stocks américains de la veille au soir, météo, arrêts nucléaires), 8 h - 9 h (Brent et TTF s'ouvrent avant les actions ; il positionne ses achats), 14 h 30 / 16 h 30 (EIA mercredi, données US), 17 h 30 (prix de clôture de référence, fixing), semaine : réunions OPEP+, rapport IEA mensuel, WASDE USDA.
- **Ce qui le fait payer** : un seul endroit pour **événements d'offre** (panne, grève, sanction, météo) rapprochés des sources primaires (EIA, GIE, EDF, ASNR, NOAA, USDA) ; alertes sur un actif précis (TTF, Brent, une centrale) ; chiffres clés **avec unités** ($/baril, €/MWh, GW, bcm, Mb/j) lisibles d'un coup d'œil.

---

## B. Sources

### B1. État du catalogue et du préréglage actuel

Préréglage TRADING (17 slugs, `professions.ts` L74-92) : `bce-ecb, federal-reserve, bank-of-england, bis, amf, esma, sec, financial-times, cnbc, marketwatch, bfm-bourse, bfm-economie, investing-fr, le-monde, le-figaro, bbc-news, coindesk`.

Constats (mesurés) :
- `marketwatch` : le seul flux (`mw_topstories`) est dominé par du conseil patrimonial/lifestyle (item du jour « A friend in her 80s fell down her basement stairs... »). Les flux marchés `mw_marketpulse` et `mw_realtimeheadlines` sont **gelés** (dernier item il y a 11 139 h et 11 673 h) : à ne pas utiliser. Flux utile : `mw_bulletins` (10 items, 0,8 h, « S&P 500 at 10,000 or bust? »).
- `financial-times` : seul `ft.com/rss/home` (12 items). Les rubriques en donnent 25 chacune et sont bien plus pertinentes (liste en B2).
- `le-monde`, `le-figaro`, `bbc-news`, `bfm-economie` : flux généralistes économie ; faible densité trading. Les rubriques Figaro bourse/matières premières/conjoncture et Le Monde entreprises sont préférables. Garder `bbc-news` business (utile pour l'actualité marchés UK/Trump/diesel) mais retirer `bbc world` (bruit) du préréglage trading.
- `cnbc` : 4 flux, tous vivants (30 items) : OK.
- `bis` : communiqués rares (dernier il y a 136 h) mais primaires : garder ; ajouter les discours de banquiers centraux (`cbspeeches.rss`).
- `esma` : **aucune date lisible** dans les items (ni `pubDate` ni `isoDate`). Idem `conseil-ue`. À corriger côté ingestion (voir E, P0 #4).
- `amf display/21` : OK au poste, **403 en prod** : prévoir `display/30` (200 items, 26,8 h) en secours.
- `coindesk` seul pour la crypto : ajouter The Block, Decrypt, Cointelegraph (déjà au catalogue mais hors préréglage).

Préréglage ENERGY_COMMODITIES (9 slugs, L130-140) : `oilprice, rigzone, connaissance-des-energies, usine-nouvelle, cnbc, investing-fr, conseil-ue, financial-times, al-jazeera`.
- **À retirer ou rendre optionnels** : `usine-nouvelle` (généraliste industrie, 429 en prod), `al-jazeera` (généraliste ; à garder seulement si coche « géopolitique »), `cnbc` (4 flux dont un seul à dominante énergie ; remplacer par la rubrique énergie seule), `investing-fr` (5 flux mixtes ; ne garder que `news_11` = matières premières et `commodities.rss`). `conseil-ue` : utile (sanctions), à garder.
- **Manque** : toute source primaire (EIA, GIE, NOAA/NHC, USDA, EDF, ASNR, DESNZ), fret/maritime, agricole, nucléaire, carbone, presse énergie FR/EN de qualité.

### B2. NOUVELLES sources vérifiées (acceptées)

Critères : parse OK, items > 0, plus récent < 7 jours (< 30 jours pour les flux institutionnels à basse fréquence, marqués « BF »). Âges mesurés samedi 18 h. Trust : primaire/officiel 90+, presse majeure 80-88, spécialisée 70-80, agrégateur 60-70.

#### B2.1 Institutions et régulateurs (catégorie INSTITUTIONS)

| Nom | slug | domaine | cat. | trust | flux | items | plus récent |
|---|---|---|---|---|---|---|---|
| Banque du Japon | `boj` | boj.or.jp | INSTITUTIONS | 95 (banque centrale) | `https://www.boj.or.jp/en/rss/whatsnew.xml` | 51 | 31 h |
| Banque nationale suisse | `snb` | snb.ch | INSTITUTIONS | 95 | `https://www.snb.ch/public/en/rss/news` ; `.../rss/pressrel` ; `.../rss/mopo` ; `.../rss/adhoc` ; `.../rss/exchangeRates` (cours quotidiens) | 20 / 20 / 15 / 35 / 28 | 0,1 h / 216 h BF / 243 h BF / 216 h BF / 29 h |
| Reserve Bank of India | `rbi` | rbi.org.in | INSTITUTIONS | 93 | `https://www.rbi.org.in/pressreleases_rss.xml` ; `.../notifications_rss.xml` | 10 / 10 | 16,8 h |
| Reserve Bank of Australia | `rba` | rba.gov.au | INSTITUTIONS | 94 | `https://www.rba.gov.au/rss/rss-cb-media-releases.xml` | 1 | 230 h BF (1 item seulement) |
| Riksbank | `riksbank` | riksbank.se | INSTITUTIONS | 92 | `https://www.riksbank.se/en-gb/rss/press-releases/` | 10 | 58 h |
| FSB | `fsb` | fsb.org | INSTITUTIONS | 93 | `https://www.fsb.org/feed/` | 10 | 34 h |
| FINMA | `finma` | finma.ch | INSTITUTIONS | 93 | `https://www.finma.ch/en/rss/news/` | 50 | 52 h |
| OMC / WTO | `omc-wto` | wto.org | INSTITUTIONS | 90 | `https://www.wto.org/library/rss/latest_news_e.xml` ; `.../latest_news_f.xml` | 10 / 8 | 64 h |
| Commission européenne (salle de presse) | `commission-ue` | ec.europa.eu | INSTITUTIONS | 94 | `https://ec.europa.eu/commission/presscorner/api/rss?language=fr` (idem `=en`) | 10 | 25 h |
| Maison-Blanche | `maison-blanche` | whitehouse.gov | INSTITUTIONS | 92 (primaire, politique : droits de douane) | `https://www.whitehouse.gov/briefings-statements/feed/` ; `https://www.whitehouse.gov/presidential-actions/feed/` | 30 / 30 | 16 h / 63 h |
| USTR | `ustr` | ustr.gov | INSTITUTIONS | 93 | `https://www.ustr.gov/rss.xml` | 10 | 21 h |
| HM Treasury (UK) | `hm-treasury` | gov.uk | INSTITUTIONS | 92 | `https://www.gov.uk/government/organisations/hm-treasury.atom` | 20 | 65 h |
| US Bureau of Labor Statistics | `bls` | bls.gov | INSTITUTIONS | 97 | `https://www.bls.gov/feed/bls_latest.rss` (1 item, mis à jour à chaque publication) ; `.../cpi.rss` (« CPI for all items increases 0.4% in August ») ; `.../empsit.rss` (« payroll employment (+29,000) ») | 1 / 12 / 12 | 26 h / 700 h BF mensuel / 196 h BF mensuel |
| US Bureau of Economic Analysis | `bea` | bea.gov | INSTITUTIONS | 97 | `https://www.bea.gov/rss/rss.xml` | 48 | 99,7 h (publication à date fixe) |
| US Census Bureau (indicateurs) | `census-us` | census.gov | INSTITUTIONS | 95 | `https://www.census.gov/economic-indicators/indicator.xml` | 18 | 50 h |
| ONS (Royaume-Uni) calendrier de publication | `ons-uk` | ons.gov.uk | INSTITUTIONS | 94 | `https://www.ons.gov.uk/releasecalendar?rss` | 10 | 31,7 h |
| Fed : données H.15 (taux) et H.10 (change) | `fed-data` | federalreserve.gov | INSTITUTIONS | 97 | `https://www.federalreserve.gov/feeds/Data/H15_H15.XML` (« US: H15 3.88 2026-10-08 FRB Federal funds ») ; `.../H10_H10.XML` (indice dollar large) | 30 / 155 | 47 h / 191 h BF |
| Taux de référence BCE (EUR/USD, GBP, JPY, CHF...) | `bce-taux-reference` | ecb.europa.eu | INSTITUTIONS | 96 | `https://www.ecb.europa.eu/rss/fxref-usd.html` (+ `fxref-gbp`, `fxref-jpy`, `fxref-chf`, 5 items chacun) | 5 | 26,9 h (quotidien 16 h) |
| FCA (Royaume-Uni) | `fca` | fca.org.uk | INSTITUTIONS | 94 | `https://www.fca.org.uk/news/rss.xml` | 20 | **date non parsable** (« Friday, October 9, 2026 - 12:06 ») : à n'ajouter qu'après un correctif de date |

**Flux supplémentaires à ajouter à des slugs EXISTANTS** (même éditeur, pas de nouvelle entrée) :

| slug existant | flux à ajouter | items | plus récent |
|---|---|---|---|
| `federal-reserve` | `https://www.federalreserve.gov/feeds/press_monetary.xml` (minutes FOMC) ; `.../speeches.xml` ; `.../press_enforcement.xml` ; `.../feds_notes.xml` | 15 / 15 / 15 / 15 | 70 h / 55,6 h / 43,7 h / 22,7 h |
| `bce-ecb` | `https://www.ecb.europa.eu/rss/statpress.html` (communiqués statistiques) ; `.../rss/wppub.html` ; `.../rss/rbu.html` | 15 chacun | 52,7 h / 79 h / 58 h |
| `bank-of-england` | `https://www.bankofengland.co.uk/rss/speeches` ; `.../rss/publications` ; `.../rss/statistics` | 50 chacun | 51,9 h / 27,1 h / 127,7 h |
| `bis` | `https://www.bis.org/doclist/cbspeeches.rss` (discours des banquiers centraux) ; `.../bispapers.rss` | 50 / 25 | 64 h |
| `sec` | `https://www.sec.gov/enforcement-litigation/litigation-releases/rss` ; `.../administrative-proceedings/rss` ; `https://www.sec.gov/news/speeches-statements.rss` | 25 chacun | 20,5 h / 25,8 h / 25,7 h |
| `amf` | `https://www.amf-france.org/fr/flux-rss/display/22` ; `display/30` ; `display/23` (communiqués) | 200 / 200 / 200 | 32 h / 26,8 h / 200 h BF |

#### B2.2 Opérateurs de marché (nouvelle catégorie proposée `MARKET_OPERATORS`, sinon INSTITUTIONS)

| Nom | slug | domaine | cat. | trust | flux | items | plus récent |
|---|---|---|---|---|---|---|---|
| Euronext (salle de presse) | `euronext` | euronext.com | MARKET_OPERATORS | 92 | `https://www.euronext.com/en/news/rss` (idem `/fr/news/rss`) | 20 | `pubDate` au format `dd/mm/yyyy` (« 07/10/2026 ») lu à l'américain par `Date.parse` : le plus récent apparaît à -703 h (futur) et le reste est faux. **Correctif de date requis avant d'activer** (voir E P0 #4). |
| Intercontinental Exchange (communiqués) | `ice` | ir.theice.com | MARKET_OPERATORS | 88 | `https://ir.theice.com/rss/pressrelease.aspx` | 10 | 20 h (bruit : « Short Interest Report », mais aussi lancements de contrats énergie/fret) |

#### B2.3 Presse financière et économique (FINANCIAL_PRESS)

| Nom | slug | domaine | cat. | trust | flux | items | plus récent |
|---|---|---|---|---|---|---|---|
| Bloomberg (flux publics) | `bloomberg` | bloomberg.com | FINANCIAL_PRESS | 88 (presse majeure ; titre + chapô) | `https://feeds.bloomberg.com/markets/news.rss` ; `.../economics/news.rss` ; `.../industries/news.rss` ; `.../politics/news.rss` ; `.../crypto/news.rss` | 20 / 11 / 15 / 20 / 1 | 0,1 h / 3,5 h / 2,2 h / 1 h / 3,7 h |
| Les Echos | `les-echos` | lesechos.fr | FINANCIAL_PRESS | 86 | `https://services.lesechos.fr/rss/les-echos-finance-marches.xml` ; `.../les-echos-economie.xml` ; `.../les-echos-entreprises.xml` ; `.../les-echos-monde.xml` | 20 chacun | 8,2 h / 3,4 h / 5,7 h / 1,9 h |
| Wall Street Journal (flux Dow Jones) | `wsj` | wsj.com | FINANCIAL_PRESS | 87 | `https://feeds.content.dowjones.io/public/rss/RSSMarketsMain` ; `.../WSJcomUSBusiness` ; `.../RSSWorldNews` | 61 / 85 / 71 | 14,1 h (samedi) / 5,1 h / 0,6 h |
| MarketWatch : bulletins | (slug existant `marketwatch`) | marketwatch.com | FINANCIAL_PRESS | 80 | `https://feeds.content.dowjones.io/public/rss/mw_bulletins` | 10 | 0,8 h |
| Financial Times par rubriques | (slug existant `financial-times`) | ft.com | FINANCIAL_PRESS | 88 | `https://www.ft.com/markets?format=rss` ; `.../companies?format=rss` ; `.../commodities?format=rss` ; `.../energy?format=rss` ; `.../central-banks?format=rss` ; `.../currencies?format=rss` ; `.../bonds?format=rss` ; `.../equities?format=rss` ; `.../eurozone-economy?format=rss` ; `.../mergers-acquisitions?format=rss` ; `.../us-economy?format=rss` ; `.../banks?format=rss` ; `.../emerging-markets?format=rss` ; `.../cryptocurrencies?format=rss` ; `.../lex?format=rss` | 25 chacun | de 0 h à 36 h |
| The New York Times (économie) | `nyt-business` | nytimes.com | FINANCIAL_PRESS | 85 | `https://rss.nytimes.com/services/xml/rss/nyt/Business.xml` ; `.../Economy.xml` | 49 / 20 | 1,6 h / 25 h |
| The Economist | `the-economist` | economist.com | FINANCIAL_PRESS | 84 (hebdomadaire) | `https://www.economist.com/finance-and-economics/rss.xml` ; `.../business/rss.xml` | 300 / 300 | 54,7 h / 51 h |
| Handelsblatt | `handelsblatt` | handelsblatt.com | FINANCIAL_PRESS | 82 (utile pour DAX/Bund ; allemand) | `https://www.handelsblatt.com/contentexport/feed/finanzen` ; `.../schlagzeilen` | 20 / 50 | 2,1 h / 0,1 h |
| Washington Post (économie) | `washington-post` | washingtonpost.com | FINANCIAL_PRESS | 82 | `https://feeds.washingtonpost.com/rss/business` | 5 | 6,2 h |
| Sky News (économie) | `sky-news` | news.sky.com | GENERAL_PRESS | 78 | `https://feeds.skynews.com/feeds/rss/business.xml` | 10 | 5 h |
| Euronews (économie) | `euronews` | euronews.com | GENERAL_PRESS | 78 | `https://www.euronews.com/rss?format=mrss&level=theme&name=business` | 50 | 26,3 h |
| Deutsche Welle (économie) | `dw` | dw.com | GENERAL_PRESS | 80 | `https://rss.dw.com/xml/rss-en-bus` ; FR : `https://rss.dw.com/xml/rss-fr-eco` (non testé) | 20 | 32 h |
| Politico Europe | `politico-eu` | politico.eu | GENERAL_PRESS | 80 | `https://www.politico.eu/feed/` | 10 | 1,5 h |
| RFI (économie) | `rfi` | rfi.fr | GENERAL_PRESS | 82 | `https://www.rfi.fr/fr/economie/rss` ; `https://www.rfi.fr/fr/rss` | 30 / 23 | 5 h / 0,6 h |
| Straits Times (business) | `straits-times` | straitstimes.com | FINANCIAL_PRESS | 80 (Asie) | `https://www.straitstimes.com/news/business/rss.xml` | 20 | 9,2 h |
| Channel NewsAsia (business) | `cna` | channelnewsasia.com | GENERAL_PRESS | 80 | `https://www.channelnewsasia.com/rssfeeds/8395954` | 20 | 16,2 h |
| Economic Times (marchés) | `economic-times` | economictimes.indiatimes.com | FINANCIAL_PRESS | 78 (Inde) | `https://economictimes.indiatimes.com/markets/rssfeeds/1977021501.cms` | 50 | 2,2 h |
| Mint (marchés) | `livemint` | livemint.com | FINANCIAL_PRESS | 76 (Inde) | `https://www.livemint.com/rss/markets` | 35 | 0 h |
| Valor Econômico | `valor` | valor.com.br | FINANCIAL_PRESS | 78 (Brésil, portugais) | `https://www.valor.com.br/rss` | 100 | 0,2 h |
| Telegraph (business) | `telegraph` | telegraph.co.uk | FINANCIAL_PRESS | 78 | `https://www.telegraph.co.uk/business/rss.xml` | 120 | 17,2 h |
| Bank Dive | `banking-dive` | bankingdive.com | FINANCIAL_PRESS | 72 | `https://www.bankingdive.com/feeds/news/` | 10 | 24 h |
| investingLive (ex-ForexLive) | `investinglive` | investinglive.com | FINANCIAL_PRESS | 72 (spécialisé change/macro, flux de dépêches) | `https://investinglive.com/feed` ; `https://www.forexlive.com/feed/news` | 25 / 25 | 4,6 h / 18,8 h |
| FXStreet | `fxstreet` | fxstreet.com | FINANCIAL_PRESS | 68 | `https://www.fxstreet.com/rss/news` ; `https://www.fxstreet.com/rss` | 30 / 30 | 17,8 h / 8,3 h |
| Investing.com (EN) | `investing-com` | investing.com | FINANCIAL_PRESS | 70 | `https://www.investing.com/rss/news.rss` ; `news_25` (actions) ; `news_1` (change) ; `news_11` (matières premières) ; `news_14` (économie) ; `news_95` ; `news_285` ; `news_301` (crypto) ; `commodities.rss` ; `forex.rss` | 10 chacun | 2 à 22 h |
| Investing.com FR (flux à ajouter à `investing-fr`) | (slug existant) | fr.investing.com | FINANCIAL_PRESS | 72 | `https://fr.investing.com/rss/news_14.rss` ; `news_285.rss` ; `news_301.rss` (crypto) ; `commodities.rss` ; `stock.rss` ; `market_overview.rss` | 10 chacun | 2,6 h / 3,5 h / 11,9 h / 31,7 h / 32,5 h / 32,6 h. **Éviter `news_1065`** (articles promotionnels « signal de Juste Valeur InvestingPro ») |
| Le Figaro (rubriques) | (slug existant `le-figaro`) | lefigaro.fr | FINANCIAL_PRESS | 82 | `https://www.lefigaro.fr/rss/figaro_bourse.xml` ; `.../figaro_matieres-premieres.xml` ; `.../figaro_conjoncture.xml` ; `.../figaro_flash-eco.xml` ; `.../figaro_societes.xml` | 20 chacun | 8,1 h / 1,9 h / 2,5 h / 0,2 h / 8,7 h |
| Le Monde (rubriques) | (slug existant `le-monde`) | lemonde.fr | GENERAL_PRESS | 88 | `https://www.lemonde.fr/entreprises/rss_full.xml` ; `.../energies/rss_full.xml` | 20 / 20 | 3,1 h / 7,7 h |
| La Tribune (rubrique Bourse) | (slug existant `la-tribune`) | latribune.fr | FINANCIAL_PRESS | 80 | `https://www.latribune.fr/rss/rubriques/bourse.html` | 20 | 24 h |
| BFM (entreprises) | (slug existant `bfm-economie`) | bfmtv.com | FINANCIAL_PRESS | 76 | `https://www.bfmtv.com/rss/economie/entreprises/` | 30 | 3,8 h |
| Seeking Alpha | `seeking-alpha` | seekingalpha.com | FINANCIAL_PRESS | 62 (agrégateur/blogueurs) | `https://seekingalpha.com/market_currents.xml` ; `https://seekingalpha.com/feed.xml` | 7 / 30 | 0,1 h / 0,2 h |
| Nasdaq.com (Markets, Stocks, Earnings) | `nasdaq-com` | nasdaq.com | FINANCIAL_PRESS | 62 (**contenu Zacks avec « Better Buy » et « Is It Time to Sell? »** : formulations de conseil, risque AMF) | `https://www.nasdaq.com/feed/rssoutbound?category=Markets` ; `...Stocks` ; `...Earnings` | 15 chacun | 0,5 h / 0,9 h / 17,5 h. **À n'ajouter qu'avec filtre de ton** : sinon rejeter. |
| Yahoo Finance (par instrument) | `yahoo-finance` | finance.yahoo.com | AGGREGATOR (nouvelle) | 62 | `https://feeds.finance.yahoo.com/rss/2.0/headline?s=%5EFCHI&region=FR&lang=fr-FR` (CAC 40, FR) ; `https://finance.yahoo.com/rss/headline?s=^GSPC` ; `...?s=AAPL...` | 20 / 19 / 16 | 9,7 h / 0,7 h / 0,1 h. Intérêt : un flux **par symbole** (voir E, gap watchlist → flux). |
| Google Actualités (recherche) | `google-news` | news.google.com | AGGREGATOR | 58 (réagrégation Reuters/Bloomberg/AFP ; CGU à vérifier) | `https://news.google.com/rss/search?q=Brent+crude&hl=en-US&gl=US&ceid=US:en` ; `...q=CAC+40&hl=fr&gl=FR&ceid=FR:fr` ; `.../headlines/section/topic/BUSINESS?hl=en-US&gl=US&ceid=US:en` | 102 / 100 / 70 | 4,7 h / 0,4 h / 2,8 h. La requête `allinurl:reuters.com` renvoie 0 item. **Risque juridique et fragilité** : P2 seulement. |
| TASS | `tass` | tass.com | GENERAL_PRESS | 45 (**média d'État russe**, à marquer comme tel) | `https://tass.com/rss/v2.xml` | 57 | 0,1 h. Utile pour lire les annonces russes à la source ; trust bas. |
| The Moscow Times | `moscow-times` | themoscowtimes.com | GENERAL_PRESS | 72 | `https://www.themoscowtimes.com/rss/news` | 50 | 4,8 h |
| Global Times | `global-times` | globaltimes.cn | GENERAL_PRESS | 45 (média d'État chinois) | `https://www.globaltimes.cn/rss/outbrain.xml` | 50 | 25,9 h |

#### B2.4 Crypto (CRYPTO)

| Nom | slug | domaine | cat. | trust | flux | items | plus récent |
|---|---|---|---|---|---|---|---|
| The Block | `the-block` | theblock.co | CRYPTO | 76 | `https://www.theblock.co/rss.xml` | 20 | 1 h |
| Decrypt | `decrypt` | decrypt.co | CRYPTO | 70 | `https://decrypt.co/feed` | 38-58 | 0,1 h |
| The Defiant | `the-defiant` | thedefiant.io | CRYPTO | 70 | `https://thedefiant.io/feed` | 100 | 1,2 h |
| Bitcoin Magazine | `bitcoin-magazine` | bitcoinmagazine.com | CRYPTO | 62 | `https://bitcoinmagazine.com/.rss/full/` | 10 | 15,2 h |
| Cointelegraph (flux supplémentaires) | (slug existant) | cointelegraph.com | CRYPTO | 64 | `https://cointelegraph.com/rss/category/markets` ; `.../tag/bitcoin` | 30 / 30 | 24,8 h / 9 h |
| CryptoPotato | `cryptopotato` | cryptopotato.com | CRYPTO | 58 | `https://cryptopotato.com/feed/` | 36 | 1,6 h |
| Blog Ethereum Foundation | `ethereum-foundation` | blog.ethereum.org | CRYPTO | 85 (primaire, BF) | `https://blog.ethereum.org/feed.xml` | 642 | 136 h BF |
| Blog Kraken | `kraken-blog` | blog.kraken.com | CRYPTO | 78 (primaire plateforme : listings) | `https://blog.kraken.com/feed` | 10 | 48 h |

#### B2.5 Énergie, matières premières, fret, agricole (catégorie ENERGY ou nouvelles `AGRI`, `MARITIME`)

Justification des nouvelles catégories : `ENERGY` mélange déjà industrie (Usine Nouvelle) et énergie ; le fret/maritime et l'agricole ont un lectorat, des mots-clés et des presets distincts (acheteur agro, supply chain). Alternative sans changer l'enum : tout en `ENERGY`.

| Nom | slug | domaine | cat. | trust | flux | items | plus récent |
|---|---|---|---|---|---|---|---|
| EIA (Administration américaine d'information énergétique) | `eia` | eia.gov | ENERGY | 96 | `https://www.eia.gov/rss/press_rss.xml` ; `https://www.eia.gov/rss/todayinenergy.xml` | 12 / 15 | 95 h (publications hebdo/mensuelles) / 74 h. **Bug de date** : « Today in Energy » donne `pubDate` « Fri, 09 Oct  09:00:00 EST » sans année, lue comme **2001** (voir E P0 #4). Le catalogue disait EIA « refusé » ; ces deux URLs répondent. |
| Gas Infrastructure Europe (GIE) | `gie` | gie.eu | ENERGY | 88 | `https://www.gie.eu/feed/` | 10 | 98,7 h BF (le stockage AGSI/ALSI lui-même n'a pas de RSS) |
| National Hurricane Center (NOAA) | `noaa-nhc` | nhc.noaa.gov | ENERGY | 97 | `https://www.nhc.noaa.gov/index-at.xml` ; `https://www.nhc.noaa.gov/gtwo.xml` | 11 / 3 | 0,6 h / 4,3 h (items « No current storm » : bruit, filtrer ; pertinent saison cyclonique, production Golfe du Mexique) |
| USDA (communiqués) | `usda` | usda.gov | AGRI | 94 | `https://www.usda.gov/rss/latest-releases.xml` | 10 | 21 h (généraliste : préparation cyclone, forêts...) |
| Ministère de l'Agriculture (FR) | `minagri-fr` | agriculture.gouv.fr | AGRI | 92 | `https://www.agriculture.gouv.fr/rss.xml` | 30 | 26 h |
| farmdoc daily (Univ. de l'Illinois) | `farmdoc-daily` | farmdocdaily.illinois.edu | AGRI | 80 | `https://farmdocdaily.illinois.edu/feed` | 10 | 19 h |
| EDF (groupe, communiqués) | `edf-groupe` | edf.com | ENERGY | 90 (primaire entreprise) | `https://www.edf.com/en/rss.xml` | 10 | 23,3 h. Ne pas utiliser `edf.fr/rss.xml` (contenu grand public : « Découvrez le Rhin autrement »). |
| ASNR (ex-ASN depuis 2025) | `asnr` | asnr.fr | ENERGY | 95 | `https://www.asnr.fr/rss.xml` | 10 | 50 h, **mais le contenu relevé est surtout institutionnel** (« Soutenance de thèse ») : valeur à confirmer en semaine, sinon rejeter. |
| DESNZ (Royaume-Uni, énergie) | `desnz` | gov.uk | ENERGY | 92 | `https://www.gov.uk/search/news-and-communications.atom?organisations%5B%5D=department-for-energy-security-and-net-zero` | 20 | 65 h (le premier item est identique à celui de HM Treasury : vérifier que le filtre par organisme s'applique) |
| National Grid | `national-grid` | nationalgrid.com | ENERGY | 85 | `https://www.nationalgrid.com/rss.xml` | 10 | 31 h |
| World Nuclear News | `world-nuclear-news` | world-nuclear-news.org | ENERGY | 80 | `https://world-nuclear-news.org/rss` | 47 | 23,4 h |
| Natural Gas Intelligence | `ngi` | naturalgasintel.com | ENERGY | 76 | `https://www.naturalgasintel.com/feed/` | 10 | 18,9 h |
| LNG Prime | `lng-prime` | lngprime.com | ENERGY | 75 | `https://www.lngprime.com/feed/` | 10 | 30,2 h |
| Offshore Energy | `offshore-energy` | offshore-energy.biz | ENERGY | 72 | `https://www.offshore-energy.biz/feed/` | 50 | 27,5 h |
| OE Digital | `oe-digital` | oedigital.com | ENERGY | 70 | `https://www.oedigital.com/rss` | 15 | 27,7 h |
| Carbon Pulse | `carbon-pulse` | carbon-pulse.com | ENERGY | 75 (carbone) | `https://carbon-pulse.com/feed/` | 10 | 9,1 h |
| Carbon Brief | `carbon-brief` | carbonbrief.org | ENERGY | 80 | `https://www.carbonbrief.org/feed/` | 12 | 34 h |
| Energy Monitor | `energy-monitor` | energymonitor.ai | ENERGY | 72 | `https://www.energymonitor.ai/feed/` | 10 | 30,8 h |
| Utility Dive | `utility-dive` | utilitydive.com | ENERGY | 74 | `https://www.utilitydive.com/feeds/news/` | 10 | 25,7 h |
| PV Magazine (EN et FR) | `pv-magazine` | pv-magazine.com | ENERGY | 70 | `https://www.pv-magazine.com/feed/` ; `https://www.pv-magazine.fr/feed/` | 10 / 10 | 6,7 h / 27,2 h |
| Actu-Environnement | `actu-environnement` | actu-environnement.com | ENERGY | 74 | `https://www.actu-environnement.com/flux/rss/environnement/` | 26 | 24,4 h |
| Clean Energy Wire | `clean-energy-wire` | cleanenergywire.org | ENERGY | 80 | `https://www.cleanenergywire.org/rss.xml` | 10 | 388 h BF |
| Enerdata | `enerdata` | enerdata.net | ENERGY | 72 | `https://www.enerdata.net/rss.xml` | 10 | 290 h BF |
| Elengy (terminaux GNL) | `elengy` | elengy.com | ENERGY | 85 | `https://www.elengy.com/rss.xml` | 10 | 364 h BF |
| Hellenic Shipping News (maritime, énergie, vrac) | `hellenic-shipping-news` | hellenicshippingnews.com | MARITIME | 70 (reprise de communiqués et de revues de marché ; relaie les déclarations IEA/OPEP+) | `https://www.hellenicshippingnews.com/feed/` ; `.../category/oil-energy/feed/` ; `.../category/dry-bulk-market/feed/` (« Baltic Dry Index at Over 1-Month Low ») | 20 / 20 / 20 | 19,1 h / 47,2 h / 19,2 h (les catégories tanker et container renvoient 0 item) |
| gCaptain | `gcaptain` | gcaptain.com | MARITIME | 72 | `https://gcaptain.com/feed/` | 12 | 19,6 h |
| Splash247 | `splash247` | splash247.com | MARITIME | 72 | `https://splash247.com/feed/` | 10 | 9,9 h |
| Seatrade Maritime | `seatrade` | seatrade-maritime.com | MARITIME | 72 | `https://www.seatrade-maritime.com/rss.xml` | 50 | 26 h (« Iran threatens wider tanker attacks as VLCC struck in Gulf ») |
| Journal of Commerce | `joc` | joc.com | MARITIME | 74 | `https://www.joc.com/rss.xml` | 40 | 23,5 h |
| FreightWaves | `freightwaves` | freightwaves.com | MARITIME | 68 | `https://www.freightwaves.com/news/feed` | 56 | 21,2 h |
| Xeneta (fret conteneurs hebdo) | `xeneta` | xeneta.com | MARITIME | 70 | `https://www.xeneta.com/news/rss.xml` | 10 | 53 h |
| MarineLink | `marinelink` | marinelink.com | MARITIME | 68 | `https://www.marinelink.com/rss` | 10 | 23,7 h |
| Terre-net | `terre-net` | terre-net.fr | AGRI | 72 | `https://www.terre-net.fr/rss` | 20 | 7,1 h |
| La France Agricole | `la-france-agricole` | lafranceagricole.fr | AGRI | 72 | `https://www.lafranceagricole.fr/rss` | 20 | 5,1 h |
| Réussir | `reussir` | reussir.fr | AGRI | 70 | `https://www.reussir.fr/rss.xml` | 20 | 2,1 h |
| Web-agri | `web-agri` | web-agri.fr | AGRI | 70 | `https://www.web-agri.fr/rss` | 20 | 5,1 h |
| Agri Mutuel | `agri-mutuel` | agri-mutuel.com | AGRI | 70 | `https://www.agri-mutuel.com/feed/` | 18 | 7,1 h |
| Brownfield Ag News | `brownfield-ag` | brownfieldagnews.com | AGRI | 66 | `https://www.brownfieldagnews.com/feed/` | 20 | 19 h |
| The Guardian (pétrole, énergie, Moyen-Orient) | (slug existant `the-guardian`) | theguardian.com | GENERAL_PRESS | 84 | `https://www.theguardian.com/business/oil/rss` ; `.../environment/energy/rss` ; `.../world/middleeast/rss` | 20 chacun | 14,6 h / 28,1 h / 0,2 h |
| The Hill (énergie) | `the-hill-energy` | thehill.com | GENERAL_PRESS | 72 | `https://thehill.com/policy/energy-environment/feed/` | 15 | 16,1 h |
| Euronext (cf. B2.2) | | | | | | | |

### B3. Candidats REJETÉS (avec raison)

| Candidat | Raison |
|---|---|
| Reuters (tous formats testés), Barron's, Zonebourse, Boursorama, ABC Bourse, L'Agefi, Cercle Finance (0 item), Boursier.com, Capital, Investir | 403/404/pas de flux valide |
| Banque de France (3 URLs), INSEE (404/500), Agence France Trésor (403), Trésor/Bercy, Gouvernement.fr (403), ACPR (pas de flux), Eurostat portail (pas de flux) | refus ou introuvable. `eurostat .../statistics-update.rss` répond (2 385 items, 7 h) mais chaque item est « EI_ISBR_M - Dataset: updated data » : illisible, rejeté |
| US Treasury (2 URLs), NY Fed (2), IMF (3), Banque mondiale (3), OCDE (2), Commerce.gov, BIS doc.gov, CFTC (403), Fiscaldata (403), Fed H.4.1 | refus 403/404/pas de flux valide |
| Bank of Canada | `feed/` : 10 items dont des événements futurs (-3 911 h) ; `press-releases` : 725 h (> 30 jours) |
| SEC `trading-suspensions` | dernier item il y a 2 895 h |
| SEC EDGAR 8-K et Form 4 (atom) | répond (40 items, 14-19 h) mais firehose illisible (« 8-K - AUTOZONE INC (0000866787) (Filer) ») : à retenir seulement pour une fonction « dépôts de mes valeurs » (voir E P2) |
| Fed `testimony.xml`, `h41.xml`, BoE `events` | dernier item 2 115 h / 2 059 h, ou événements futurs : inutilisables |
| ECB `pub.html` | pas de flux valide ; `yc.html` (3 items, pages interactives) et `operations.html` (32 items, opérations de liquidité en code) trop bruités |
| OPEC, IEA, Fastmarkets, Argus, S&P Platts, Montel, LME, CME, Kitco, Mining.com, Mining Weekly, BullionVault, Gold.org (« Gold Investor, October 2018 ») | refus 403/404/introuvable, ou contenu ancien |
| RTE, CRE, ENTSO-E, ACER, Enedis, Engie, TotalEnergies, Shell, BP, Exxon, Equinor, Aramco, Eni | pas de flux valide / 404 (Engie 0 item) |
| ENTSOG (27 239 h), GRTgaz/NaTran (1 248 h, « Témoignages d'alternants »), Cleantech/Euractiv (introuvable), World Oil (aucune date) | périmé ou illisible |
| USDA AMS (« G-175 Grand Western Angus Beef Program »), NOAA général, usda.gov/home (13 298 h), energy.gov (55 513 h), Eurostat | bruit ou périmé |
| MarketWatch `mw_marketpulse`, `mw_realtimeheadlines` | gelés (11 139 h et 11 673 h) |
| Investing.com `central_banks.rss` (2016), `market_overview_Fundamental`, `stock_Fundamental`, `stock_Technical` (août 2026), `news_1065` (promotionnel) | périmé ou promotionnel |
| Benzinga (« Toncoin Price Prediction 2025, 2026, 2027-2030 »), CryptoNews, U.Today (« Almost Like Satoshi Knew Something »), Bitcoinist, NewsBTC, DailyHodl, AMBCrypto, Blockworks (dates incohérentes), BeInCrypto | qualité éditoriale basse / prédictions de prix / promotionnel |
| ZeroHedge, Wolf Street, Mish Talk, Calculated Risk (6 507 h), Ritholtz, MoneyWeek, This Is Money, Le Revenu (conseil particulier) | blogs ou conseil particuliers : hors cible et formulations d'achat/vente |
| Nikkei Asia (50 items, **aucune date**), SCMP `91/92`, Independent (1 item), Business Insider, Axios, Liberation, Ouest-France, Interfax (russe), Tehran Times | pas de date / généraliste hors cible / un seul item |
| Reuters-via-Google (`allinurl:reuters.com`) | 0 item |
| Bruegel, Ifri (3 ans), NBER (sans date), Xinhua business (2017) | académique/périmé |
| ICE `cmegroup`, Euronext `/en/rss` etc. | 404 (seul `/news/rss` existe) |

### B4. Préréglages recommandés (réécriture)

**TRADING** (cible 38-45 slugs/flux) : conserver `bce-ecb, federal-reserve, bank-of-england, bis, amf, esma, sec, financial-times, cnbc, bfm-bourse, bfm-economie, investing-fr, le-figaro, coindesk` ; **ajouter** `boj, snb, rbi, ons-uk, bls, bea, census-us, commission-ue, maison-blanche, ustr, hm-treasury, bce-taux-reference, fed-data, fsb, euronext (après correctif de date), ice, bloomberg, les-echos, wsj, nyt-business, the-economist, handelsblatt, investinglive, investing-com, the-block, decrypt, cointelegraph, marketwatch (flux `mw_bulletins` seulement)`. **Retirer** `le-monde` (économie généraliste), `bbc-news world` (garder le flux business).

**ENERGY_COMMODITIES** (cible 30-35 slugs) : conserver `oilprice, rigzone, connaissance-des-energies, conseil-ue, financial-times (rubriques energy, oil, commodities)` ; **ajouter** `eia, gie, noaa-nhc, usda, minagri-fr, edf-groupe, desnz, national-grid, world-nuclear-news, ngi, lng-prime, offshore-energy, oe-digital, carbon-pulse, carbon-brief, utility-dive, hellenic-shipping-news, gcaptain, seatrade, joc, xeneta, terre-net, la-france-agricole, reussir, bloomberg (industries, markets), les-echos (entreprises), le-monde (energies), le-figaro (matières premières), rfi, euronext, ice, the-guardian (oil, middleeast), moscow-times, tass (trust 45)`. **Retirer ou rendre optionnels** `usine-nouvelle` (429 en prod), `cnbc` (hors rubrique énergie), `al-jazeera` (garder en option « géopolitique »).

---

## C. Actifs et mots-clés

### C0. Règles du moteur qui conditionnent la liste (lues dans `signal.ts`)

- `compileTerms` : un terme est **sensible à la casse** s'il est tout en majuscules de 5 caractères maximum OU s'il contient une majuscule et fait 3 caractères maximum. Les autres termes sont comparés sans casse ni accents, **mot entier** (`(?<![a-z0-9])...(?![a-z0-9])`) sur le titre et le chapô seulement. Le trait d'union et le `+` sont conservés par la normalisation : `euro-dollar` et `euro dollar` sont deux termes distincts.
- **Pas de lemmatisation** : `baril` ne trouve pas `barils`, `réacteur` ne trouve pas `réacteurs`.
- **Pas d'exclusion par actif** : impossible de dire « Nasdaq mais pas Nasdaq-listed ». La sourdine par mot-clé existe (Réglages > Sourdine) mais elle masque l'info partout, pas par actif.
- Le libellé n'est utilisé que si l'actif n'a aucun mot-clé.

### C1. Problèmes de précision du modèle ACTUEL (TRADING, `professions.ts` L176-193)

| Terme actuel | Problème | Correction proposée |
|---|---|---|
| `Fed` (BCE/Fed) | 3 caractères avec majuscule : sensible à la casse. Les titres en capitales (« FED HOLDS RATES ») **échappent**. | Ajouter `FED` |
| `Powell` | **Périmé dans le monde du corpus** : le flux `federalreserve.gov/feeds/testimony.xml` publie « Warsh, Semiannual Monetary Policy Report to the Congress » ; `speeches.xml` : « Waller, ... ». Le président n'est plus Powell. Un terme de personne doit se maintenir. | Ajouter `Warsh`, `Waller`, `Jefferson`, `Barr` ; garder `Powell` (gouverneur éventuel). Documenter une révision trimestrielle de la liste de personnes. |
| `BCE` | En anglais, **BCE = BCE Inc. (Bell Canada, NYSE: BCE)** et « BCE » signifie aussi « Before Common Era » : faux positifs sur la presse US. | Exclusion `BCE Inc`, `Bell Canada` (nécessite le gap « exclusions », sinon ne garder que `ECB`, `Lagarde`, `Banque centrale européenne`, `European Central Bank` pour le feed anglophone) |
| `Nasdaq` | Apparaît dans **toute** introduction/lien de cotation (« Nasdaq-listed », « Nasdaq: AAPL ») : l'actif « Nasdaq » devient « toute valeur US ». | Remplacer par `Nasdaq 100`, `Nasdaq Composite`, `Nasdaq index`, `Nasdaq gains`, `Nasdaq falls` ; conserver `Nasdaq` avec exclusion `Nasdaq-listed`, `listed on Nasdaq`, `Nasdaq:` |
| `S&P 500` | `SP500` et `Standard & Poor’s 500` peu fréquents ; `Wall Street` (très fréquent) manque, titre standard des synthèses de séance. | Ajouter `Wall Street`, `S&P500`, `SPX` (symbole existe) |
| `euro dollar` / `parité euro` | « parité euro » capte « parité euro-franc » etc. ; `EURUSD`, `euro/dollar`, `euro vs dollar` manquent ; `euro-dollar` et `euro dollar` doivent coexister (déjà le cas). | Retirer `parité euro`, ajouter `EURUSD`, `euro/dollar`, `EUR-USD`, `euro vs dollar`, `euro slips against the dollar` (regex non nécessaire : `euro` + `dollar` à distance n'est pas gérable ; accepter le rappel limité) |
| `pétrole`, `baril` | `baril` ne trouve pas `barils` (« 100 dollars le baril » OK ; « 12 millions de barils » raté). `pétrole` seul est large (OK pour Brent). | Ajouter `barils`, `pétrole brut`, `prix du pétrole`, `cours du pétrole`, `Ormuz`, `Hormuz`, `OPEC+`, `OPEP+` |
| `gold`, `once d'or`, `XAU`, `prix de l’or` | `gold` seul capte « Gold Fields », « Gold Coast », « Goldman » non (mot entier) mais « gold medal » oui. Le titre FR typique « L'or atteint... » **n'est pas capté**. | Remplacer `gold` par `gold prices`, `gold price`, `spot gold`, `gold futures`, `gold rally` ; ajouter `cours de l’or`, `l’or`, `or jaune`, `argent métal`. (`l’or` normalisé `l or` : mot entier avec lookbehind, un risque résiduel avec « l'or noir » = pétrole, acceptable.) |
| `inflation` (THEME) | Mot isolé = n'importe quelle « inflation » (courses, alimentaire) : l'alerte « Inflation - source fiable » sonnera pour un article sur le panier de rentrée. | Réduire aux termes de données : `CPI`, `IPC`, `IPCH`, `HICP`, `inflation en zone euro`, `inflation américaine`, `inflation sous-jacente`, `core inflation`, `PCE`, `indice des prix à la consommation`, `consumer prices` ; `CPI` seul est ambigu en français (Cour pénale internationale) mais **l'ambiguïté n'existe que dans `signal.ts` pour les types d'événements** : ici, pour l'actif, utiliser `CPI report`, `CPI data`, `US CPI` |
| `earnings` (THEME « Résultats trimestriels ») | Fait de **chaque** publication de résultats une mention de l'actif : le pouls de la watchlist sature. | Séparer en deux actifs : « Résultats (grandes valeurs) » avec les noms de sociétés suivies + `résultats trimestriels`, et « Avertissements sur résultats » (`profit warning`, `avertissement sur résultats`, `abaisse ses objectifs`, `relève ses objectifs`) |
| `Inflation` alert (`minTrust: 70`) | Le Trust d'une dépêche non corroborée dépasse rarement 70 → règle jamais déclenchée sur un flux unique. | Passer à `minImpact: MEDIUM` + `entityLabel` précis (voir D) |
| `Pétrole - événement majeur` : `GEOPOLITICS_SANCTIONS` | `sanction` / `guerre` / `strike` sont très larges (voir F : « Trump Strikes Diesel Deal » est classé INCIDENT). | Combiner avec l'actif Brent (déjà le cas) ; corriger le lexique |

### C2. Liste par défaut proposée, TRADING (30 actifs)

Syntaxe : mots-clés exacts tels qu'ils apparaissent dans les titres ; `CAPS` = comparés sensibles à la casse (≤ 5 majuscules). « Faux positifs » = exclusions à prévoir (gap E #1).

| # | Libellé | Kind | Symbole | Mots-clés FR + EN | Faux positifs / exclusions |
|---|---|---|---|---|---|
| 1 | BCE | INSTITUTION | ECB | `ECB`, `Lagarde`, `Schnabel`, `Cipollone`, `Banque centrale européenne`, `European Central Bank`, `conseil des gouverneurs`, `Governing Council`, `BCE` | `BCE Inc`, `Bell Canada`, « BCE » = Before Common Era. Sans exclusion, préférer ne pas garder `BCE` seul sur le flux anglophone |
| 2 | Fed | INSTITUTION | FED | `Fed`, `FED`, `FOMC`, `Warsh`, `Waller`, `Powell`, `Réserve fédérale`, `Federal Reserve`, `Beige Book`, `Jackson Hole`, `dot plot`, `minutes of the FOMC`, `Fed funds` | `Fed` en minuscule hors cible (« fed up » est déjà évité par la sensibilité à la casse) ; `Federal Reserve Bank of ...` = succursales (garder) |
| 3 | Bank of England | INSTITUTION | BoE | `BoE`, `Bank of England`, `Banque d'Angleterre`, `MPC`, `Andrew Bailey` | `MPC` = sigle fréquent hors banque (Mpc, mégaparsec rarement) : le retirer si bruit |
| 4 | Banque du Japon | INSTITUTION | BoJ | `BoJ`, `BOJ`, `Bank of Japan`, `Banque du Japon`, `Ueda` | `Ueda` (nom propre fréquent, sport) : préférer `Kazuo Ueda` |
| 5 | Autres banques centrales | INSTITUTION | | `SNB`, `BNS`, `Banque nationale suisse`, `Swiss National Bank`, `PBOC`, `PBoC`, `People's Bank of China`, `Banque populaire de Chine`, `RBA`, `RBI`, `Reserve Bank of India`, `Bank of Canada`, `Banque du Canada`, `Riksbank` | `BNS` = Bank of Nova Scotia (Scotiabank, ticker BNS) : exclure `Scotiabank` ; `RBA` ambigu (RBA = parti, label) |
| 6 | CAC 40 | INDEX | CAC | `CAC 40`, `CAC40`, `Bourse de Paris`, `SBF 120`, `Euronext Paris` | `Euronext Paris` = aussi listings (bruit) ; symbole `CAC` seul = « CAC Mid 60 » |
| 7 | DAX | INDEX | DAX | `DAX`, `Dax`, `Bourse de Francfort`, `Frankfurt stocks`, `MDAX` | aucun majeur |
| 8 | Euro Stoxx 50 | INDEX | SX5E | `Euro Stoxx 50`, `EuroStoxx 50`, `Euro Stoxx`, `Stoxx 600`, `STOXX 600`, `Bourses européennes`, `European stocks`, `European shares` | aucun majeur |
| 9 | FTSE 100 | INDEX | UKX | `FTSE 100`, `FTSE`, `Footsie`, `London stocks`, `Bourse de Londres` | « Footsie » rare en FR ; `FTSE Russell` = indices (bruit léger) |
| 10 | S&P 500 | INDEX | SPX | `S&P 500`, `S&P500`, `SPX`, `SP500`, `Wall Street`, `Dow Jones`, `Standard & Poor’s 500` | `Wall Street` capte aussi « Wall Street Journal » : l'ajout est utile mais à exclure `Wall Street Journal` |
| 11 | Nasdaq 100 | INDEX | NDX | `Nasdaq 100`, `Nasdaq Composite`, `Nasdaq index`, `Nasdaq`, `valeurs technologiques américaines`, `techs américaines` | `Nasdaq-listed`, `listed on Nasdaq`, `Nasdaq:` (cotation) ; « Nasdaq » est aussi un opérateur (communiqués) |
| 12 | Nikkei et Asie | INDEX | N225 | `Nikkei`, `Nikkei 225`, `Hang Seng`, `CSI 300`, `Shanghai Composite`, `Kospi`, `Sensex`, `Nifty` | `Nikkei Asia` (média) : exclure |
| 13 | VIX | INDEX | VIX | `VIX`, `indice de volatilité`, `volatility index`, `fear gauge` | aucun majeur |
| 14 | EUR/USD | FX | EURUSD | `EUR/USD`, `EURUSD`, `EUR-USD`, `euro dollar`, `euro-dollar`, `euro/dollar`, `euro vs dollar`, `euro against the dollar`, `euro falls against`, `euro rises against` | « parité » : ne pas utiliser ; « euro » seul : exclure |
| 15 | USD/JPY | FX | USDJPY | `USD/JPY`, `USDJPY`, `dollar yen`, `dollar-yen`, `yen`, `yen japonais`, `Japanese yen`, `intervention sur le yen` | « yen » hors devise rare |
| 16 | GBP/USD | FX | GBPUSD | `GBP/USD`, `GBPUSD`, `livre sterling`, `sterling`, `cable`, `pound sterling` | `cable` = câble (télécom) : à retirer |
| 17 | Indice dollar | FX | DXY | `DXY`, `dollar index`, `indice dollar`, `US dollar index`, `billet vert`, `dollar américain` | `dollar` seul : à exclure (australien, canadien) |
| 18 | Franc suisse | FX | USDCHF | `USD/CHF`, `EUR/CHF`, `franc suisse`, `Swiss franc` | |
| 19 | Treasuries US 10 ans | RATE | US10Y | `Treasury yields`, `Treasuries`, `10-year yield`, `10-year Treasury`, `US10Y`, `rendement à 10 ans`, `taux à 10 ans américain`, `T-bond`, `Treasury yield` | **`Treasury` seul = US Treasury Department/Bessent** : ne pas utiliser ; `10-year` suffit rarement |
| 20 | Taux européens (Bund, OAT, spreads) | RATE | | `Bund`, `Bunds`, `OAT`, `BTP`, `spread OAT-Bund`, `spread France Allemagne`, `dette française`, `French yields`, `taux d'emprunt`, `taux de la France` | `OAT` = oats ; `BUND` (ONG allemande) rares ; `BTP` = bâtiment et travaux publics en français : **fort faux positif** (« secteur du BTP ») : utiliser `BTP italiens`, `Italian BTP`, `spread BTP-Bund` |
| 21 | Gilts et JGB | RATE | | `gilts`, `gilt yields`, `30-year gilt`, `JGB`, `Japanese government bonds`, `rendements japonais` | « gilts » = boucle de bijouterie rare |
| 22 | Brent / WTI | COMMODITY | BRN | `Brent`, `WTI`, `West Texas Intermediate`, `pétrole`, `pétrole brut`, `prix du pétrole`, `cours du pétrole`, `baril`, `barils`, `crude oil`, `oil prices`, `OPEC+`, `OPEP+`, `Ormuz`, `Hormuz` | `Brent` = aussi « Brent Council » (UK) et `Brent geese` : exclure `Brent Council`, `Brentford` (mot entier : exclu par construction) |
| 23 | Gaz naturel | COMMODITY | TTF | `TTF`, `Henry Hub`, `gaz naturel`, `natural gas`, `prix du gaz`, `gaz européen`, `European gas`, `GNL`, `LNG` | `TTF` = aussi « Taxe » rarement ; `LNG` = Lingotto ? faible |
| 24 | Or | COMMODITY | XAU | `XAU`, `gold prices`, `gold price`, `spot gold`, `gold futures`, `prix de l’or`, `cours de l’or`, `once d’or`, `l’or` | « Gold Fields », « Gold Coast », médailles d'or (`l’or` en sport : `médaille d’or` à exclure) |
| 25 | Cuivre et métaux | COMMODITY | HG | `cuivre`, `copper`, `prix du cuivre`, `LME`, `Comex`, `aluminium`, `nickel`, `minerai de fer`, `iron ore`, `lithium` | `LME` = aussi « Lme » rare |
| 26 | Bitcoin | CRYPTO | BTC | `Bitcoin`, `bitcoin`, `BTC`, `spot ETF`, `bitcoin ETF`, `Strategy`, `MicroStrategy` | `Strategy` (mot courant) : exclure |
| 27 | Ethereum | CRYPTO | ETH | `Ethereum`, `ether`, `Ether`, `ETH`, `Solana`, `XRP`, `stablecoin`, `stablecoins`, `USDT`, `USDC` | **`ETH` = ETH Zurich** ; `ether` = produit chimique : exclure `ETH Zurich` |
| 28 | Régulation crypto | THEME | | `MiCA`, `crypto-actifs`, `cryptomonnaies`, `cryptocurrency`, `Coinbase`, `Binance`, `Kraken`, `ETF crypto`, `crypto ETF` | `Kraken` (monstre marin) rare |
| 29 | Valeurs phares CAC 40 (panier) | EQUITY | | `LVMH`, `TotalEnergies`, `Airbus`, `BNP Paribas`, `Société Générale`, `Sanofi`, `Schneider Electric`, `Hermès`, `L’Oréal`, `Air Liquide`, `Stellantis`, `Safran`, `AXA`, `Crédit Agricole`, `Vinci`, `Michelin`, `Kering`, `Capgemini`, `Danone`, `Orange` | **`Orange`** (couleur, vigilance orange, jus d'orange, Orange County) ; **`Vinci`** (Leonardo da Vinci) ; **`Total`** seul ; **`Carrefour`** (nom commun) ; **`Renault`** OK ; `Hermès` OK ; recommandation : un actif par société suivie plutôt qu'un panier |
| 30 | Mégacaps US et IA | EQUITY | | `Nvidia`, `Apple`, `Microsoft`, `Alphabet`, `Google`, `Amazon`, `Meta Platforms`, `Tesla`, `Broadcom`, `TSMC`, `ASML`, `AMD`, `OpenAI` | **`Meta`** (mot « meta ») : utiliser `Meta Platforms` ; `Amazon` (forêt amazonienne) : exclure `Amazon rainforest`, `Amazonie` (mot différent) ; `Apple` (fruit) ; `Intel` (« intel » = renseignement) ; `Shell` (société fictive, coquille) |

Thèmes à ajouter en complément (kind THEME, 8 de plus) : « Emploi US » (`nonfarm payrolls`, `NFP`, `payrolls`, `jobless claims`, `taux de chômage`), « Inflation données » (`CPI report`, `CPI data`, `IPCH`, `HICP`, `core inflation`, `PCE`), « PMI et enquêtes » (`PMI`, `ISM`, `Ifo`, `ZEW`), « PIB » (`PIB`, `GDP`, `produit intérieur brut`), « Droits de douane » (`droits de douane`, `tariffs`, `Section 232`, `USTR`), « Moyen-Orient et Ormuz » (`Ormuz`, `Hormuz`, `Houthis`, `Iran`, `Riyad`, `mer Rouge`), « OPA et M&A » (`OPA`, `OPE`, `offre publique`, `takeover bid`, `rachat`, `buyout`), « Sanctions Russie » (`sanctions contre la Russie`, `plafonnement du prix`, `flotte fantôme`, `shadow fleet`).

---

### C3. Liste par défaut, ÉNERGIE & MATIÈRES PREMIÈRES (28 actifs)

Problèmes du modèle actuel (`professions.ts` L379-387) :
- `ASN` : l'autorité s'appelle **ASNR** depuis janvier 2025 (fusion ASN + IRSN ; le flux est `asnr.fr`). `ASN` (3 majuscules) ne trouvera plus rien.
- `OPEP+`, `OPEC+` : sensibles à la casse (5 caractères majuscules) ; la presse écrit **« Opec+ » et « Opep+ »** (FT, Bloomberg, Le Monde) : **ratés**. Ajouter `Opec`, `Opep`, `Opec+`, `Opep+` (termes de 4-5 caractères non tout-majuscules : comparés sans casse).
- `réacteurs`, `canicule`, `tempête`, `ouragan`, `sécheresse` : formes plurielles absentes (`réacteur`, `canicules`, `tempêtes`, `ouragans`...).
- `électricité` seul : capte tout (factures, fournisseurs). `nucléaire` : capte le nucléaire iranien (armes), fréquent dans le corpus actuel (guerre Iran-Etats-Unis).
- `blé` : le terme normalisé `ble` ne capte pas `blés`.
- `Euronext blé` : jamais écrit ainsi ; le contrat s'appelle « blé tendre Euronext » ou « Matif ».
- `mer du Nord` (Brent) : capte aussi pêche, éolien offshore ; peu de valeur.

| # | Libellé | Kind | Symbole | Mots-clés FR + EN | Faux positifs |
|---|---|---|---|---|---|
| 1 | Brent | COMMODITY | BRN | `Brent`, `baril de Brent`, `Brent crude`, `Brent futures`, `pétrole`, `pétrole brut`, `prix du pétrole`, `barils`, `baril`, `crude oil`, `oil prices` | `Brent Council` ; « pétrole » capte « chimie » (rare) |
| 2 | WTI | COMMODITY | CL | `WTI`, `West Texas Intermediate`, `pétrole américain`, `US crude`, `Cushing`, `stocks de brut américains`, `crude inventories`, `crude stockpiles` | |
| 3 | Gaz TTF (Europe) | COMMODITY | TTF | `TTF`, `gaz européen`, `European gas`, `prix du gaz`, `gaz naturel`, `natural gas`, `stockage de gaz`, `gas storage`, `taux de remplissage`, `AGSI` | `TTF` rare ailleurs |
| 4 | Gaz Henry Hub (US) | COMMODITY | NG | `Henry Hub`, `US natural gas`, `gaz américain`, `Golden Pass`, `Sabine Pass`, `Cameron LNG` | |
| 5 | GNL | COMMODITY | | `GNL`, `LNG`, `LNG carriers`, `méthaniers`, `Qatar LNG`, `terminal GNL`, `LNG terminal`, `regazéification` | `LNG` = Lingotto (rare) |
| 6 | Électricité (prix de gros) | COMMODITY | | `prix de l’électricité`, `marché de gros de l’électricité`, `prix spot`, `power prices`, `electricity prices`, `day-ahead`, `EPEX`, `baseload`, `prix négatifs`, `negative power prices` | `électricité` seul exclu ; `prix spot` parfois hors énergie (change) |
| 7 | Réseau et pannes | THEME | | `RTE`, `Enedis`, `blackout`, `panne de courant`, `coupure d’électricité`, `power outage`, `grid`, `réseau électrique`, `délestage` | `RTE` = Radio Télévision d'Irlande (RTÉ) : exclure `RTÉ` ; `grid` large |
| 8 | EDF et nucléaire | COMPANY | EDF | `EDF`, `réacteur`, `réacteurs`, `parc nucléaire`, `arrêt de réacteur`, `nuclear output`, `Flamanville`, `ASNR`, `ASN`, `disponibilité nucléaire` | **`nucléaire` seul exclu** (Iran/armes) ; `EDF` OK ; `ASN` (ASN Bank, ASN) |
| 9 | OPEP+ | INSTITUTION | | `OPEP`, `OPEP+`, `Opep`, `Opep+`, `OPEC`, `OPEC+`, `Opec`, `Opec+`, `quotas de production`, `production cuts`, `output hike`, `hausse de production` | |
| 10 | Détroit d'Ormuz et Golfe | COUNTRY | | `Ormuz`, `Hormuz`, `Strait of Hormuz`, `détroit d’Ormuz`, `Golfe Persique`, `Persian Gulf`, `Houthis`, `mer Rouge`, `Red Sea`, `Bab el-Mandeb` | `Golfe` = aussi sport/golf |
| 11 | Sanctions Russie / plafonnement | THEME | | `plafonnement du prix`, `price cap`, `flotte fantôme`, `shadow fleet`, `diesel russe`, `Russian diesel`, `Nord Stream`, `Gazprom`, `Rosneft`, `Lukoil` | `Rosneft`, `Lukoil` OK ; `price cap` hors énergie (assurance) |
| 12 | Iran et Moyen-Orient | COUNTRY | | `Iran`, `iranien`, `Aramco`, `Saudi Aramco`, `Arabie saoudite`, `Saudi Arabia`, `Riyad`, `Riyadh` | `Iran` capte tout le corpus géopolitique : à croiser avec un type d'événement |
| 13 | Raffinage et produits | COMMODITY | | `diesel`, `gazole`, `jet fuel`, `kérosène`, `raffinerie`, `raffineries`, `refinery`, `refineries`, `marges de raffinage`, `crack spread`, `pénurie de carburant` | `diesel` (voiture, Dieselgate) : exclure `Dieselgate` |
| 14 | Stocks et forages | THEME | | `EIA`, `American Petroleum Institute`, `Strategic Petroleum Reserve`, `SPR`, `réserves stratégiques`, `Baker Hughes`, `rig count`, `nombre de forages` | **`API`** (3 majuscules) : API logicielle, ne pas utiliser seul ; `SPR` ambigu |
| 15 | Charbon | COMMODITY | | `charbon`, `coal`, `Newcastle`, `API2`, `coking coal`, `charbon à coke` | `Newcastle` (ville anglaise, football) : `Newcastle coal` |
| 16 | Carbone (EU ETS) | COMMODITY | EUA | `EU ETS`, `quotas carbone`, `carbon price`, `prix du carbone`, `EUA`, `CBAM`, `carbon allowances` | `EUA` rare |
| 17 | Cuivre | COMMODITY | HG | `cuivre`, `copper`, `prix du cuivre`, `LME`, `Comex`, `Dr Copper` | |
| 18 | Aluminium, nickel, zinc | COMMODITY | | `aluminium`, `aluminum`, `nickel`, `zinc`, `étain`, `tin prices`, `LME` | `tin` : utiliser `tin prices` ; `étain` rare |
| 19 | Lithium et métaux de la batterie | COMMODITY | | `lithium`, `cobalt`, `terres rares`, `rare earths`, `graphite`, `Albemarle`, `CATL` | |
| 20 | Minerai de fer et acier | COMMODITY | | `minerai de fer`, `iron ore`, `acier`, `steel prices`, `ArcelorMittal`, `Vale`, `Rio Tinto`, `BHP` | `Vale` (« vale » en espagnol/latin) : `Vale SA` |
| 21 | Or et argent | COMMODITY | XAU | `XAU`, `XAG`, `gold prices`, `silver prices`, `prix de l’or`, `cours de l’or`, `once d’or`, `l’or`, `argent métal`, `platinum`, `palladium` | `argent` seul = money : n'utiliser que `argent métal`, `prix de l’argent` |
| 22 | Blé et céréales | COMMODITY | | `blé`, `blé tendre`, `Matif`, `wheat`, `Chicago wheat`, `céréales`, `grain exports`, `corridor céréalier`, `Black Sea grain`, `mer Noire` | `mer Noire` (hors grain) |
| 23 | Maïs, soja, colza | COMMODITY | | `maïs`, `corn`, `soja`, `soybeans`, `colza`, `rapeseed`, `canola`, `tournesol`, `huile de palme`, `palm oil` | `corn` = `Cornwall` (mot différent) ; « corn » dans « acorn » exclu (mot entier) |
| 24 | Sucre, café, cacao | COMMODITY | | `cacao`, `cocoa`, `café arabica`, `arabica`, `sucre`, `sugar prices`, `Côte d’Ivoire cacao` | `sucre` seul : « sucre » (aliment) → `sugar prices`, `prix du sucre` |
| 25 | Engrais | COMMODITY | | `engrais`, `fertilizer`, `fertiliser`, `urée`, `ammoniac`, `potasse`, `Yara`, `Nutrien` | |
| 26 | Fret et maritime | THEME | | `Baltic Dry`, `BDI`, `Drewry`, `fret maritime`, `freight rates`, `pétrolier`, `pétroliers`, `tanker`, `tankers`, `VLCC`, `canal de Suez`, `Suez Canal`, `canal de Panama`, `Panama Canal`, `taux de fret` | `Drewry`, `Baltic` OK ; `BDI` ambigu (BDI = Bund der Industrie) rare |
| 27 | Météo et hydrologie | THEME | | `canicule`, `canicules`, `vague de froid`, `cold snap`, `heatwave`, `sécheresse`, `drought`, `tempête`, `tempêtes`, `ouragan`, `ouragans`, `hurricane`, `cyclone`, `typhon`, `El Niño`, `La Niña`, `niveau du Rhin`, `Rhine water levels`, `hydraulicité`, `NHC` | « tempête » capte « tempête dans un verre d'eau » ; `NHC` = National Hurricane Center (OK) |
| 28 | Renouvelables et PPA | THEME | | `éolien`, `wind farm`, `offshore wind`, `solaire`, `photovoltaïque`, `PPA`, `hydrogène`, `stockage batterie`, `energy storage`, `appel d’offres CRE` | `PPA` = autre sens (Paycheck Protection) |

---

## D. Modèles d'alertes et mise en page

Contraintes lues : une règle = `name`, `entityLabel?`, `keywords[]`, `eventTypes[]`, `minImpact?`, `minTrust?`. Sans plancher explicite, impact au moins moderé (doc, « Alertes »). Pas de condition de rumeur, de source primaire, de type de source ou de chiffre.

### D1. Règles TRADING (12)

| # | Nom | Actif / mots-clés | Types | Impact / Trust min | Remarque |
|---|---|---|---|---|---|
| 1 | Banque centrale : source officielle | actif BCE ou Fed ou BoE ou BoJ | CENTRAL_BANK | trust ≥ 90 (flux officiel), impact ≥ MEDIUM | l'actuelle règle « impact fort » ne sonnera presque jamais : une décision de taux est un communiqué sobre sans « choc » ni « surprise » (INTENSITY). Exiger plutôt `trust ≥ 90` que `impact HIGH` |
| 2 | Fed : minutes, discours, FOMC | actif Fed | CENTRAL_BANK, MACRO | impact ≥ MEDIUM | |
| 3 | BCE : décision et discours | actif BCE | CENTRAL_BANK | impact ≥ MEDIUM | |
| 4 | Données macro US de la matinée | mots-clés `nonfarm payrolls`, `NFP`, `CPI report`, `PCE`, `jobless claims` | MACRO | impact ≥ MEDIUM, trust ≥ 80 | remplace « Inflation - source fiable » (trop large) |
| 5 | Mouvement EUR/USD | actif EUR/USD | MACRO, CENTRAL_BANK, MARKETS | impact ≥ MEDIUM | existante, à garder |
| 6 | Avertissement sur résultats | mots-clés `profit warning`, `avertissement sur résultats`, `abaisse ses objectifs`, `guidance cut` | EARNINGS | impact ≥ MEDIUM | une règle par panier de valeurs suivies |
| 7 | OPA, fusion ou rumeur de rachat | mots-clés `OPA`, `OPE`, `offre publique`, `takeover bid`, `rachat` ou actif | M_AND_A | trust ≥ 70 (les rumeurs passeront sous 60 : voir gap rumeur) | |
| 8 | Dégradation de note souveraine ou bancaire | actif ou mots-clés `France`, `Italie`, `États-Unis`, `Moody’s`, `Fitch`, `S&P Global Ratings` | RATINGS | trust ≥ 80 | attention au faux positif « dégrade » du lexique (voir F) |
| 9 | Brent : événement majeur | actif Brent / WTI | ENERGY_COMMODITIES, GEOPOLITICS_SANCTIONS | impact ≥ HIGH | existante |
| 10 | Droits de douane et sanctions | mots-clés `droits de douane`, `tariffs`, `sanctions` | GEOPOLITICS_SANCTIONS | impact ≥ HIGH, trust ≥ 70 | |
| 11 | Régulateur : action sur une valeur suivie | actif de la watchlist | REGULATION_LEGAL | trust ≥ 90 (AMF/ESMA/SEC) | |
| 12 | Crypto : ETF, régulation, piratage | actif Bitcoin ou Ethereum + mots-clés `hack`, `exploit`, `ETF`, `MiCA` | REGULATION_LEGAL, INCIDENT, MARKETS | impact ≥ MEDIUM | `hack`/`exploit` absents du lexique INCIDENT (voir F) |

### D2. Règles ÉNERGIE (10)

| # | Nom | Actif / mots-clés | Types | Seuil |
|---|---|---|---|---|
| 1 | OPEP+ : décision | actif OPEP+ | ENERGY_COMMODITIES, GEOPOLITICS_SANCTIONS | aucun plancher (l'existante est bonne) |
| 2 | Ormuz / mer Rouge : incident de navigation | actif Détroit d'Ormuz et Golfe | INCIDENT, GEOPOLITICS_SANCTIONS | impact ≥ MEDIUM |
| 3 | Brent / WTI : événement majeur | actif Brent ou WTI | ENERGY_COMMODITIES, GEOPOLITICS_SANCTIONS | impact ≥ HIGH |
| 4 | Gaz TTF : source fiable | actif Gaz TTF | ENERGY_COMMODITIES, INCIDENT | trust ≥ 60 (existante ; passer à `impact ≥ MEDIUM`) |
| 5 | Stockage de gaz et GNL | actifs Gaz TTF + GNL | ENERGY_COMMODITIES | impact ≥ MEDIUM |
| 6 | Arrêt de réacteur ou panne réseau | actif EDF et nucléaire ou Réseau et pannes | INCIDENT | impact ≥ MEDIUM |
| 7 | Stocks de brut EIA | mots-clés `crude inventories`, `stocks de brut`, `EIA` | ENERGY_COMMODITIES, MACRO | trust ≥ 80 |
| 8 | Météo extrême sur zone de production | actif Météo et hydrologie | INCIDENT | impact ≥ MEDIUM |
| 9 | Fret : chokepoint ou grève portuaire | actif Fret et maritime | INCIDENT, GEOPOLITICS_SANCTIONS | impact ≥ MEDIUM |
| 10 | Sanctions et plafonnement de prix | actif Sanctions Russie | GEOPOLITICS_SANCTIONS | trust ≥ 80 |

### D3. Disposition des widgets (types existants uniquement)

**TRADING, mise en page pré-ouverture 7 h 30** (largeurs sur 12 colonnes ; `config` supporte `scope`, `entityIds`, `eventTypes`, `minTrust`, `minImpact`, `hours`) :
1. `BRIEFING` 4x2 : en tête à gauche (c'est la première chose lue).
2. `ALERTS` 4x2 : à droite du brief.
3. `LIVE_WIRE` 4x2 « Banques centrales et macro » (`eventTypes: CENTRAL_BANK, MACRO`, `minTrust: 80`, `hours: 24`).
4. `LIVE_WIRE` 8x2 « Fil de mes actifs » (`scope: watchlist`, `minImpact: MEDIUM`).
5. `WATCHLIST_PULSE` 6x2.
6. `RUMOR_RADAR` 6x2.
7. `LIVE_WIRE` 6x1 « Résultats et M&A » (`eventTypes: EARNINGS, M_AND_A`, `hours: 12`).
8. `LIVE_WIRE` 6x1 « Matières premières et géopolitique » (`eventTypes: ENERGY_COMMODITIES, GEOPOLITICS_SANCTIONS`, `minImpact: MEDIUM`).
9. `EVENT_MIX` 4x1, `DECISION_JOURNAL` 4x2, `SOURCE_HEALTH` 4x1 : en bas (non lus à 7 h 30).
(Si le mode édition n'autorise pas plusieurs `LIVE_WIRE` de config distincte, c'est le gap E #6.)

**ÉNERGIE** : `WATCHLIST_PULSE` 8x2 + `ALERTS` 4x2 ; `BRIEFING` 4x2 ; `LIVE_WIRE` 8x2 `scope: all` filtré `eventTypes: ENERGY_COMMODITIES, INCIDENT, GEOPOLITICS_SANCTIONS` ; `EVENT_MIX` 6x1 (déjà filtré) ; `RUMOR_RADAR` 6x2 ; `LIVE_WIRE` « Sources primaires » (`minTrust: 90`, `hours: 48` : EIA, NHC, GIE, EDF, ASNR) ; `DECISION_JOURNAL` ; `SOURCE_HEALTH`.

---

## E. Manques fonctionnels

Légende faisabilité : **RSS** = faisable avec flux publics, sans IA, sans données de marché sous licence.

| # | Besoin | État actuel (réf.) | Proposition | Prio | Faisabilité |
|---|---|---|---|---|---|
| 1 | **Mots-clés d'exclusion par actif** (« Nasdaq mais pas Nasdaq-listed », « BCE sauf BCE Inc », « Brent sauf Brent Council », « Orange sauf vigilance orange ») | `compileTerms` (`signal.ts` L329-348) sans exclusion ; la sourdine par mot-clé (Réglages > Sourdine) est globale | Champ `excludeKeywords` par actif et par règle d'alerte ; testé après le match positif, mêmes règles de casse | P0 | RSS : oui, déterministe |
| 2 | **Casse et variantes** : `Opec+`, `FED`, `Opep`, pluriels (`barils`, `réacteurs`) | acronymes ≤5 majuscules sensibles à la casse ; pas de lemmatisation (`signal.ts` L329-348) | (a) un terme tout en majuscules de 4-5 lettres devient insensible à la casse sauf liste de collisions connues (`Fed`, `Or`, `UN`, `EPS`...) ; (b) option `s` final optionnel ; (c) saisie aide : « ajouter aussi la forme `Opec+` » | P0 | oui |
| 3 | **Règles d'alerte plus riches** : rumeur seule, source primaire uniquement, un seul déclenchement par histoire, accélération des mentions (×2 déjà affichée en widget), seuil sur un chiffre (« pb ≥ 25 »), plage horaire (07:00-22:00), silence, une règle muette hors séance | `AlertRuleTemplate` : entité, mots-clés, types, trust, impact seulement ; docs : « Sans plancher explicite, impact modéré » | Conditions optionnelles : `rumorOnly`, `primaryOnly` (catégorie INSTITUTIONS), `perStory: once`, `velocityGte`, `figureMinBps`, `quietHours`, `snoozeMinutes` | P0 pour `perStory` et `primaryOnly` ; P1 le reste | oui (champs déjà calculés : rumeur, trust, corroboration, chiffres) |
| 4 | **Exactitude des horodatages** (le métier vit de l'heure) | `rssIngestionService.ts` L33 : `new Date(isoDate)` sinon `new Date(pubDate)` sinon `null` | (a) ESMA, Conseil UE, FCA : **aucune date lisible** -> `publishedAt = null` : afficher « date inconnue », ne pas la classer comme récente ; (b) **Euronext `dd/mm/yyyy`** (« 07/10/2026 ») lu à l'américain (mois/jour inversés) ; (c) **EIA Today in Energy** : « Fri, 09 Oct  09:00:00 EST » sans année -> **2001** ; (d) **Investing FR** : `pubDate` « 2026-10-10 11:11:01 » **sans fuseau** : lu en heure du serveur ; si le conteneur est en UTC, les items paraissent 1-2 h dans le futur [INFERENCE : le fuseau du conteneur prod n'a pas été observé] ; (e) flux avec dates futures (BoC, BoE `events`, Euronext). Parseur par éditeur (format + fuseau déclarés dans le catalogue) et plafonnement des dates futures à `capturedAt` | P0 | oui, purement technique |
| 5 | **Agenda économique** (le trader ouvre d'abord le calendrier) | « Calendrier économique non livré : sans source fiable... » (`centre-operations.md` L52) | Ne pas inventer de dates : (a) **calendriers officiels préannoncés** (réunions BCE/Fed/BoE/BoJ, publi BLS/BEA/Census, ONS `releasecalendar?rss` vérifié) en tant qu'événements « à venir » sans valeur prévisionnelle ; (b) une rubrique « Aujourd'hui » dans `/brief` fondée sur ces calendriers ; (c) distinguer « publié » de « attendu » | P1 | RSS pour ONS et dérivés de flux officiels ; les dates BCE/Fed se saisissent à la main chaque année dans un fichier source (ne dépend d'aucun fournisseur). Pas de consensus/estimations (licence) |
| 6 | **Plusieurs widgets du même type avec filtres distincts** et vues enregistrées | `WIDGET_TYPES` ; mode édition (« ajout, ordre, largeur, hauteur, filtres ») ; filtres rapides dans l'URL | Vues nommées de fil (« Matin banques centrales », « Résultats »), épinglables sur le tableau de bord et partageables par lien | P1 | oui |
| 7 | **Livraison hors page** : e-mail pré-ouverture à 7 h 00, webhook Slack/Teams/Discord, Telegram | `centre-operations.md` : son, notifications de bureau, compteur d'onglet ; « Mobile hors périmètre » | Digest planifié du `/brief` (HTML + texte) et webhooks par règle d'alerte ; fuseau utilisateur | P1 | oui (SMTP/webhook sortants ; pas d'IA) |
| 8 | **Brief « depuis ma dernière session »** et brief Asie | `/brief` : « depuis la clôture » (Euronext 17 h 30), 12 h, 24 h | Ajouter « depuis ma dernière visite » (séparateur « nouvelles depuis » existe dans `/flux`) ; sections par région ; séances Tokyo, Hong Kong, Sydney en plus de Paris/Francfort/Londres/NYSE | P1 | oui |
| 9 | **Sourcing primaire visible** | trust par éditeur dans le catalogue ; catégories INSTITUTIONS... | Badge « source primaire », « première dépêche » déjà donnée ; filtre « officiel seulement » et « repris par ≥ 2 éditeurs » ; temps de latence médian par éditeur | P1 | oui |
| 10 | **Watchlist riche** : groupes/paniers, étiquettes, tri, favori épinglé, import avec ISIN/ticker, actifs liés (spread France-Allemagne = deux actifs), règle par groupe | liste d'actifs + import en masse ; un seul niveau | Dossiers/étiquettes (« Banques », « Luxe », « Défense »), suggestion de mots-clés à la création (annuaire interne FR/EN des sociétés CAC 40 / Stoxx 50 / S&P 100, maintenu dans le dépôt) ; aide à la saisie des pièges (Orange, Meta, Apple, BTP) | P1 | oui (fichier d'annuaire statique) |
| 11 | **Sources par actif** | sources par tableau de bord seulement | Flux par actif ciblé (« pour le gaz : EIA + GIE + LNG Prime ») ; génération de flux à partir d'un ticker (Yahoo `headline?s=`) ou d'un mot-clé (Google Actualités) : agrégateurs, trust 58-62, à marquer | P2 | RSS : oui mais CGU Google/Yahoo à vérifier : risque juridique |
| 12 | **Chiffres clés avec unités métiers** | `FIGURE_PATTERNS` (`signal.ts` L456-465) : %, pb, montants avec devise | Ajouter : `$/baril`, `$/bbl`, `€/MWh`, `$/MMBtu`, `GW`, `TWh`, `bcm`, `Mb/j`, `kb/d`, `$/t`, points d'indice, niveaux de taux (« 3,88 % » existe), cours (« 1,1206 USD ») | P1 | oui, regex |
| 13 | **Taux de référence officiels sans licence** (équivalent de cotations) | « Aucune cotation » (`centre-operations.md` L51) | Widget « Références » alimenté par flux officiels : BCE `fxref-*` (EUR/USD, GBP, JPY, CHF), Fed H.10/H.15 (fed funds 3,88, indice dollar), SNB `exchangeRates` ; pas de tick, une valeur par jour | P2 | RSS vérifié ; réutilisation : BCE publie ses taux de référence sous conditions d'attribution, Fed domaine public [INFERENCE : confirmer les mentions légales avant intégration] |
| 14 | **Exports et audit** | journal CSV exportable (`;`, BOM) ; copier le brief ; copier pour chat | Export du fil filtré (CSV/JSON), export des alertes déclenchées avec horodatage de capture, lien partageable en lecture seule | P1 | oui |
| 15 | **Partage et équipe** (une licence par siège, mais un desk partage ses règles) | tableaux de bord personnels ; sources privées par auteur (`Publisher.ownerUserId`) | Tableau de bord partagé en lecture, bibliothèque de règles d'équipe, journal consolidé | P2 | oui (modèle de données) |
| 16 | **Panneau d'état des sources en prod** | `SOURCE_HEALTH`, `lastError` dans `/api/channels` | Alerter le propriétaire quand une source d'un tableau de bord tombe (403/429) ; suggérer un flux de secours (AMF 21 -> 30) ; backoff déjà géré | P1 | oui |
| 17 | **Étiquette de média d'État / partisan** | trust par éditeur seulement | Colonne « nature » (État, indépendant, agrégateur, entreprise) pour TASS/Global Times/Tehran Times ; plafond de trust | P1 | oui |
| 18 | **Rafraîchir le catalogue sans redéploiement** | `ensureProSourceCatalog` crée au démarrage (`proSourceCatalog.ts`) | Rien d'existant n'est modifié : les corrections de flux (AMF 21 -> 30) ne se propagent pas. Prévoir une migration de flux par slug | P1 | oui |
| 19 | **Conformité AMF du contenu ingéré** | `Syntheza informe, ne recommande jamais` | Certains flux (Nasdaq.com/Zacks : « Which Chip Stock Is a Better Buy », « Is It Time to Sell? ») portent des formulations de conseil : filtre de ton « conseil » sur le titre (déterministe : `better buy`, `buy now`, `time to sell`, `à acheter`) ou rejet du flux | P1 | oui |
| 20 | **Performance au pré-ouverture** | 15 s pour flux chauds, 10 min froids (`centre-operations.md`) | Prioriser par tableau de bord actif ; le nombre de flux grimperait (~80+) : dimensionner la file de relevé | P1 | oui |

Gaps spécifiques ÉNERGIE (en plus des précédents) :

| # | Besoin | État | Proposition | Prio | Faisabilité |
|---|---|---|---|---|---|
| E1 | **Sources primaires électricité** (prix spot EPEX, indisponibilités RTE/ENTSO-E, REMIT) | aucune : RTE, CRE, ENTSO-E, ACER, EPEX n'ont pas de flux public valide (testé) | Reste aveugle sur l'électricité : s'appuyer sur la presse (Le Monde Énergies, Les Echos, Utility Dive) et sur EDF (communiqués) ; documenter honnêtement la limite | P0 (information produit) | RSS : impossible pour les données ; pas de licence |
| E2 | **Données de stocks hebdomadaires** (EIA mercredi, API, GIE AGSI) | EIA publie des communiqués et « Today in Energy » (verifiés), pas les chiffres hebdo en flux | Détecter les titres de publication (« EIA expects... », « crude inventories ») ; ne pas annoncer de chiffres que le flux ne porte pas | P1 | partielle |
| E3 | **Alerte météo/ouragan** liée à la production | NHC (verifié) disponible ; type d'événement INCIDENT couvre `ouragan|hurricane` seulement | Source NHC + lexique météo étendu + lien actif « Golfe du Mexique » | P1 | oui |
| E4 | **Unités et conversions** | aucune | Voir E #12 | P1 | oui |
| E5 | **Calendrier OPEP+ / IEA / WASDE / EIA** | aucun | Dates préannoncées publiées par les organismes, saisies au fichier, étiquetées « attendu » | P2 | manuel annuel |

---

## F. Types d'événements et lexiques manquants ou imprécis

Types actuels (`opsTypes.ts` L28-40) : CENTRAL_BANK, MACRO, EARNINGS, M_AND_A, RATINGS, GEOPOLITICS_SANCTIONS, ENERGY_COMMODITIES, REGULATION_LEGAL, DEFAULT_RESTRUCTURING, INCIDENT, MARKETS.

### F1. Faux positifs constatés dans `EVENT_LEXICON` (`signal.ts` L92-182) sur des titres réels du flux du 10/10/2026

| Lexique | Terme | Titre réel qui le déclenche | Effet | Correction |
|---|---|---|---|---|
| INCIDENT | `strikes?` | « Trump **Strikes** Diesel Deal With Putin » (BBC/Guardian), « China, EU **strike** deal to cut Chinese hybrid vehicle exports » (Investing) | classé INCIDENT (poids 25), impact relevé | Exclure `strike(s)? (a )?(deal|agreement|accord)`, ou ne garder `strike` qu'avec `workers|port|refinery|union|grève` à distance |
| M_AND_A | `rachat\\w*` | « rachat d'actions » (buyback), « rachat de crédit » | classé M&A | Séparer `rachat d actions|share buyback` (type EARNINGS/CAPITAL_RETURN) ; ne garder `rachat` qu'avec `de la société|d’un concurrent|offre` |
| M_AND_A | `stake` | « what's at **stake** » | faux positif | `stake in|stake of|takes? a \\d+% stake` |
| M_AND_A | `fusion\\w*` | « fusion nucléaire » (énergie) | M&A sur du nucléaire | `fusion de|fusion avec|fusion-acquisition` |
| M_AND_A | `acquisition\\w*` | « acquisition de données » | faux positif | `acquisition (de|d’|of)` suivi d'un nom propre : non faisable par regex simple ; accepter ou pondérer |
| RATINGS | `degrade\\w*`, `degradation\\w*`, `downgrade\\w*`, `rehausse\\w*` | « mode dégradé », dégradation d'un produit, **rehaussement de prévisions de croissance**, « downgrade » d'**analyste** | confond note de crédit et recommandation d'analyste | Exiger un voisinage `note|rating|notation|perspective|Moody|Fitch|S&P` ; créer un type distinct `ANALYST_ACTION` (voir F2) |
| MARKETS | `treasur(?:y|ies)` | « **Treasury** Secretary Bessent... » (Département du Trésor) | classé MARKETS | `treasury (yields?|bonds?|notes?|bills?)|treasuries` |
| ENERGY_COMMODITIES | `nucleaire|nuclear` | « programme **nucléaire** iranien », « nuclear weapons » (corpus actuel : guerre Iran-États-Unis) | classé énergie | Exclure `iranien|arme(s)? nucleaire|nuclear (weapons?|talks|deal)` ; garder `reacteur|centrale nucleaire|nuclear (power|output|reactor)` |
| ENERGY_COMMODITIES | `pipelines?` | « data pipeline », « sales pipeline » | faux positif tech | `oil pipeline|gas pipeline|gazoduc|oleoduc` |
| ENERGY_COMMODITIES | `commodit\\w*` | « commodité » (FR) | rare | |
| GEOPOLITICS_SANCTIONS | `guerre|war` | « guerre des prix », « price war » | faux positif commerce | pondération faible |
| CENTRAL_BANK | `powell` | `Powell` inexistant comme président en 2026 (Warsh), mais mention de « Powell » gouverneur | information obsolète | voir C1 |
| MARKETS | `dax`, `ftse`, `nikkei` | « Dax » fréquent ; sans souci | | |

### F2. Types / catégories à ajouter (avec titres types et termes FR/EN)

| Type proposé | Pourquoi | Exemples de titres | Termes FR | Termes EN |
|---|---|---|---|---|
| `SUPPLY_DISRUPTION` (offre : arrêt non planifié, force majeure, grève de raffinerie, coupure de pipeline) | cœur du métier énergie ; INCIDENT ne connaît ni `force majeure` ni `shut-in` | « Oil and gas production disrupted as Hurricane Isaias hits US » (FT, 10/10), « Panama Canal unaffected by huge earthquake » (Splash), « Iran threatens wider tanker attacks as VLCC struck in Gulf » (Seatrade) | `force majeure`, `arrêt non planifié`, `arrêt de production`, `interruption de production`, `coupure de gazoduc`, `grève dans les raffineries`, `fermeture du détroit`, `blocus` | `force majeure`, `unplanned outage`, `shut-in`, `production halted`, `curtailment`, `refinery fire`, `pipeline rupture`, `tanker attack`, `vessel struck` |
| `INVENTORIES_DATA` (stocks, forages, stockage) | donnée hebdo centrale | « EIA expects highest natural gas inventories in a decade heading into winter », « crude inventories draw » | `stocks de brut`, `stocks américains`, `stockage de gaz`, `taux de remplissage`, `nombre de forages` | `crude inventories`, `stockpiles`, `inventory draw`, `inventory build`, `gas storage`, `rig count` |
| `WEATHER_CLIMATE` | énergie, agricole | « Hurricane Isaias leaves two dead and 800,000 without power » (Al Jazeera) | `canicule`, `vague de froid`, `sécheresse`, `ouragan`, `tempête`, `cyclone`, `El Niño`, `La Niña`, `niveau du Rhin` | `heatwave`, `cold snap`, `drought`, `hurricane`, `storm`, `typhoon`, `El Niño`, `La Niña`, `Rhine water levels` |
| `SHIPPING_LOGISTICS` | fret, chokepoints | « Il est désormais plus cher d'affréter un pétrolier que de lancer une fusée » (Le Figaro, 10/10), « Baltic Dry Index at Over 1-Month Low » (Hellenic) | `fret maritime`, `affrètement`, `pétrolier`, `canal de Suez`, `canal de Panama`, `mer Rouge`, `Ormuz`, `grève portuaire` | `freight rates`, `charter rates`, `Baltic Dry Index`, `VLCC`, `Suez Canal`, `Red Sea`, `port strike`, `container rates` |
| `SUPPLY_POLICY` (OPEP+, quotas, plafonds de prix, taxes) | décisions politiques d'offre | « Accord Moscou-Washington sur le pétrole : quelles conséquences après la... » (RFI), « Trump announces deal for Russian diesel » | `quotas de production`, `hausse de production`, `baisse de production`, `plafonnement du prix`, `embargo`, `accord sur le pétrole` | `production cuts`, `output hike`, `price cap`, `export ban`, `OPEC+ meeting` |
| `CAPITAL_RETURN` (rachat d'actions, dividende) | séparer du M&A | « Air Liquide s'est doté de nouveaux objectifs... » | `rachat d’actions`, `dividende`, `acompte sur dividende`, `programme de rachat` | `share buyback`, `dividend hike`, `special dividend` |
| `ANALYST_ACTION` (recommandations, objectifs de cours tiers) | distinguer des notations souveraines ; **attention AMF** : ne rapporter que comme fait attribué | « JPMorgan makes bullish call in fixed income space » (CNBC) | `relève son objectif de cours`, `abaisse sa recommandation`, `initie la couverture` | `price target`, `upgrades`, `downgrades`, `initiates coverage` |
| `CRYPTO_EVENT` (ETF, listing, piratage, liquidations, depeg) | MARKETS ne couvre que `bitcoin|crypto` ; INCIDENT ne connaît ni `hack` ni `exploit` | « Ether ETFs extend outflow streak to nine days as Solana funds snap record » (The Block), « One year after 10/10 flash crash, bitcoin and ether liquidity have rebuilt » (CoinDesk), « Bitcoin steadies near $83,000 as spot ETF outflows top $680 million » (Investing) | `liquidations`, `piratage`, `ETF spot`, `décotation`, `stablecoin`, `dépeg`, `entrées nettes`, `sorties nettes` | `hack`, `exploit`, `drained`, `depeg`, `liquidations`, `ETF outflows`, `ETF inflows`, `listing`, `delisting` |
| `MARKET_STRUCTURE` (suspension de cotation, volatility halt, circuit breaker, réaménagement d'indice) | trader | « Euronext announces volumes for September 2026 » | `suspension de cotation`, `cotation suspendue`, `circuit breaker`, `entrée dans le CAC 40`, `réaménagement` | `trading halt`, `circuit breaker`, `index rebalancing`, `delisting` |
| `MACRO` : sous-étiquettes de publication (emploi, inflation, PMI, PIB, commerce, confiance) | lecture rapide du calendrier | « CPI for all items increases 0.4% in August; gasoline rises » (BLS), « payroll employment (+29,000) and unemployment rate (4.2%) » (BLS), « Le sentiment des consommateurs américains recule légèrement en octobre » (Investing) | `inflation sous-jacente`, `IPCH`, `PMI`, `confiance des consommateurs`, `ventes au détail` | `core CPI`, `HICP`, `payrolls`, `jobless claims`, `PMI`, `consumer sentiment`, `retail sales` |

Remarque de cohérence : le lexique MACRO a un commentaire explicite « Bare CPI is excluded : in French it is the Cour pénale internationale » : même logique pour **`BTP`**, **`Orange`**, **`Vinci`**, **`Meta`**, **`Shell`**, **`Intel`**, **`Strike`**, **`Treasury`** (liste des pièges à injecter dans les tests de non-régression du lexique).

---

## G. Synthèse des priorités

P0 :
1. Corriger l'exactitude des dates (E #4) avant d'activer Euronext, EIA Today in Energy, ESMA/Conseil UE/FCA, Investing FR (fuseau).
2. Exclusions par actif + casse/variantes (E #1, #2) : sans elles la watchlist trader génère du bruit (Nasdaq, BCE, Orange, BTP, ETH, Treasury).
3. Alertes : `primaryOnly`, `perStory` (E #3) ; règle « Banques centrales » basée sur `trust ≥ 90` plutôt que `impact HIGH`.
4. Corriger le lexique (F1) : `strike(s)` (« Trump Strikes Diesel Deal »), `rachat`, `treasury`, `nucléaire`, `downgrade/dégrade`.
5. Actualiser les mots-clés périmés : `Powell` (président = Warsh dans le corpus), `ASN` -> `ASNR`, `Opec+/Opep+` en casse mixte.
6. Remplacer les préréglages (B4) : TRADING 17 -> ~40 flux ; ÉNERGIE 9 -> ~35 flux avec primaires (EIA, NHC, GIE, EDF, USDA, DESNZ).

P1 : agenda officiel, e-mail/webhooks pré-ouverture, brief « depuis ma dernière session », sources primaires visibles, unités métier dans les chiffres clés, vues enregistrées, watchlist par paniers, état des sources, filtre de ton « conseil », migration de flux du catalogue, nouveaux types (SUPPLY_DISRUPTION, INVENTORIES_DATA, WEATHER_CLIMATE, SHIPPING_LOGISTICS, CRYPTO_EVENT).

P2 : « Références » officielles (BCE, Fed, SNB), flux par actif via agrégateur, partage d'équipe, ANALYST_ACTION, SEC EDGAR 8-K/Form 4 pour « dépôts de mes valeurs ».
