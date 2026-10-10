> Audit du 10/10/2026 (sources vérifiées en direct depuis un poste local ; l'hôte de prod en bloque certaines, voir centre-operations.md).

# Audit domaine : Gestion d'actifs + Risque & conformité

Méthode : lecture de `professions.ts`, `proSourceCatalog.ts`, `signal.ts`, `alertService.ts`, `opsTypes.ts`. Vérification des flux avec `fetchFeed` (`feedUrlGuard`, même fetcher que la prod) le samedi 2026-10-10 vers 16 h UTC, environ 750 URL candidates testées, fichier de test supprimé. Les âges sont donc gonflés par le week-end (les flux de presse publient peu le samedi ; beaucoup de flux institutionnels ont leur dernier item du vendredi). Toutes les URL "OK" ci-dessous ont été parsées avec succès et ont des items. Âge = âge de l'item le plus récent.

Constats transverses les plus importants (détail plus bas) :
1. Les règles d'alerte sans actif ni mot-clé (`Résultats des valeurs suivies`, `Fusions-acquisitions sur le portefeuille`) ne sont pas limitées à la watchlist : `alertService.evaluateRecent` (l.72-73) n'applique AUCUN filtre d'entité si `entityId` est null et `keywords` est vide. Elles sonnent sur toute la presse des sources du dashboard.
2. Plancher d'impact par défaut = MEDIUM (`DEFAULT_MIN_IMPACT`, alertService l.96-97) alors que les poids de base de `signal.ts` sont : EARNINGS 25, REGULATION_LEGAL 20, INCIDENT 25, RATINGS 30, GEOPOLITICS_SANCTIONS 30 (MEDIUM = 35). Une règle `RATINGS` ou `EARNINGS` sans `minImpact` explicite ne sonne que si l'item cumule un 2e type, un mot de choc, un `%` >= 5 ou 2+ sources. Le gabarit actuel "Notation souveraine France" et "Résultats des valeurs suivies" est donc quasi muet. Les alertes RC `Actions des régulateurs` (20) et `Cyberattaques - impact fort` (HIGH = 60 requis, INCIDENT 25) sont dans le même cas.
3. Trois flux déjà au catalogue n'ont AUCUNE date dans le XML : ESMA (`rss.xml`), Conseil UE (`pressreleases.ashx`), Europol. `rssIngestionService.ts:33` donne `publishedAt = null`. Et FCA / Crisis Group utilisent des dates "Friday, October 9, 2026 - 12:06" que `new Date()` ne sait pas lire (le test donne NaN) : `new Date(raw.pubDate)` produit une Invalid Date, à vérifier (risque d'erreur Prisma ou d'item perdu). Prévoir un parseur de dates tolérant (ou un repli sur `ingestedAt`) avant d'ajouter FCA et Crisis Group.
4. Le filtre alerte `!item.publishedAt || publishedAt >= since` laisse passer les items sans date : flux sans date = tout item ré-ingéré est considéré neuf.

---

# PARTIE 1 : GESTION D'ACTIFS

## A. Persona

Qui : gérant de portefeuille actions/obligataire/diversifié (société de gestion, 1 à 3 fonds), analyste buy-side, gérant sous mandat, banquier privé / conseiller en gestion de patrimoine, family office (3 à 15 personnes). Le fond ESG / conformité produit est transverse. Le client paie pour ne pas se faire surprendre sur une ligne détenue et pour avoir un horodatage défendable de "ce que j'ai su et quand".

Décisions : ajuster/arbitrer une allocation (poids actions/taux/crédit/liquidités, couverture de change), alléger ou renforcer une ligne après un événement, prévenir un client ou un comité de risque (alerte client), respecter une contrainte (liste d'exclusion ESG, seuils SFDR, limites par émetteur, notation minimale investment grade), préparer le comité d'investissement / la lettre mensuelle / le reporting client, justifier a posteriori une décision (Journal).

Ce qui les fait bouger :
- Décisions de banques centrales (BCE, Fed, BoE, BoJ) et leur communication : taux, bilan, minutes, discours (Fed speeches, ECB blog, BIS central bankers' speeches).
- Chiffres macro (inflation CPI/IPC, emploi, PMI, PIB), dette souveraine et notation de la France/Italie/USA (Moody's, S&P, Fitch, calendrier de revue le vendredi soir), spread OAT-Bund.
- Résultats et guidance des valeurs détenues (saison des résultats : mi-octobre à mi-novembre, mi-janvier à fin février, fin avril à mi-mai, fin juillet à mi-août), avertissements sur résultats, dividendes, rachats d'actions.
- OPA/OPE/fusions, introductions en bourse, retraits de cote (AMF liste des offres).
- Flux : collecte nette / décollecte des fonds et ETF, rachats massifs, fermeture/suspension de fonds, mouvements de grands gérants (BlackRock, Amundi, Vanguard), flux ETF bitcoin.
- Réglementation produit : SFDR, taxonomie, CSRD/CSDDD, UCITS/AIFM, MiFID II, guidance ESMA sur les noms de fonds ESG ("fund names"), greenwashing, amendes AMF/SEC.
- Géopolitique et droits de douane (effet marché), pétrole/or.
- Épargne française (assurance-vie, PEA, Livret A, PER) pour la banque privée / CGP.

Journée type :
- Pré-ouverture (7 h-9 h) : "Brief" de la nuit : Asie (BoJ, Nikkei), futures US, publications Europe du matin (résultats), calendrier macro du jour, alertes sur lignes détenues. Veut 10 lignes, triées par impact, avec source primaire.
- Séance : veille passive. Alertes ciblées uniquement (ligne détenue, décision de banque centrale, notation). Les rumeurs (OPA, démission, profit warning) doivent être horodatées et signalées comme non confirmées (RUMOR_RADAR).
- Clôture (17 h 35-19 h) : résultats après bourse, décisions de notation (vendredi soir), flux, pré-rédaction de l'alerte client.
- Hebdomadaire : agenda de la semaine (banques centrales, résultats, macro), revue des sujets (Sujets), revue des décisions consignées (Journal).
- Mensuel/trimestriel : lettre aux clients, comité d'investissement, reporting : export du Journal et du Brief.

Ce qui ferait payer : (a) zéro bruit sur la watchlist, avec exclusions et regroupement par émetteur ; (b) exhaustivité des sources primaires (BC, régulateurs, wires de communiqués d'émetteurs) ; (c) traçabilité horodatée exportable (preuve de diligence) ; (d) agenda des publications ; (e) partage d'une watchlist/alerte à une équipe ; (f) notifications e-mail/Slack hors de l'application.

## B. Sources

### B.1 Preset actuel `ASSET_MANAGEMENT` (16 slugs)

Garder : `bce-ecb`, `federal-reserve`, `bank-of-england`, `bis`, `amf`, `esma`, `financial-times`, `cnbc`, `marketwatch`, `la-tribune`, `challenges`, `bfm-bourse`, `bfm-economie`, `investing-fr`, `le-figaro`.
À requalifier :
- `le-monde` : flux économie utile mais bruité (macro, social) ; garder uniquement le flux `economie`, pas `international` (le catalogue a les deux feeds).
- Manquent dans le preset : `sec` (un gérant détient des valeurs US : communiqués + litigation releases), `conseil-ue` (droits de douane, sanctions : effet marché), `commission-europeenne` (nouveau, voir B.2), nouvelles sources BC (BoJ), `les-echos` (si confirmé en prod), wires d'émetteurs (Actusnews, GlobeNewswire, PR Newswire), presse spécialisée ESG/ETF/fonds (ESG Today, Responsible Investor, Top1000funds, ETF Express).
À ne PAS mettre : `coindesk`/`cointelegraph` (hors persona sauf option crypto), `al-jazeera`, `bbc-news` (présents dans d'autres presets, pas dans celui-ci : OK).

### B.2 Nouvelles sources vérifiées (OK, items > 0, récentes)

Les slugs existants à ÉTENDRE avec de nouveaux feeds sont marqués "(étend)". Colonne âge : celle du dernier item au moment du test (samedi).

| Nom | slug | domaine | catégorie | trust | feeds | items | âge |
|---|---|---|---|---|---|---|---|
| Federal Reserve : discours | federal-reserve (étend) | federalreserve.gov | INSTITUTIONS | 96 | https://www.federalreserve.gov/feeds/speeches.xml | 15 | 2,3 j |
| Federal Reserve : politique monétaire | federal-reserve (étend) | federalreserve.gov | INSTITUTIONS | 96 | https://www.federalreserve.gov/feeds/press_monetary.xml | 15 | 2,9 j |
| Federal Reserve : FEDS Notes (recherche) | federal-reserve (étend) | federalreserve.gov | INSTITUTIONS | 96 | https://www.federalreserve.gov/feeds/feds_notes.xml | 15 | 0,9 j |
| Federal Reserve : régulation bancaire | federal-reserve (étend) | federalreserve.gov | INSTITUTIONS | 96 | https://www.federalreserve.gov/feeds/press_bcreg.xml | 15 | 7,8 j (flux basse fréquence) |
| BCE : statistiques | bce-ecb (étend) | ecb.europa.eu | INSTITUTIONS | 96 | https://www.ecb.europa.eu/rss/statpress.html | 15 | 2,2 j |
| BCE : supervision bancaire | bce-ecb (étend) | bankingsupervision.europa.eu | INSTITUTIONS | 95 | https://www.bankingsupervision.europa.eu/rss/press.html | 15 | 3,4 j |
| Banque d'Angleterre : discours et publications | bank-of-england (étend) | bankofengland.co.uk | INSTITUTIONS | 95 | https://www.bankofengland.co.uk/rss/speeches ; https://www.bankofengland.co.uk/rss/publications | 50 + 50 | 2,2 j / 1,1 j |
| BRI : discours des banquiers centraux | bis (étend) | bis.org | INSTITUTIONS | 94 | https://www.bis.org/doclist/cbspeeches.rss ; https://www.bis.org/doclist/bispapers.rss | 50 / 25 | 2,7 j |
| Banque du Japon | bank-of-japan | boj.or.jp | INSTITUTIONS | 95 | https://www.boj.or.jp/en/rss/whatsnew.xml | 51 | 1,3 j |
| Riksbank | riksbank | riksbank.se | INSTITUTIONS | 94 | https://www.riksbank.se/en-gb/rss/press-releases/ | 10 | 2,4 j |
| Reserve Bank of Australia | rba | rba.gov.au | INSTITUTIONS | 94 | https://www.rba.gov.au/rss/rss-cb-media-releases.xml | 1 | 9,6 j (basse fréquence, < 30 j) |
| Fed de San Francisco | frb-san-francisco | frbsf.org | INSTITUTIONS | 90 | https://www.frbsf.org/feed/ | 10 | 3,0 j |
| BLS (emploi, CPI, PPI) | bls | bls.gov | INSTITUTIONS | 95 | https://www.bls.gov/feed/bls_latest.rss ; https://www.bls.gov/feed/empsit.rss ; https://www.bls.gov/feed/cpi.rss | 1 / 12 / 12 | 1,1 j / 8,2 j / 29,2 j (mensuel, < 30 j) |
| BEA (PIB, PCE US) | bea | bea.gov | INSTITUTIONS | 94 | https://www.bea.gov/rss/rss.xml | 48 | 4,2 j |
| US Census Bureau : indicateurs | us-census | census.gov | INSTITUTIONS | 92 | https://www.census.gov/economic-indicators/indicator.xml | 18 | 2,1 j |
| ONS (Royaume-Uni) | ons-uk | gov.uk | INSTITUTIONS | 93 | https://www.gov.uk/government/organisations/office-for-national-statistics.atom | 20 | 0,9 j |
| OMC / WTO | wto | wto.org | INSTITUTIONS | 92 | https://www.wto.org/library/rss/latest_news_e.xml ; https://www.wto.org/library/rss/latest_news_f.xml | 10 / 8 | 2,7 j |
| Commission européenne (salle de presse FR/EN) | commission-europeenne | ec.europa.eu | INSTITUTIONS | 94 | https://ec.europa.eu/commission/presscorner/api/rss?language=fr ; https://ec.europa.eu/commission/presscorner/api/rss?language=en | 10 / 10 | 1,0 j |
| Financial Stability Board | fsb | fsb.org | INSTITUTIONS | 95 | https://www.fsb.org/feed/ | 10 | 1,4 j |
| CSSF (Luxembourg, fonds UCITS/SICAV) | cssf | cssf.lu | INSTITUTIONS | 92 | https://www.cssf.lu/en/feed/ | 10 | 2,1 j |
| FINMA (Suisse, banque privée) | finma | finma.ch | INSTITUTIONS | 93 | https://www.finma.ch/en/rss/news/ | 50 | 2,2 j |
| SFC Hong Kong | sfc-hk | sfc.hk | INSTITUTIONS | 90 | https://www.sfc.hk/en/RSS-Feeds/Press-releases | 7 | 1,6 j |
| AMF : offres publiques (OPA/OPE) | amf (étend) | amf-france.org | INSTITUTIONS | 95 | https://www.amf-france.org/fr/flux-rss/display/29 | 1 | 8,3 j (basse fréquence, très utile M&A) |
| AMF : communiqués de presse | amf (étend) | amf-france.org | INSTITUTIONS | 95 | https://www.amf-france.org/fr/flux-rss/display/23 | 200 | 8,4 j |
| AMF : actualités | amf (étend) | amf-france.org | INSTITUTIONS | 95 | https://www.amf-france.org/fr/flux-rss/display/30 | 200 | 1,1 j |
| AMF : épargnants | amf (étend) | amf-france.org | INSTITUTIONS | 95 | https://www.amf-france.org/fr/flux-rss/display/22 | 200 | 1,3 j |
| SEC : discours et déclarations | sec (étend) | sec.gov | INSTITUTIONS | 95 | https://www.sec.gov/news/speeches-statements.rss | 25 | 1,1 j |
| SEC : tender offers (SC TO, OPA US) | sec-edgar-offres | sec.gov | INSTITUTIONS | 90 | https://www.sec.gov/cgi-bin/browse-edgar?action=getcurrent&type=SC%20TO&output=atom | 10 | 0,9 j (titre = "SC TO-T - Société (CIK)", peu de texte) |
| SEC : S-1 (introductions en bourse US) | sec-edgar-ipo | sec.gov | INSTITUTIONS | 90 | https://www.sec.gov/cgi-bin/browse-edgar?action=getcurrent&type=S-1&output=atom | 21 | 0,8 j |
| Les Echos (via services.lesechos.fr) | les-echos | lesechos.fr | FINANCIAL_PRESS | 85 | https://services.lesechos.fr/rss/les-echos-finance-marches.xml ; .../les-echos-economie.xml ; .../les-echos-patrimoine.xml ; .../les-echos-monde.xml ; .../les-echos-politique.xml ; .../les-echos-tech-medias.xml | 20 chacun | 0,1 à 0,4 j |
| FT : flux thématiques | financial-times (étend) | ft.com | FINANCIAL_PRESS | 88 | https://www.ft.com/asset-management?format=rss ; .../central-banks?format=rss ; .../bonds?format=rss ; .../equities?format=rss ; .../hedge-funds?format=rss ; .../private-equity?format=rss ; .../private-credit?format=rss ; .../banks?format=rss ; .../climate-capital?format=rss ; .../commodities?format=rss ; .../currencies?format=rss ; .../global-economy?format=rss ; .../us-economy?format=rss ; .../emerging-markets?format=rss ; .../sovereign-wealth-funds?format=rss ; .../alphaville?format=rss ; .../companies?format=rss ; .../markets?format=rss | 25 chacun | 0,0 à 1,5 j (wealth-management 12,5 j : exclure) |
| Wall Street Journal | wsj | wsj.com | FINANCIAL_PRESS | 86 | https://feeds.content.dowjones.io/public/rss/RSSMarketsMain ; https://feeds.content.dowjones.io/public/rss/socialeconomyfeed ; https://feeds.content.dowjones.io/public/rss/WSJcomUSBusiness | 61 / 36 / 85 | 0,2 à 0,8 j |
| MarketWatch : bulletins | marketwatch (étend) | marketwatch.com | FINANCIAL_PRESS | 80 | https://feeds.content.dowjones.io/public/rss/mw_bulletins | 10 | 0,0 j |
| New York Times (DealBook, Economy, Business) | new-york-times | nytimes.com | GENERAL_PRESS | 85 | https://rss.nytimes.com/services/xml/rss/nyt/DealBook.xml ; .../Economy.xml ; .../Business.xml | 20 / 20 / 49 | 0,1 à 1,0 j |
| The Economist : finance & économie | the-economist | economist.com | FINANCIAL_PRESS | 85 | https://www.economist.com/finance-and-economics/rss.xml | 300 | 2,1 j (feed volumineux, hebdomadaire) |
| Guardian : économie et marchés | the-guardian (étend) | theguardian.com | GENERAL_PRESS | 82 | https://www.theguardian.com/business/economics/rss ; https://www.theguardian.com/business/stock-markets/rss | 20 / 20 | 0,5 j |
| BBC : économie | bbc-news (étend) | bbc.co.uk | GENERAL_PRESS | 82 | https://feeds.bbci.co.uk/news/business/economy/rss.xml | 24 | 1,0 j |
| Le Figaro : conjoncture / bourse / sociétés / flash éco | le-figaro (étend) | lefigaro.fr | FINANCIAL_PRESS | 80 | https://www.lefigaro.fr/rss/figaro_conjoncture.xml ; .../figaro_bourse.xml ; .../figaro_societes.xml ; .../figaro_flash-eco.xml | 20 chacun | 0,0 à 0,4 j |
| Le Monde : argent, éco française, entreprises | le-monde (étend) | lemonde.fr | GENERAL_PRESS | 84 | https://www.lemonde.fr/argent/rss_full.xml ; .../economie-francaise/rss_full.xml ; .../entreprises/rss_full.xml | 20 chacun | 0,1 à 0,5 j |
| Le Revenu | le-revenu | lerevenu.com | FINANCIAL_PRESS | 72 | https://www.lerevenu.com/rss.xml | 50 | 0,1 j (épargne/bourse grand public, attention au vocabulaire de conseil) |
| L'Express : économie | l-express | lexpress.fr | GENERAL_PRESS | 76 | https://www.lexpress.fr/rss/economie.xml | 100 | 0,1 j |
| Courrier international | courrier-international | courrierinternational.com | GENERAL_PRESS | 76 | https://www.courrierinternational.com/feed/all/rss.xml | 20 | 0,1 j |
| RFI : économie | rfi | rfi.fr | GENERAL_PRESS | 80 | https://www.rfi.fr/fr/economie/rss | 30 | 0,2 j |
| Investing.com FR : feeds complémentaires | investing-fr (étend) | fr.investing.com | FINANCIAL_PRESS | 72 | https://fr.investing.com/rss/news_14.rss (éco) ; .../news_356.rss (entreprises) ; .../market_overview.rss (marchés) ; .../stock.rss (analyses bourse : vocabulaire de conseil possible) ; .../commodities.rss | 10 chacun | 0,1 à 1,4 j |
| Investing.com EN | investing-com | investing.com | FINANCIAL_PRESS | 70 | https://www.investing.com/rss/news.rss ; .../news_25.rss ; .../news_14.rss ; .../news_95.rss ; .../news_1.rss ; .../news_11.rss ; .../market_overview.rss | 10 chacun | 0,1 à 1,1 j |
| Nasdaq.com (syndication) | nasdaq-com | nasdaq.com | FINANCIAL_PRESS | 62 | https://www.nasdaq.com/feed/rssoutbound?category=Markets ; ...=Stocks ; ...=ETFs ; ...=Earnings ; ...=Economy ; ...=Commodities ; ...=Investing ; ...=Financial+Advisors ; ...=Regulation ; ...=Dividends | 15 chacun | 0,0 à 4,1 j (agrégateur, contenu Zacks avec beaucoup de wording "buy/sell" : à surveiller côté AMF) |
| Seeking Alpha : market currents | seeking-alpha | seekingalpha.com | FINANCIAL_PRESS | 60 | https://seekingalpha.com/market_currents.xml | 7 | 0,0 j (contributeurs, recommandations : rumeurs possibles, trust bas ; N'utiliser que market_currents, pas feed.xml) |
| ESG Today | esg-today | esgtoday.com | FINANCIAL_PRESS | 75 | https://www.esgtoday.com/feed/ | 10 | 1,1 j |
| Responsible Investor | responsible-investor | responsible-investor.com | FINANCIAL_PRESS | 78 | https://www.responsible-investor.com/feed/ | 10 | 1,1 j |
| Top1000funds | top1000funds | top1000funds.com | FINANCIAL_PRESS | 75 | https://www.top1000funds.com/feed/ | 10 | 2,5 j |
| ETF Express | etf-express | etfexpress.com | FINANCIAL_PRESS | 70 | https://www.etfexpress.com/feed/ | 10 | 1,3 j |
| ETF Trends | etf-trends | etftrends.com | FINANCIAL_PRESS | 66 | https://www.etftrends.com/feed/ | 50 | 0,2 j (marketing émetteurs, trust bas) |
| Private Banker International | private-banker-international | privatebankerinternational.com | FINANCIAL_PRESS | 75 | https://www.privatebankerinternational.com/feed/ | 10 | 1,2 j |
| AdvisorHub | advisorhub | advisorhub.com | FINANCIAL_PRESS | 72 | https://www.advisorhub.com/feed/ | 10 | 0,8 j |
| Money Marketing (UK) | money-marketing | moneymarketing.co.uk | FINANCIAL_PRESS | 68 | https://www.moneymarketing.co.uk/feed/ | 12 | 1,1 j |
| Economie Matin | economie-matin | economiematin.fr | FINANCIAL_PRESS | 60 | https://www.economiematin.fr/rss | 24 | 0,0 j (opinion + info, trust bas) |
| JDN économie | jdn | journaldunet.com | FINANCIAL_PRESS | 65 | https://www.journaldunet.com/economie/rss/ | 60 | 1,1 j |
| Actusnews (informations réglementées FR) | actusnews | actusnews.com | FINANCIAL_PRESS | 82 | https://www.actusnews.com/rss/ | 20 | 1,0 j (communiqués d'émetteurs, source primaire non vérifiée par un tiers) |
| GlobeNewswire (sociétés cotées + résultats) | globenewswire | globenewswire.com | FINANCIAL_PRESS | 80 | https://www.globenewswire.com/RssFeed/orgclass/1/feedTitle/GlobeNewswire%20-%20News%20about%20Public%20Companies ; https://www.globenewswire.com/RssFeed/subjectcode/13-Earnings%20Releases%20And%20Operating%20Results/feedTitle/GlobeNewswire%20-%20Earnings%20Releases%20And%20Operating%20Results | 20 / 20 | 0,1 j / 1,0 j |
| PR Newswire (services financiers, résultats) | pr-newswire | prnewswire.com | FINANCIAL_PRESS | 80 | https://www.prnewswire.com/rss/financial-services-latest-news/financial-services-latest-news-list.rss ; https://www.prnewswire.com/rss/financial-services-latest-news/earnings-list.rss ; https://www.prnewswire.com/rss/news-releases-list.rss | 20 chacun | 0,1 à 1,2 j |
| Think tanks / recherche : IRIS | iris | iris-france.org | GENERAL_PRESS | 75 | https://www.iris-france.org/feed/ | 10 | 0,3 j |
| Foreign Policy | foreign-policy | foreignpolicy.com | GENERAL_PRESS | 74 | https://foreignpolicy.com/feed/ | 25 | 0,8 j |
| France 24 : flux généraux FR/EN | france-24 (étend) | france24.com | GENERAL_PRESS | 82 | https://www.france24.com/fr/rss ; https://www.france24.com/en/rss | 24 / 24 | 0,0 j |

Remarques de vérification :
- Les Echos : le commentaire du catalogue dit "refused automated access" pour lesechos.fr. L'hôte `services.lesechos.fr` (et non `www.lesechos.fr/rss/…` qui répond 404) renvoie bien du RSS via `fetchFeed` depuis le poste de dev. À RE-TESTER depuis l'IP de prod OVH avant d'ajouter (le blocage éventuel est IP/anti-bot). Mêmes réserves pour GlobeNewswire (le catalogue le dit refusé, ces URL ont été acceptées ici) et Nasdaq / WSJ / NYT / FT thématiques. Les feeds Les Echos `les-echos-bourse.xml` et `les-echos-entreprises-secteurs.xml` répondent 404.
- Les flux AMF (display/21,22,23,25,26,28,30) ont 200 items : prévoir `maxItems` raisonnable.
- Les flux EDGAR "getcurrent" renvoient les derniers dépôts toutes sociétés (10 à 40 items) : faible rappel, utile seulement pour les valeurs en watchlist (titre "SC TO-T - Société (CIK)").
- Eurostat `statistics-update.rss` : 2385 items, 0,3 j, mais ce sont des mises à jour de jeux de données ("ESTAT - Data and Data Structure updates") sans valeur éditoriale : REJETÉ (bruit).

### B.3 Candidats rejetés

| Candidat | Raison |
|---|---|
| OFAC (recent actions) | RSS officiellement retiré le 6 février 2025 (ofac.treasury.gov/recent-actions/20250206) ; toutes les URL testées 404 / DNS. Substitut : Federal Register (voir partie 2). |
| US Treasury press releases (home.treasury.gov), FinCEN (`/news/rss.xml`, etc.), AFT (Agence France Trésor) | 404 ou 403. FinCEN : substitut Federal Register. |
| FATF / GAFI (4 URL), CFTC (3 URL), IOSCO, IMF (403 + flux invalide), OECD (403), World Bank (404, DNS), Banque de France (403/404), ACPR (403/404), INSEE (500, page RSS existe mais flux inconnu), Trésor (404), Euronext (404), EIOPA (404), ENISA (404), BaFin (404), Bundesbank (404), SNB (404), ASIC (404), MAS (flux invalide), AMLA (404), Interpol (timeout 8 s) | Pas de flux exploitable trouvé depuis `fetchFeed`. FATF, IMF, OECD, CFTC, IOSCO : blocage anti-bot (403). INSEE : 500 : à retenter plus tard, c'est la source macro FR n°1 manquante. |
| Moody's, S&P Global Ratings, Fitch, MSCI, LSEG, Morningstar | Pas de RSS public (404/403/flux invalide). Les décisions de notation ne sont accessibles que via la presse (d'où l'importance du lexique RATINGS). |
| ETF Stream (403), Citywire (404), Investment Week (404), IPE (404), Euractiv (403) | Bloqués ou introuvables. |
| ETF.com | 186 j sans item : mort. |
| MarketWatch `mw_marketpulse` (464 j), `mw_realtimeheadlines` (486 j), WSJ `feeds.a.dj.com` (620 j), Investing `central_banks.rss` (3638 j), `stock_Indices.rss` (68 j), `market_overview_Technical.rss` (67 j), CNBC Market Insider (81 j), Nasdaq Cryptocurrencies (214 j), Ifri FR (1297 j), Bruegel (52 j), CERT-FR `ioc` (821 j), NCSC UK `report` (521 j) | Périmés. |
| FT `wealth-management` (12,5 j) | Trop lent pour un flux d'alerte. |
| Boursorama, Zonebourse, MarketScreener, Capital, Cerclefinance, Newsmanagers, MoneyVox, ABC Bourse, Boursier, Agefi, Investir, Le Revenu `investir` | 404, 403 ou "ce lien ne renvoie pas un flux valide". Confirme le commentaire du catalogue pour Zonebourse/Boursorama. |
| Yahoo Finance, Reuters (401 / DNS), Barron's, Bloomberg | Pas de flux public. |
| Bank of Canada | 30,2 j : à la limite de la tolérance basse fréquence (30 j) ; à revoir après la prochaine décision. |
| `bfm-bourse` "Conseils" (`tradingsat.com/rssanalyses.xml`) | OK techniquement (1,4 j) mais contenu "conseils" = vocabulaire d'achat/vente, contraire à la ligne AMF du produit. |

## C. Actifs et mots-clés (watchlist par défaut, 28 entités)

Règles de matching rappelées (signal.ts l.329-358) : titre + chapô uniquement ; un terme tout en majuscules <= 5 caractères ou toute forme capitalisée <= 3 caractères = sensible à la casse ; le reste = mot entier insensible à la casse/accents ; le `symbol` est ajouté aux termes (donc un symbole court est un terme de matching : ne JAMAIS mettre `MC`, `OR`, `AI`, `AIR`, `EN`, `ML`). Le label n'est utilisé que si aucun mot-clé. Il n'y a PAS de liste d'exclusion dans le modèle (voir E).

Problèmes de précision du gabarit actuel :
- `Schneider` seul : patronyme (Schneider Electric aussi : Schneider National, Schneider (nom propre), Schneider Logistics). Garder `Schneider Electric` et `Peter Herweck` seulement si le dirigeant est à jour ; retirer `Schneider`.
- `BNP` seul (3 majuscules, sensible à la casse) : acronyme aussi de partis politiques (Bangladesh Nationalist Party, British National Party, Bulgarian...). Acceptable en tant que ticker mais ajouter `BNP Paribas` en tête ; garder `BNPP`.
- `Apple` : ok pour la finance mais "Apple" ressort pour le fruit/Apple Music ; `iPhone` et `Tim Cook` sont fiables. `Azure` (Microsoft) : "Côte d'Azur" non concerné (mot entier "azur"), mais "azure" couleur/Azure Cosmos : acceptable.
- `Pouyanné` : à vérifier (dirigeant) ; `Louis Vuitton`/`Bernard Arnault` ok.
- `Notation souveraine France` : le terme `notation souveraine` seul n'est pas spécifique à la France (matche tout pays). `dette française` ressort sur toute news budgétaire : volontaire mais gonfle les alertes ; le couple `Moody’s France` / `Fitch France` suppose que les deux mots soient accolés, ce qui est rare ("Moody's abaisse la note de la France"). Utiliser plutôt `note de la France`, `notation de la France`, `note souveraine de la France`, `note de crédit de la France`, `la France perd son`, `AAA France` et traiter l'agence via l'event type RATINGS.
- Le gabarit n'a AUCUN indice, taux, change, matière première, banque centrale ni thème (ESG, ETF, flux) : un gérant a besoin de ces entités.
- Le gabarit n'a aucune entité "marché de taux" (OAT, Bund, Treasuries).

| Label | Kind | Symbol | Mots-clés (FR + EN) | Faux positifs / exclusions à prévoir |
|---|---|---|---|---|
| CAC 40 | INDEX | null | CAC 40, CAC40, SBF 120, indice parisien, Bourse de Paris | `CAC` seul interdit (Crédit Agricole... non) ; "Bourse de Paris" parfois commentaire événementiel |
| S&P 500 | INDEX | SPX | S&P 500, SP500, Wall Street, indice élargi américain | `Wall Street` seul = génériquement la place, accepter ; `SPX` 3 majuscules ok |
| Nasdaq | INDEX | null | Nasdaq 100, Nasdaq Composite, Nasdaq | "Nasdaq" = aussi l'opérateur (communiqués d'émetteurs, nouveaux listings) : retirer `Nasdaq` seul si trop bruyant |
| Euro Stoxx 50 | INDEX | SX5E | Euro Stoxx 50, Stoxx 50, Stoxx 600, STOXX Europe 600 | aucun notable |
| DAX | INDEX | null | DAX, Dax 40, indice allemand | `DAX` 3 caractères sensible à la casse : ne ressort pas de "dax" (Dax, ville) |
| Nikkei 225 | INDEX | null | Nikkei, Nikkei 225, Topix | aucun notable |
| MSCI World | INDEX | null | MSCI World, MSCI ACWI, MSCI Emerging Markets, MSCI EM | `MSCI` seul = société (actualité de l'éditeur), à ne pas inclure seul |
| VIX / volatilité | INDEX | VIX | VIX, indice de la peur, fear gauge, volatilité implicite | `volatilité` seul trop générique : ne pas mettre |
| EUR/USD | FX | EURUSD | EUR/USD, euro-dollar, euro dollar, parité euro dollar, euro vs dollar | `euro` seul interdit ; `dollar` seul interdit |
| USD/JPY | FX | USDJPY | USD/JPY, dollar-yen, yen, Banque du Japon | `yen` ok (mot court mais pas ambigu) |
| Dollar index | FX | DXY | DXY, dollar index, indice dollar | aucun notable |
| OAT 10 ans | RATE | OAT | OAT, OAT 10 ans, rendement de l'OAT, taux à 10 ans français, spread OAT-Bund, French 10-year yield | `OAT` sensible à la casse (avoine ok) ; `taux à 10 ans` seul ambigu (US/DE) : toujours "français" |
| Bund 10 ans | RATE | null | Bund, Bunds, Bund allemand, German 10-year, Schatz | `Bund` = "Bund" (ex. Shanghai Bund) : ok marginal |
| Treasuries US | RATE | null | Treasuries, T-Note, US Treasury yields, rendement du 10 ans américain, US 10-year yield | `Treasury` seul = ministère (Trésor US) : ne pas inclure seul |
| BCE | INSTITUTION | null | BCE, ECB, Banque centrale européenne, European Central Bank, Lagarde, taux de dépôt | `BCE` = aussi Bell Canada Enterprises (BCE Inc., Montréal) ; ne pas exclure faute de mécanisme, signaler |
| Fed | INSTITUTION | null | Fed, FOMC, Federal Reserve, Réserve fédérale, Beige Book, Jackson Hole, Powell | `Fed` sensible à la casse OK ; `Powell` : à mettre à jour avec le président en exercice, homonymes (Powell Industries) |
| Banque d'Angleterre | INSTITUTION | null | BoE, Bank of England, Banque d'Angleterre, Bailey | `Bailey` homonymes : retirer si doute |
| Banque du Japon | INSTITUTION | null | BoJ, BOJ, Bank of Japan, Banque du Japon | aucun notable |
| Or | COMMODITY | XAU | XAU, prix de l'or, cours de l'or, once d'or, gold price, gold prices, lingot | `or` (conjonction) et `gold` (nom propre Gold, Goldman) INTERDITS comme mots seuls |
| Pétrole Brent | COMMODITY | null | Brent, WTI, baril de pétrole, cours du pétrole, crude oil | `Brent` = prénom/ville (Brentford) : ok marginal |
| Bitcoin / ETF crypto | CRYPTO | BTC | Bitcoin, BTC, ETF bitcoin, spot bitcoin ETF | `BTC` 3 majuscules ; "Bitcoin" ok |
| LVMH | EQUITY | null | LVMH, Louis Vuitton, Moët Hennessy, Bernard Arnault | ok ; ne pas mettre le ticker `MC` |
| TotalEnergies | EQUITY | TTE | TotalEnergies, Total Energies, Pouyanné | `Total` seul interdit (adjectif) ; `TTE` ok |
| Airbus | EQUITY | null | Airbus, A320neo, A350, Guillaume Faury | `A350`, `A320neo` fiables |
| Schneider Electric | EQUITY | null | Schneider Electric | retirer `Schneider` seul |
| BNP Paribas | EQUITY | BNP | BNP Paribas, BNPP, BNP | cf. ci-dessus |
| Société Générale | EQUITY | GLE | Société Générale, SocGen, Societe Generale | `GLE` 3 majuscules ; `Générale` seul interdit |
| AXA | EQUITY | null | AXA, AXA IM, Thomas Buberl | `AXA` 4 majuscules sensible à la casse ok |
| Sanofi | EQUITY | null | Sanofi | aucun |
| L'Oréal | EQUITY | null | L'Oréal, L’Oréal, LOreal | l'apostrophe est normalisée : tester les deux formes |
| Hermès | EQUITY | null | Hermès, Hermes International | `Hermes` seul = dieu, Hermes (colis), Hermes (Evri) : préférer `Hermès` (accent) et `Hermes International` |
| Stellantis | EQUITY | STLA | Stellantis | `STLA` ticker ok |
| Orange (opérateur) | EQUITY | null | Orange SA, Christel Heydemann, opérateur Orange | `Orange` seul INTERDIT (fruit, couleur, Orange (ville), Orange Bank) |
| Nvidia | EQUITY | NVDA | Nvidia, NVDA, Jensen Huang | aucun |
| Apple | EQUITY | AAPL | Apple Inc, AAPL, iPhone, Tim Cook | `Apple` seul acceptable ; vérifier le bruit |
| Microsoft | EQUITY | MSFT | Microsoft, MSFT, Satya Nadella | `Azure` retiré : trop spécifique |
| ASML | EQUITY | ASML | ASML, Christophe Fouquet | aucun |
| Alphabet / Google | EQUITY | GOOGL | Alphabet, Google, GOOGL, Sundar Pichai | `Google` ok |
| Amazon | EQUITY | AMZN | Amazon.com, AMZN, AWS, Andy Jassy | `Amazon` seul (fleuve, forêt, Amazonie) INTERDIT |
| Meta Platforms | EQUITY | META | Meta Platforms, Zuckerberg, Facebook, Instagram | `Meta` seul (préfixe, "meta" mot) INTERDIT, `META` 4 majuscules sensible à la casse acceptable mais hasardeux |
| Tesla | EQUITY | TSLA | Tesla, TSLA, Elon Musk | `Tesla` = aussi unité (tesla) et Nikola Tesla : ok |
| Grands gérants d'actifs | COMPANY | null | Amundi, BlackRock, Vanguard, Pimco, Carmignac, AXA IM, Natixis IM, Candriam, Ostrum, Edmond de Rothschild, Rothschild & Co, Blackstone, KKR | `Apollo` interdit (espace) ; `Carlyle` ok |
| Notation souveraine France | COUNTRY | null | note de la France, notation de la France, note souveraine de la France, note de crédit de la France, dette française, AAA France, France rating, Moody’s France, Fitch France | retirer `notation souveraine` seul |
| Notation souveraine Italie / USA / UK | COUNTRY | null | note de l'Italie, Italy rating, note des États-Unis, US credit rating, Moody’s U.S., UK credit rating | idem |
| Agences de notation | INSTITUTION | null | Moody’s, S&P Global Ratings, Fitch Ratings, Scope Ratings, DBRS Morningstar | `Fitch` ok ; `Moody’s` apostrophe normalisée ; `S&P` seul ambigu avec `S&P 500` |
| Thème ESG / SFDR | THEME | null | SFDR, taxonomie verte, taxonomie européenne, greenwashing, écoblanchiment, CSRD, CSDDD, label ISR, ESG, fonds Article 8, fonds Article 9 | `ESG` 3 majuscules sensible à la casse ok ; `Article 8`/`Article 9` seuls interdits (articles de loi) : utiliser `fonds Article 8` / `fonds Article 9` |
| Thème ETF et fonds | THEME | null | ETF, ETFs, UCITS, OPCVM, fonds indiciels, fonds cotés, tracker, trackers | `tracker` mot courant (suivi de colis, fitness tracker) : ne pas inclure seul ; `ETF` ok |
| Flux et collecte | THEME | null | collecte nette, décollecte, rachats massifs, fund flows, net outflows, net inflows, outflows from funds, EPFR, Lipper | aucun notable |
| Épargne française | THEME | null | assurance-vie, Livret A, PEA, PER individuel, plan d'épargne retraite, fonds en euros, taux du Livret A | `PER` seul INTERDIT (price-earnings ratio, préposition latine, `PER` majuscule dans titres) |
| Saison des résultats | THEME | null | saison des résultats, earnings season, publication trimestrielle, profit warning, avertissement sur résultats, guidance | `guidance` seul intercepte "forward guidance" des banques centrales (déjà faux positif EARNINGS) |

Recommandation de gabarit livrable (28 entités) : 8 indices/FX/taux/matières, 4 banques centrales, 7 actions CAC/US/Euro, 1 gérants, 3 notation, 5 thèmes. Onboarding : laisser l'utilisateur cocher.

## D. Règles d'alerte et widgets

### D.1 Corrections du gabarit actuel
- `Résultats des valeurs suivies` : actuellement mondial (cf. constat 1) et plancher MEDIUM sur un poids EARNINGS de 25. Corriger en ajoutant un champ de portée "liste de suivi" (voir E) ET `minImpact: 'LOW'`.
- `Fusions-acquisitions sur le portefeuille` : même problème de portée.
- `Notation souveraine France` : ajouter `minImpact: 'LOW'` (RATINGS = 30).
- `Nvidia - impact fort` : bon exemple (impact HIGH = 60 est rare : prévoir plutôt MEDIUM). 

### D.2 Gabarit proposé (10 règles)

| Nom | Actif ou mots-clés | Types d'événement | minImpact / minTrust |
|---|---|---|---|
| Décision de banque centrale | BCE, Fed, Banque d'Angleterre, Banque du Japon | CENTRAL_BANK | LOW / trust 80 |
| Chiffres macro majeurs | mots-clés : inflation, IPC, CPI, emploi, nonfarm, PIB, PMI | MACRO | MEDIUM / trust 70 |
| Notation souveraine | Notation souveraine France, Italie, USA, Agences de notation | RATINGS | LOW / trust 60 |
| Résultats des valeurs suivies | chaque action de la watchlist (portée watchlist) | EARNINGS | LOW / trust 60 |
| Avertissement sur résultats | mots-clés : profit warning, avertissement sur résultats, abaisse ses prévisions | EARNINGS | LOW / trust 0 |
| OPA / M&A sur valeurs suivies | watchlist | M_AND_A | LOW / trust 60 |
| Offres publiques AMF | publisher AMF (liste des offres) | M_AND_A | LOW / trust 90 |
| Régulateurs sur le produit | mots-clés : SFDR, greenwashing, UCITS, MiFID, fonds | REGULATION_LEGAL | LOW / trust 80 |
| Chocs de marché | CAC 40, S&P 500, VIX | MARKETS | HIGH / trust 60 |
| Rumeur sur une valeur suivie | watchlist | tous (rumeur) | MEDIUM / sans floor de trust |

### D.3 Widgets (types existants)
Ligne 1 : `BRIEFING` (w6, h2) + `ALERTS` (w6, h2). Ligne 2 : `WATCHLIST_PULSE` (w8, h2) + `RUMOR_RADAR` (w4, h2). Ligne 3 : `LIVE_WIRE` (w8, h2) filtré banques centrales/macro + `EVENT_MIX` (w4, h1) + `SOURCE_HEALTH` (w4, h1). Ligne 4 : `DECISION_JOURNAL` (w12, h2). Pourquoi : en pré-ouverture, le Brief et les alertes sont ce qui est lu en premier ; le Journal (traçabilité) en bas.

## E. Lacunes produit (personnalisation)

| Besoin | État actuel | Proposition | Prio | Faisabilité RSS-only, sans IA |
|---|---|---|---|---|
| Portée "watchlist" sur une règle d'alerte | `alertService.ts` l.72-73 : sans entité ni mot-clé, la règle sonne sur tout le flux ; `AlertRuleTemplate` (professions.ts l.19-26) n'a pas d'option de portée | Ajouter `scope: 'WATCHLIST' | 'ALL'` à `AlertRule` (colonne Prisma + UI), évalué avec `requireEntityMatch` comme dans `opsFeedService` (l.163-181) | P0 | Facile, 100 % déterministe |
| Plancher d'impact explicite et lisible | Plancher par défaut MEDIUM invisible ; EARNINGS/RATINGS/INCIDENT/REGULATION de base < 35 | Afficher le plancher dans le formulaire, défaut LOW pour les règles par type d'événement, ou rebaser les poids ; corriger les gabarits (cf. D.1) | P0 | Facile |
| Mots à exclure par entité / règle | `compileTerms` n'a pas de négation ; `mutedKeywords` est global (utilisateur) | `excludeKeywords` par entité et par règle (chaîne de termes exclus évalués avant) | P0 | Facile |
| Date de publication absente ou non parsable | `rssIngestionService.ts:33` met null ou Invalid Date ; ESMA, Conseil UE, Europol sans date ; FCA, Crisis Group au format texte | Parseur de dates tolérant + repli `firstSeenAt` (date de première ingestion) | P0 | Facile |
| Agenda des publications | Aucun | Détecter dans les titres "calendrier", "agenda" ; ajouter feeds `agenda` AMF (display/26 OK 3,1 j) ; widget "à venir" basé sur les flux d'agenda institutionnels | P2 | Partiel : pas de calendrier économique sans donnée sous licence ; les calendriers officiels BCE/Fed existent en page HTML, pas en RSS |
| Notifications hors app | Notifications intra-application et push live (centre-operations.md) | E-mail quotidien (Brief) + e-mail/Slack/webhook par alerte | P1 | Facile (SMTP/webhook) |
| Export du Journal et du Brief | Journal en ligne uniquement (à confirmer : centre-operations.md) | Export CSV/PDF horodaté du Journal, du Brief, des items d'une alerte | P1 | Facile |
| Partage de watchlist / règles à une équipe | Watchlist par utilisateur (WatchEntity.userId) | Partage en lecture d'un dashboard, modèles d'équipe (société de gestion) | P2 | Moyen (modèle de données) |
| Groupes d'entités et sous-thèmes | Entités plates | Regrouper : "Portefeuille X", "Secteur Y", "Émetteurs exclus ESG" | P1 | Facile |
| Importer une watchlist (CSV de positions, ISIN) | Saisie manuelle | Import CSV (label, ticker, mots-clés) ; pas de position (pas de valeur) | P1 | Facile |
| ISIN / noms de fonds | Aucun | Autoriser ISIN comme terme (12 caractères alphanumériques : recherche exacte dans titres de communiqués) | P2 | Facile |
| Filtres par pays/langue/source | Sources par dashboard ; pas de filtre de langue | Filtre langue FR/EN, pays | P2 | Facile (détection de langue par jeu de mots simples) |
| Exclusion automatique des communiqués promotionnels | Aucun | Marquer les sources "communiqué d'émetteur" (wires) pour un tri/filtre dédié | P1 | Facile |
| Disposition de dashboard | Widgets fixes par template (professions.ts) | Déjà personnalisable par widgets ? À confirmer dans centre-operations.md ; sinon : réordonner/redimensionner/masquer | P1 | Facile |
| Contrôle de wording AMF sur contenu agrégé | Syntheza n'écrit pas de recommandation, mais affiche des titres tiers (Nasdaq/Zacks, Seeking Alpha, ETF Trends, BFM Conseils) | Drapeau "contient une recommandation" (lexique buy/sell/achat/vendre/objectif de cours) pour le masquer ou l'afficher sans mise en avant | P1 | Facile (lexique) |

## F. Types d'événements manquants (gestion d'actifs)

Constats de précision sur `signal.ts` :
- `M_AND_A` : `rachat\w*` matche "rachat d'actions" (buyback, pas M&A), "rachat de dette". Séparer.
- `EARNINGS` : `guidance` matche "forward guidance" (banques centrales). Lexique manque `résultat net`, `résultat opérationnel`, `free cash flow`, `operating profit`, `carnet de commandes`, `T3`/`Q3`/`third quarter`, `publie ses résultats`, `relève (ses )?objectifs`, `abaisse (sa|ses)`, `résultats du (premier|deuxième|troisième|quatrième|\d)`.
- `RATINGS` : manquent `upgrade\w*`, `abaisse (la|sa) note`, `relève (la|sa) note`, `confirme la note`, `affirm\w* (the )?rating`, `perspective (stable|positive)`, `credit watch`, `mise sous surveillance`, `Scope Ratings`, `DBRS`. `degrade\w*` et `rehausse\w*` matchent hors notation ("dégradé", "rehaussé le plafond").
- `CENTRAL_BANK` : manquent `Banque de France`, `Bundesbank`, `dot plot`, `minutes`, `Beige Book`, `Livre beige`, `Jackson Hole`, `taux de dépôt`, `deposit facility`, `resserrement`, `pivot monétaire`, `bilan de la banque centrale`.
- `MACRO` : `nonfarm` seul : "non-farm payrolls" normalisé donne "non farm" non reconnu ; manquent `non farm payrolls`, `jobs report`, `rapport sur l'emploi`, `taux de chômage`, `inflation sous-jacente`, `core inflation`, `confiance des consommateurs`, `consumer confidence`, `production industrielle`, `industrial production`, `balance courante`, `courbe des taux`, `yield curve`, `budget`, `plafond de la dette`, `debt ceiling`, `shutdown`.
- `INCIDENT` : `strikes?` (grève) matche "strikes a deal" en anglais (accord) : retirer `strikes?` seul, garder `workers strike`, `strike action`, `grève`.

Nouveaux types proposés :

| Type | Exemples de titres | Lexique FR | Lexique EN |
|---|---|---|---|
| `ESG_SUSTAINABILITY` (ou `ESG_REGULATION`) | "L'AMF sanctionne une société de gestion pour greenwashing" ; "ESMA publishes final guidelines on ESG fund names" | SFDR, taxonomie verte, greenwashing, écoblanchiment, CSRD, CSDDD, label ISR, fonds Article 8, fonds Article 9, durabilité, ESG, devoir de vigilance, bilan carbone | SFDR, EU taxonomy, greenwashing, CSRD, CSDDD, ESG fund names, net zero, climate disclosure, sustainability reporting |
| `FUNDS_FLOWS` | "Les fonds actions européens subissent leur plus forte décollecte depuis 2022" ; "Spot bitcoin ETFs see record inflows" | collecte nette, décollecte, rachats massifs, encours, souscriptions nettes, fermeture du fonds, suspension des rachats, gel des rachats, liquidité du fonds | net inflows, net outflows, fund flows, record inflows, redemptions, gated, suspension of redemptions, assets under management, AUM |
| `CAPITAL_RETURN` (dividendes, rachats d'actions) | "Total relève son dividende et son programme de rachat d'actions" | dividende, acompte sur dividende, rachat d'actions, programme de rachat, détachement du coupon, coupon | dividend, share buyback, buyback programme, special dividend, payout |
| `ANALYST_RATINGS` (avis d'analystes, sans recommandation) | "Goldman relève son objectif de cours sur LVMH" | objectif de cours, relève son objectif, abaisse son objectif, recommandation, analystes | price target, raises target, cuts target, upgrade, downgrade, initiates coverage (afficher en tant qu'information de marché, sans verbe d'achat) |
| `POLITICAL_RISK` (élections, budget, shutdown) | "Motion de censure : le gouvernement tombe" ; "US government shutdown looms" | élections, référendum, motion de censure, dissolution, budget, plafond de la dette, 49.3, instabilité politique, remaniement | election, referendum, no-confidence vote, shutdown, debt ceiling, snap election, coalition collapse |
| `IPO_LISTING` (séparé de M_AND_A) | "Introduction en bourse de X sur Euronext" | introduction en bourse, cotation, retrait de cote, OPRA, relance de l'offre | IPO, direct listing, delisting, SPAC |

---

# PARTIE 2 : RISQUE & CONFORMITÉ

## A. Persona

Qui : responsable conformité (CCO) / RCSI, responsable LCB-FT / sanctions, risk manager (banque, assurance, société de gestion), analyste risque pays, responsable cyber/risque opérationnel (RSSI en lien avec la 2e ligne), assureur crédit / risque politique, auditeur interne. Souvent 1 à 10 personnes ; budget conformité plus élevé que front-office : prêts à payer pour de la couverture exhaustive et de la preuve.

Décisions : bloquer/geler une relation ou un paiement (sanctions, gel des avoirs), escalader une alerte à la direction/au comité des risques, réduire une exposition sur une contrepartie/un pays, déclarer (Tracfin, déclaration d'incident au régulateur, notification CNIL sous 72 h, DORA incident reporting), mettre à jour une politique (nouvelle règle AMF/ACPR/ESMA/EBA/FinCEN), activer le PCA après un incident cyber/ opérationnel chez un fournisseur critique, déclasser un pays (risque pays).

Ce qui les fait bouger :
- Sanctions : nouveaux paquets UE (Conseil UE), OFAC/US (SDN, general licenses), UK OFSI, ONU (Conseil de sécurité), gel des avoirs, listes d'entités, contrôles à l'export (BIS-Commerce).
- Actions de répression : amendes AMF/ACPR/SEC/FCA/DOJ/OCC/FINRA, CJIP, consent orders, accords de poursuites différées, enquêtes ouvertes, procédures de l'Autorité de la concurrence, CNIL.
- Nouvelle réglementation : textes UE (Journal officiel L/C), consultations EBA/ESMA/EIOPA, AMLA, DORA, MiCA, Bâle III, FSB, FATF (listes grise/noire : juin, octobre, février).
- Cyber : alertes CERT-FR/CISA/NCSC, vulnérabilités activement exploitées, ransomware touchant une contrepartie ou un fournisseur, fuites de données.
- Défaillances : faillites, redressements, défauts souverains, risque de contrepartie.
- Risque pays : conflits, coups d'État, élections, sanctions, avis aux voyageurs (FCDO, Département d'État), instabilité.

Journée type :
- Pré-ouverture (7 h-9 h) : revue des alertes de la nuit (sanctions Asie/US, cyber), nouveaux textes publiés, items "fiables" vs rumeurs.
- Journée : surveillance de fil continu, vérification de contreparties, traitement des alertes ; réponse rapide à toute sanction nouvelle.
- Clôture : synthèse quotidienne à la direction (Brief), consignation des décisions (Journal).
- Hebdomadaire : veille réglementaire, comité conformité, mise à jour du registre des sanctions/pays à risque.
- Trimestriel : rapport au comité des risques, revue des politiques, preuve de veille pour le régulateur (traçabilité).

Ce qui ferait payer : couverture exhaustive des régulateurs et sources d'enforcement (FR/UE/US/UK), horodatage défendable, classification par type d'événement fiable (sanction vs amende vs consultation), alertes par contrepartie (liste de noms) avec exclusion des homonymes, export d'audit, notification e-mail, discrimination rumeur vs source primaire.

## B. Sources

### B.1 Preset actuel `RISK_COMPLIANCE` (11 slugs)

Slugs : `amf`, `esma`, `eba`, `sec`, `conseil-ue`, `un-news`, `cert-fr`, `cisa`, `bleepingcomputer`, `financial-times`, `al-jazeera`.
- Garder : tous. `al-jazeera` et `financial-times` sont généralistes (bruit) mais utiles pour le risque pays ; à déclarer "optionnel".
- Retirer ou ne pas par défaut : rien d'inadéquat.
- Manque : régulateurs/enforcers (FCA, DOJ, FINRA, OCC, Fed enforcement, CSSF, FINMA, SRB, FSB, EDPB, CNIL, ECB supervision), UK (OFSI, HMT, SFO, NCA, NCSC), sanctions et droit UE (Commission européenne, EUR-Lex), cyber (Krebs, SecurityWeek, The Record, Schneier, LeMagIT, Silicon, ZDNet FR...), compliance (Risk.net, Compliance Week, OCCRP, ICIJ), risque pays (FCDO, Département d'État, ReliefWeb, Crisis Group, IRIS, RFI, France 24, UN News FR).

### B.2 Nouvelles sources vérifiées

| Nom | slug | domaine | catégorie | trust | feeds | items | âge |
|---|---|---|---|---|---|---|---|
| Federal Reserve : sanctions et décisions d'application | federal-reserve (étend) | federalreserve.gov | INSTITUTIONS | 96 | https://www.federalreserve.gov/feeds/press_enforcement.xml ; https://www.federalreserve.gov/feeds/press_orders.xml ; https://www.federalreserve.gov/feeds/press_other.xml | 15 chacun | 1,8 j ; 4,8 j ; 1,1 j |
| SEC : enforcement | sec (étend) | sec.gov | INSTITUTIONS | 95 | https://www.sec.gov/enforcement-litigation/litigation-releases/rss ; https://www.sec.gov/enforcement-litigation/administrative-proceedings/rss | 25 / 25 | 1,0 j / 1,1 j |
| SEC : suspensions de négociation | sec (étend) | sec.gov | INSTITUTIONS | 95 | https://www.sec.gov/enforcement-litigation/trading-suspensions/rss | 25 | 120,6 j : hors tolérance 30 j, flux très rare ; à ajouter seulement pour l'exhaustivité (marqué basse fréquence) |
| US DOJ : communiqués | doj | justice.gov | INSTITUTIONS | 92 | https://www.justice.gov/news/rss?type=press_release | 25 | 1,2 j |
| FINRA | finra | finra.org | INSTITUTIONS | 90 | https://www.finra.org/rss.xml | 10 | 0,8 j |
| OCC (US) | occ | occ.gov | INSTITUTIONS | 92 | https://www.occ.gov/rss/occ_news.xml | 10 | 1,8 j (occ_bulletins 10,0 j) |
| CFPB | cfpb | consumerfinance.gov | INSTITUTIONS | 90 | https://www.consumerfinance.gov/about-us/newsroom/feed/ | 22 | 8,5 j |
| Federal Register (OFAC, FinCEN, SEC, CFTC, BIS-Commerce, OCC) | federal-register | federalregister.gov | INSTITUTIONS | 94 | https://www.federalregister.gov/api/v1/documents.rss?conditions%5Bagencies%5D%5B%5D=foreign-assets-control-office ; ...=financial-crimes-enforcement-network ; ...=securities-and-exchange-commission ; ...=commodity-futures-trading-commission ; ...=industry-and-security-bureau ; ...=comptroller-of-the-currency ; https://www.federalregister.gov/api/v1/documents.rss?conditions%5Bterm%5D=sanctions | 42 / 3 / 200 / 5 / 7 / 4 / 151 | OFAC et SEC : documents datés à venir (public inspection, âge négatif -2,5 j) ; FinCEN 4,5 j ; BIS 10,5 j |
| UK OFSI | ofsi-uk | gov.uk | INSTITUTIONS | 95 | https://www.gov.uk/government/organisations/office-of-financial-sanctions-implementation.atom | 20 | 1,3 j |
| HM Treasury | hm-treasury | gov.uk | INSTITUTIONS | 94 | https://www.gov.uk/government/organisations/hm-treasury.atom | 20 | 2,7 j |
| UK FCA : actualités (RSS natif) | fca-uk | fca.org.uk | INSTITUTIONS | 95 | https://www.fca.org.uk/news/rss.xml | 20 | date non parsable ("Friday, October 9, 2026 - 12:06" = vendredi, donc 1 j) : parseur de dates à fixer d'abord |
| UK FCA : activité GOV.UK | fca-uk (étend) | gov.uk | INSTITUTIONS | 95 | https://www.gov.uk/government/organisations/financial-conduct-authority.atom | 20 | 15,2 j (basse fréquence) |
| UK Serious Fraud Office | sfo-uk | gov.uk | INSTITUTIONS | 92 | https://www.gov.uk/government/organisations/serious-fraud-office.atom | 20 | 1,2 j |
| UK National Crime Agency | nca-uk | nationalcrimeagency.gov.uk | INSTITUTIONS | 92 | https://www.nationalcrimeagency.gov.uk/news?format=feed&type=rss | 20 | 2,0 j |
| UK NCSC | ncsc-uk | ncsc.gov.uk | CYBER | 93 | https://www.ncsc.gov.uk/api/1/services/v1/all-rss-feed.xml ; https://www.ncsc.gov.uk/api/1/services/v1/news-rss-feed.xml | 20 / 20 | 2,2 j |
| UK CMA | cma-uk | gov.uk | INSTITUTIONS | 92 | https://www.gov.uk/government/organisations/competition-and-markets-authority.atom | 20 | 1,2 j |
| UK Insolvency Service | insolvency-service-uk | gov.uk | INSTITUTIONS | 90 | https://www.gov.uk/government/organisations/insolvency-service.atom | 20 | 1,2 j |
| UK HMRC | hmrc | gov.uk | INSTITUTIONS | 90 | https://www.gov.uk/government/organisations/hm-revenue-customs.atom | 20 | 1,0 j |
| UK FCDO : actualités | fcdo-uk | gov.uk | INSTITUTIONS | 92 | https://www.gov.uk/government/organisations/foreign-commonwealth-development-office.atom | 20 | 0,0 j |
| UK FCDO : conseils aux voyageurs (risque pays) | fcdo-travel-advice | gov.uk | INSTITUTIONS | 92 | https://www.gov.uk/foreign-travel-advice.atom | 20 | 0,0 j |
| US State Dept : travel advisories | state-travel-advisories | travel.state.gov | INSTITUTIONS | 92 | https://travel.state.gov/_res/rss/TAsTWs.xml | 223 | 25,8 j (flux d'avis par pays, date = dernière révision, basse fréquence) |
| UK Payment Systems Regulator | psr-uk | gov.uk | INSTITUTIONS | 88 | https://www.gov.uk/government/organisations/payment-systems-regulator.atom | 16 | 15,2 j |
| Commission européenne : salle de presse | commission-europeenne | ec.europa.eu | INSTITUTIONS | 94 | https://ec.europa.eu/commission/presscorner/api/rss?language=fr ; ...=en | 10 | 1,0 j |
| EUR-Lex : législation UE | eur-lex | eur-lex.europa.eu | INSTITUTIONS | 96 | https://eur-lex.europa.eu/EN/display-feed.rss?rssId=162 ; https://eur-lex.europa.eu/EN/display-feed.rss?rssId=222 | 100 / 100 | 1,8 j |
| EBA | eba (existant) | eba.europa.eu | INSTITUTIONS | 95 | https://www.eba.europa.eu/rss.xml | 10 | 1,0 j (ok) |
| ESRB | esrb | esrb.europa.eu | INSTITUTIONS | 94 | https://www.esrb.europa.eu/rss/press.xml | 15 | 8,4 j (basse fréquence) |
| Single Resolution Board | srb | srb.europa.eu | INSTITUTIONS | 94 | https://www.srb.europa.eu/rss | 10 | 1,4 j |
| FSB | fsb | fsb.org | INSTITUTIONS | 95 | https://www.fsb.org/feed/ | 10 | 1,4 j |
| CSSF | cssf | cssf.lu | INSTITUTIONS | 92 | https://www.cssf.lu/en/feed/ | 10 | 2,1 j |
| FINMA | finma | finma.ch | INSTITUTIONS | 93 | https://www.finma.ch/en/rss/news/ | 50 | 2,2 j |
| SFC Hong Kong | sfc-hk | sfc.hk | INSTITUTIONS | 90 | https://www.sfc.hk/en/RSS-Feeds/Press-releases | 7 | 1,6 j |
| EDPB | edpb | edpb.europa.eu | INSTITUTIONS | 94 | https://www.edpb.europa.eu/feed/news_en | 10 | 1,2 j |
| CNIL | cnil | cnil.fr | INSTITUTIONS | 94 | https://www.cnil.fr/fr/rss.xml | 10 | 1,2 j |
| Autorité de la concurrence | autorite-concurrence | autoritedelaconcurrence.fr | INSTITUTIONS | 94 | https://www.autoritedelaconcurrence.fr/fr/rss.xml | 10 | 16,1 j (basse fréquence, < 30 j) |
| DGCCRF | dgccrf | economie.gouv.fr | INSTITUTIONS | 92 | https://www.economie.gouv.fr/dgccrf/rss | 20 | 1,1 j |
| Tracfin | tracfin | economie.gouv.fr | INSTITUTIONS | 94 | https://www.economie.gouv.fr/tracfin/rss | 10 | 44,0 j : hors tolérance 30 j ; flux très rare mais source LCB-FT centrale : à ajouter, signalé basse fréquence |
| AMF (flux complémentaires) | amf (étend) | amf-france.org | INSTITUTIONS | 95 | https://www.amf-france.org/fr/flux-rss/display/23 (communiqués, 8,4 j) ; display/24 (sanctions et transactions, 79,2 j) ; display/28 (alertes et mises en garde, 52,3 j) ; display/25 (publications, 1,1 j) ; display/30 (actualités, 1,1 j) | 200 chacun | cf. âges |
| ECB banking supervision | bce-ecb (étend) | bankingsupervision.europa.eu | INSTITUTIONS | 95 | https://www.bankingsupervision.europa.eu/rss/press.html | 15 | 3,4 j |
| BIS : Comité de Bâle et papiers | bis (étend) | bis.org | INSTITUTIONS | 94 | https://www.bis.org/doclist/bispapers.rss | 25 | 2,7 j |
| CISA (compléments) | cisa (étend) | cisa.gov | CYBER | 96 | https://www.cisa.gov/news.xml ; https://www.cisa.gov/cybersecurity-advisories/ics-advisories.xml ; https://www.cisa.gov/cybersecurity-advisories/alerts.xml ; https://www.cisa.gov/uscert/ncas/alerts.xml | 10 / 30 / 30 / 10 | 2,2 j ; 2,2 j ; 6,2 j ; 4,0 j |
| CERT-FR (compléments) | cert-fr (étend) | cert.ssi.gouv.fr | CYBER | 96 | https://www.cert.ssi.gouv.fr/avis/feed/ ; .../alerte/feed/ ; .../actualite/feed/ ; .../cti/feed/ | 40 chacun | 1,7 j ; 12,7 j ; 5,7 j ; 8,7 j |
| Krebs on Security | krebs | krebsonsecurity.com | CYBER | 82 | https://krebsonsecurity.com/feed/ | 10 | 0,7 j |
| SecurityWeek | securityweek | securityweek.com | CYBER | 78 | https://www.securityweek.com/feed/ | 10 | 0,2 j |
| The Hacker News | the-hacker-news | thehackernews.com | CYBER | 72 | https://thehackernews.com/feeds/posts/default | 50 | 0,2 j |
| The Record (Recorded Future News) | the-record | therecord.media | CYBER | 80 | https://therecord.media/feed | 5 | 0,9 j |
| Dark Reading | dark-reading | darkreading.com | CYBER | 76 | https://www.darkreading.com/rss.xml | 50 | 0,8 j |
| Schneier on Security | schneier | schneier.com | CYBER | 76 | https://www.schneier.com/feed/atom/ | 10 | 0,8 j |
| Help Net Security | help-net-security | helpnetsecurity.com | CYBER | 74 | https://www.helpnetsecurity.com/feed/ | 10 | 1,2 j |
| Infosecurity Magazine | infosecurity-magazine | infosecurity-magazine.com | CYBER | 74 | https://www.infosecurity-magazine.com/rss/news/ | 250 | 1,0 j |
| CSO Online | cso-online | csoonline.com | CYBER | 74 | https://www.csoonline.com/feed/ | 20 | 1,0 j |
| The Register (sécurité) | the-register-security | theregister.com | CYBER | 74 | https://www.theregister.com/security/headlines.atom | 50 | 0,2 j |
| DataBreaches.net | databreaches-net | databreaches.net | CYBER | 68 | https://www.databreaches.net/feed/ | 10 | 0,2 j |
| Have I Been Pwned : brèches | have-i-been-pwned | haveibeenpwned.com | CYBER | 78 | https://haveibeenpwned.com/feed/breaches/ | 20 | 1,4 j |
| Ransomware.live | ransomware-live | ransomware.live | CYBER | 60 | https://www.ransomware.live/rss.xml | 200 | 0,0 j (revendications des groupes de rançongiciels : donnée non vérifiée, à traiter comme rumeur) |
| Cisco Talos | cisco-talos | talosintelligence.com | CYBER | 82 | https://blog.talosintelligence.com/rss/ | 15 | 1,9 j |
| Unit 42 (Palo Alto) | unit42 | unit42.paloaltonetworks.com | CYBER | 80 | https://unit42.paloaltonetworks.com/feed/ | 15 | 2,8 j |
| CrowdStrike | crowdstrike | crowdstrike.com | CYBER | 78 | https://www.crowdstrike.com/blog/feed/ | 10 | 2,5 j |
| Microsoft Security Blog | microsoft-security | microsoft.com | CYBER | 82 | https://www.microsoft.com/en-us/security/blog/feed/ | 10 | 1,8 j |
| WeLiveSecurity (ESET) | welivesecurity | welivesecurity.com | CYBER | 78 | https://www.welivesecurity.com/en/rss/feed/ | 100 | 2,3 j |
| LeMagIT | lemagit | lemagit.fr | CYBER | 80 | https://www.lemagit.fr/rss/ContentSyndication.xml | 20 | 1,1 j |
| Silicon.fr | silicon-fr | silicon.fr | CYBER | 74 | https://www.silicon.fr/feed | 50 | 1,1 j |
| ZDNet France | zdnet-fr | zdnet.fr | CYBER | 74 | https://www.zdnet.fr/feeds/rss/actualites/ | 50 | 0,2 j |
| Numerama : cyberguerre | numerama-cyberguerre | numerama.com | CYBER | 74 | https://www.numerama.com/cyberguerre/feed/ | 15 | 0,3 j |
| ZATAZ | zataz | zataz.com | CYBER | 65 | https://www.zataz.com/feed/ | 10 | 2,7 j |
| 01net | zero-un-net | 01net.com | CYBER | 68 | https://www.01net.com/feed/ | 30 | 0,1 j |
| Risk.net | risk-net | risk.net | FINANCIAL_PRESS | 78 | https://www.risk.net/feeds/rss | 50 | 0,9 j |
| Compliance Week | compliance-week | complianceweek.com | FINANCIAL_PRESS | 72 | https://www.complianceweek.com/rss | 10 | 0,7 j |
| Corporate Compliance Insights | corporate-compliance-insights | corporatecomplianceinsights.com | FINANCIAL_PRESS | 68 | https://www.corporatecomplianceinsights.com/feed/ | 10 | 1,2 j |
| OCCRP (crime organisé et corruption) | occrp | occrp.org | GENERAL_PRESS | 80 | https://www.occrp.org/en/feed | 60 | 1,0 j |
| ICIJ | icij | icij.org | GENERAL_PRESS | 85 | https://www.icij.org/feed/ | 11 | 1,1 j |
| ReliefWeb | reliefweb | reliefweb.int | INSTITUTIONS | 85 | https://reliefweb.int/updates/rss.xml ; https://reliefweb.int/updates/rss.xml?search=sanctions | 20 / 20 | 0,3 j / 3,0 j |
| International Crisis Group | crisis-group | crisisgroup.org | INSTITUTIONS | 85 | https://www.crisisgroup.org/rss.xml | 10 | 8,8 j (basse fréquence) ; l'autre flux `/rss` a une date texte non parsable |
| UN News (compléments FR et justice) | un-news (étend) | news.un.org | INSTITUTIONS | 92 | https://news.un.org/feed/subscribe/fr/news/all/rss.xml ; https://news.un.org/feed/subscribe/en/news/topic/law-and-crime-prevention/feed/rss.xml ; https://news.un.org/feed/subscribe/en/news/topic/economic-development/feed/rss.xml | 30 chacun | 1,2 j ; 1,2 j ; 8,2 j |
| RFI | rfi | rfi.fr | GENERAL_PRESS | 80 | https://www.rfi.fr/fr/rss | 23 | 0,0 j |
| France 24 (généraliste) | france-24 (étend) | france24.com | GENERAL_PRESS | 82 | https://www.france24.com/fr/rss | 24 | 0,0 j |
| IRIS | iris | iris-france.org | GENERAL_PRESS | 75 | https://www.iris-france.org/feed/ | 10 | 0,3 j |
| Foreign Policy | foreign-policy | foreignpolicy.com | GENERAL_PRESS | 74 | https://foreignpolicy.com/feed/ | 25 | 0,8 j |
| WTO (droits de douane, différends) | wto | wto.org | INSTITUTIONS | 92 | https://www.wto.org/library/rss/latest_news_e.xml | 10 | 2,7 j |
| FT : cybersécurité, banques, banques centrales | financial-times (étend) | ft.com | FINANCIAL_PRESS | 88 | https://www.ft.com/cyber-security?format=rss ; https://www.ft.com/banks?format=rss ; https://www.ft.com/central-banks?format=rss | 25 chacun | 1,2 j ; 1,2 j ; 1,1 j |
| Politico Europe | politico-eu | politico.eu | GENERAL_PRESS | 80 | https://www.politico.eu/feed/ | 10 | 0,0 j |

Remarques :
- Note sur les dates : le samedi, `Federal Register` OFAC/SEC remonte des documents dont la date est dans le futur (publication prévue lundi). Impact : le filtre `publishedAt >= since` passe, mais la liste "récente" affichera des horodatages futurs : à plafonner à `ingestedAt`.
- Dépôts de documents "ofac-recent-actions" via le Federal Register sont les seuls textes OFAC machine-lisibles : ils couvrent les règlements (31 CFR) et avis, pas les ajouts SDN du jour.
- FCA : l'URL `https://www.fca.org.uk/news/rss.xml` a `pubDate` texte, non parsable : corriger l'ingestion d'abord.
- ESMA (`https://www.esma.europa.eu/rss.xml`), Conseil UE et Europol (flux déjà au catalogue pour les deux premiers) : items sans date. Europol : OK structurellement mais sans date, ne pas ajouter avant correctif.

### B.3 Rejetés

| Candidat | Raison |
|---|---|
| OFAC RSS natif, Treasury press releases, FinCEN RSS natif, FATF/GAFI, CFTC | RSS retiré (OFAC, 6 févr. 2025) ou 403/404 : voir partie 1 B.3. |
| ACPR, Banque de France, Tracfin via site ACPR, Trésor (sanctions financières internationales), Euractiv, ENISA, EIOPA, AMLA | 403/404/flux invalide. ACPR : la source n°1 de la conformité FR est inaccessible : seul recours = AMF + presse. |
| Interpol | Timeout 8 s (limite du fetcher). |
| BIS-Commerce natif (`bis.doc.gov`), State Dept (`state.gov/rss-feed`), Commerce | 404/introuvable. |
| CERT-FR `ioc` | 821 j. |
| NCSC UK `report-rss-feed` | 521 j. |
| `gov.uk` ICO | 87 j (basse fréquence, hors tolérance). |
| `gov.uk` Financial Reporting Council | 86 j. |
| `gov.uk` The Pensions Regulator | 58 j. |
| `gov.uk` NCSC activité | 89 j. |
| `gov.uk` Prudential Regulation Authority | introuvable. |
| `bankofengland.co.uk/rss/events` | dates dans le futur (agenda, -162 j) : à traiter comme agenda, pas news. |
| Mandiant, Google Threat Intelligence, MSRC, Sanctionscanner, Risk screen, ACAMS/moneylaundering.com, Transparency International, Global Witness, ACLED | 404 / pas de flux / 403. |
| Eurostat data updates | bruit (voir partie 1). |

## C. Actifs et mots-clés (watchlist par défaut, 28 entités)

Problèmes de précision du gabarit actuel :
- `Sanctions` (THEME) contient `sanctions` (très générique : sanctions sportives, disciplinaires, sanctions d'une entreprise contre un salarié) et `SDN` (3 majuscules sensible à la casse ; en français aussi "SDN" = Syndicat/sans domicile ? peu courant mais "SDN" = Société des nations) ; `OFAC` ok. Ajouter `sanctions économiques`, `sanctions financières`, `paquet de sanctions`.
- `Régulateurs financiers` : `SEC` 3 majuscules sensible à la casse OK ; mais `SEC` en français ("sec" = dry) est insensible : la règle casse sensible empêche la plupart des faux positifs sauf titres en MAJUSCULES. `FCA` (ok) ; `AMF` ok.
- `Cyberattaques` : correct ; manquent `vulnérabilité`, `zero-day`, `CVE`, `phishing`, `DDoS`, `compromission`, `exfiltration`.
- `Russie` : `Moscou`, `Kremlin`, `Poutine` ok ; `Russia` ok ; mais le terme `Kremlin` ressort pour "Kremlin-Bicêtre" (ville du Val-de-Marne) : mot entier mais les deux mots seraient séparés par un tiret normalisé en espace : "kremlin bicetre" contient "kremlin" mot entier = faux positif. Garder `Kremlin` avec exclusion `Kremlin-Bicêtre`.
- `Chine / Taïwan` : label mixte ; séparer en `Chine` et `Taïwan`. `Pékin`/`Beijing` ok ; manque `Chine`, `China`, `Xi Jinping`, `PBOC` (3-4 majuscules ok).
- `Moyen-Orient` : mélange pays/zones ; `Iran`, `Israël`, `Gaza`, `Red Sea` ok ; `Israel` insensible ; `Iran` mot entier ("Iran" ok). 
- `Faillites` : `faillite`/`faillites` ok ; `défaut de paiement` ok ; `insolvency` ok.
- `Blanchiment & fraude` : `fraude` générique (fraude fiscale, fraude au bac...) ; `fraud` ; `blanchiment` ok mais PAS dans le lexique d'événements (cf. F).
- Absent du gabarit : Corée du Nord/Iran (prolifération), Venezuela, Ukraine, Turquie, Afrique sahélienne (coups d'État), listes FATF, pays "à risque" à suivre (risque pays), PCA/ fournisseurs critiques, DORA/ICT tiers, DPA/RGPD, corruption (Sapin II/FCPA), Tracfin, KYC, crypto-actifs/MiCA.

Watchlist proposée :

| Label | Kind | Symbol | Mots-clés (FR + EN) | Faux positifs / exclusions |
|---|---|---|---|---|
| Sanctions internationales | THEME | null | sanctions économiques, sanctions financières, paquet de sanctions, sanctions européennes, sanctions américaines, gel des avoirs, liste noire, asset freeze, sanctions package, sanctions list, OFAC, SDN list, entity list, export controls, contrôles à l'export | `sanctions` seul INTERDIT (sportives, disciplinaires) ; `OFAC` ok |
| OFAC / Trésor US (sanctions) | INSTITUTION | null | OFAC, Office of Foreign Assets Control, Trésor américain, US Treasury sanctions | `Treasury` seul = rendement |
| OFSI / UE (sanctions) | INSTITUTION | null | OFSI, sanctions de l'UE, EU sanctions, Conseil de l'UE, restrictive measures, mesures restrictives | aucun |
| Régulateurs FR/UE | INSTITUTION | null | AMF, ACPR, Autorité des marchés financiers, ESMA, EBA, EIOPA, AMLA, CNIL, DGCCRF, Tracfin, Autorité de la concurrence | `AMF` ok ; `Tracfin` unique |
| Régulateurs US/UK | INSTITUTION | null | SEC, CFTC, FINRA, OCC, FinCEN, FCA, PRA, Serious Fraud Office, DOJ | `SEC` majuscules ; `FCA` ok ; `PRA` 3 majuscules = "Pra" (rare) ok |
| Enforcement et amendes | THEME | null | amende record, sanction pécuniaire, commission des sanctions, mise en demeure, consent order, deferred prosecution agreement, CJIP, convention judiciaire d'intérêt public, settlement, transaction homologuée | `settlement` mot courant (colonies, règlement) ; ajouter `SEC settlement` si besoin |
| Blanchiment et LCB-FT | THEME | null | blanchiment, blanchiment d'argent, LCB-FT, LCB FT, money laundering, AML, KYC, financement du terrorisme, terrorist financing, FATF, GAFI, liste grise, liste noire du GAFI, grey list | `fraude` seul interdit ; `AML` et `KYC` 3 majuscules ok ; `GAFI` 4 majuscules ok |
| Corruption et délit d'initié | THEME | null | corruption, pot-de-vin, trafic d'influence, délit d'initié, manipulation de cours, insider trading, market abuse, bribery, Sapin II, FCPA | `corruption` seul ok ; `pot de vin` normalisé |
| Cyberattaques | THEME | null | cyberattaque, cyberattaques, ransomware, rançongiciel, fuite de données, data breach, cyberattack, attaque informatique, piratage, phishing, DDoS, zero-day, vulnérabilité critique, CVE, exfiltration | `piratage` aussi "piratage de films" ; ok ; `CVE` 3 majuscules ok |
| Risque tiers critique / ICT | THEME | null | DORA, prestataire critique, fournisseur critique, supply chain attack, attaque sur la chaîne d'approvisionnement, panne informatique, cloud outage, AWS, Azure | `DORA` 4 majuscules (prénom Dora) ; `AWS` ok |
| Protection des données | THEME | null | RGPD, GDPR, CNIL, amende CNIL, EDPB, violation de données, data protection | `RGPD`/`GDPR` ok |
| Défaillances et faillites | THEME | null | faillite, faillites, redressement judiciaire, liquidation judiciaire, procédure de sauvegarde, défaut de paiement, bankruptcy, chapter 11, insolvency, default, restructuration de dette | `licenciements` appartient à un autre type (cf. F) ; `default` mot courant (par défaut) : retirer seul |
| Banques en difficulté / résolution | THEME | null | résolution bancaire, Single Resolution Board, SRB, recapitalisation, bank run, run bancaire, sauvetage bancaire, bailout, bail-in | `SRB` 3 majuscules ok |
| Défaut souverain / dette | THEME | null | défaut souverain, sovereign default, restructuration de la dette, Club de Paris, FMI, IMF programme, dette souveraine, spread souverain | `FMI` 3 majuscules ok ; `IMF` ok |
| Russie | COUNTRY | null | Russie, Russia, Moscou, Kremlin, Poutine, Putin, Ukraine, Kiev | `Kremlin` = Kremlin-Bicêtre ; `Kiev` ok ; `Poutine` (plat québécois) rare |
| Chine | COUNTRY | null | Chine, China, Pékin, Beijing, Xi Jinping, PBOC | `Chine` ok |
| Taïwan | COUNTRY | null | Taïwan, Taiwan, détroit de Taïwan, Taiwan Strait, TSMC | `Taiwan` insensible aux accents |
| Iran | COUNTRY | null | Iran, Téhéran, Tehran, Pasdaran, IRGC | `Iran` ok |
| Israël / Gaza | COUNTRY | null | Israël, Israel, Gaza, Hamas, Hezbollah, Cisjordanie, West Bank | aucun notable |
| Mer Rouge / détroits | THEME | null | mer Rouge, Red Sea, Houthis, Houthi, canal de Suez, Suez Canal, détroit d'Ormuz, Strait of Hormuz, Bab el-Mandeb | `Suez` : Suez (entreprise) : ajouter `canal de Suez` seulement |
| Corée du Nord | COUNTRY | null | Corée du Nord, North Korea, Pyongyang, Kim Jong Un | aucun |
| Venezuela | COUNTRY | null | Venezuela, Maduro, Caracas | aucun |
| Turquie | COUNTRY | null | Turquie, Turkey, Türkiye, Erdogan, livre turque | `Turkey` = dinde, "talking turkey" |
| Sahel / Afrique (risque pays) | COUNTRY | null | Mali, Niger, Burkina Faso, Sahel, coup d'État, junte, junta | `Mali` ok ; `Niger` ok ; `coup d'État` générique |
| États-Unis (politique, tarifs) | COUNTRY | null | droits de douane, tarifs douaniers, tariffs, Trump, shutdown, plafond de la dette, debt ceiling, Maison-Blanche, White House | `Trump` ok |
| Cryptoactifs (MiCA, FTX) | THEME | null | MiCA, crypto-actifs, cryptoactifs, stablecoin, Binance, Tether, FTX, SEC crypto | `MiCA` 4 caractères mixtes (sensible à la casse non appliqué) ; `Binance` ok |
| Grandes banques (contreparties) | COMPANY | null | BNP Paribas, Société Générale, Crédit Agricole, Deutsche Bank, Credit Suisse, UBS, HSBC, Barclays, JPMorgan, Goldman Sachs, Citigroup, Santander | `Credit Suisse` (absorbé par UBS) ; `UBS` 3 majuscules ok |

Remarque : pour un module "contreparties" propre au client, la watchlist devra accepter une liste de noms (import CSV) avec exclusions, car le gabarit ne peut pas connaître ses contreparties.

## D. Règles d'alerte et widgets

### D.1 Corrections du gabarit actuel
- `Nouvelles sanctions` : GEOPOLITICS_SANCTIONS = 30 de base ; minImpact MEDIUM (35) requiert une confirmation ou un mot de choc : mettre LOW. Risque de faux positifs : le type contient `guerre`, `conflit` (matche "conflit d'intérêts" : un titre de conformité classé géopolitique), `UN`, `OTAN`, `droits de douane`, `missile`. Pour des sanctions, préférer une règle par mots-clés (sanctions économiques, gel des avoirs, OFAC, SDN list) plutôt que le type d'événement seul.
- `Actions des régulateurs` : REGULATION_LEGAL = 20 → plancher implicite MEDIUM : aucune alerte jusqu'à 2+ sources ou mot de choc. Mettre `minImpact: 'LOW'`.
- `Cyberattaques - impact fort` : HIGH = 60 est rare (INCIDENT 25 + intensité 20 + BREAKING 15). Garder HIGH pour une règle "critique" et ajouter une règle MEDIUM sans entité de contrepartie.
- `Défaillances et faillites` : DEFAULT_RESTRUCTURING = 35 → MEDIUM atteint : correct, mais le type inclut `licenciements`, `plan social`, `layoffs`, `job cuts` : les plans sociaux déclenchent des alertes "défaut" : séparer.

### D.2 Gabarit proposé (10 règles)

| Nom | Actif ou mots-clés | Types d'événement | minImpact / minTrust |
|---|---|---|---|
| Nouvelles sanctions (primaires) | Sanctions internationales | GEOPOLITICS_SANCTIONS | LOW / trust 80 |
| Sanctions : toute source | mots-clés : gel des avoirs, asset freeze, OFAC, SDN list | tous | LOW / trust 0 |
| Actions des régulateurs | Régulateurs FR/UE ; Régulateurs US/UK | REGULATION_LEGAL | LOW / trust 80 |
| Amendes et enforcement | Enforcement et amendes | REGULATION_LEGAL | LOW / trust 80 |
| Blanchiment / LCB-FT | Blanchiment et LCB-FT | REGULATION_LEGAL, GEOPOLITICS_SANCTIONS | LOW / trust 70 |
| Cyberattaque critique | Cyberattaques | INCIDENT | HIGH / trust 60 |
| Cyberattaque ou vulnérabilité (veille) | Cyberattaques ; Risque tiers critique | INCIDENT | MEDIUM / trust 80 |
| Faillite d'une contrepartie | Défaillances et faillites ; Banques en difficulté | DEFAULT_RESTRUCTURING | MEDIUM / trust 60 |
| Défaut souverain / pays | Défaut souverain ; Russie ; Chine ; Moyen-Orient | DEFAULT_RESTRUCTURING, GEOPOLITICS_SANCTIONS | MEDIUM / trust 60 |
| Rumeur sur contrepartie | Grandes banques | tous (rumeur) | MEDIUM / sans floor de trust |

### D.3 Widgets (types existants)
Gabarit actuel : `ALERTS`, `RUMOR_RADAR`, `DECISION_JOURNAL`, `LIVE_WIRE`, `WATCHLIST_PULSE`, `BRIEFING` (tous w6) + `EVENT_MIX`, `SOURCE_HEALTH`. Proposition : `ALERTS` (w8, h2) en tête avec `BRIEFING` (w4, h2), puis `LIVE_WIRE` (w8, h2) filtré sur types REGULATION_LEGAL/GEOPOLITICS_SANCTIONS/INCIDENT + `RUMOR_RADAR` (w4, h2), puis `WATCHLIST_PULSE` (w6, h2) + `EVENT_MIX` (w6, h1) + `SOURCE_HEALTH` (w6, h1), enfin `DECISION_JOURNAL` (w12, h2). Pour un compliance officer, la traçabilité (Journal) est un livrable : placer le Journal visible, et l'export en priorité (E).

## E. Lacunes produit (personnalisation)

| Besoin | État actuel | Proposition | Prio | Faisabilité RSS-only, sans IA |
|---|---|---|---|---|
| Liste de contreparties/noms à surveiller (watchlist de personnes morales) avec exclusions d'homonymes | `WatchEntity` avec mots-clés ; pas d'exclusion | Import CSV de noms + `excludeKeywords` par entité ; indicateur "homonyme possible" sur items avec casse différente | P0 | Facile (déterministe) |
| Portée d'alerte "watchlist" et plancher d'impact explicite | cf. partie 1 (alertService.ts l.72-97) | idem | P0 | Facile |
| Parseur de dates tolérant (FCA, Crisis Group) et repli firstSeenAt (ESMA, Conseil UE, Europol) | `rssIngestionService.ts:33` | cf. constat 3 | P0 | Facile |
| Export d'audit (Journal, alertes, items sources avec horodatage et URL d'origine) | Journal en ligne ; export non confirmé | Export CSV/PDF signé (hash SHA-256 de l'extrait) : preuve de diligence pour le régulateur | P0 | Facile (hash, pas de signature qualifiée) |
| Preuve d'horodatage : date de première capture par Syntheza | `ingestedAt` en base (utilisé par alertService) | Afficher "vu par Syntheza à HH:MM" dans l'item, exportable | P1 | Facile |
| Notifications par e-mail/webhook/Slack/Teams, avec niveaux d'escalade | Notifications dans l'application seulement | E-mail instantané pour les alertes HIGH, résumé quotidien, webhook | P1 | Facile |
| Partage et délégation (équipe conformité, rôles) | Données par utilisateur | Dashboard partagé, journal d'équipe, assignation d'une alerte à un collègue | P1 | Moyen |
| Statut de l'alerte (nouveau, en cours, traité, faux positif) avec commentaire | Alerte lue/non lue (à confirmer) ; Journal à part | Workflow léger sur AlertEvent + lien avec Decision | P1 | Facile |
| Calendrier des échéances réglementaires | Aucun | Liste statique maintenue (FATF juin/octobre/février, entrées en vigueur UE) saisie manuellement dans le produit : pas de flux RSS | P2 | Partiel : contenu éditorial maison, pas de RSS |
| Filtre par pays / liste de pays à risque | Entités COUNTRY basiques | Étiquette "pays" calculée par lexique des noms de pays + filtre | P1 | Moyen (lexique de pays FR/EN + capitales, déterministe) |
| Filtres source primaire seulement | `minTrust` | Option "source primaire seulement" (catégorie INSTITUTIONS) | P1 | Facile |
| Détection de démentis et de corrections | Template MEDIA_COMMS a "Démentis" | Reprendre pour RC (démenti d'une sanction/d'un incident) | P2 | Facile (lexique) |
| Suppression des rediffusions et communiqués copiés | Story clustering existe | OK ; ajouter "première source" du cluster mise en avant | P1 | Facile |

## F. Types d'événements manquants (risque & conformité)

Constats sur `signal.ts` :
- `REGULATION_LEGAL` ne reconnaît ni `blanchiment`, `money laundering`, `LCB-FT`, `AML`, `KYC`, `corruption`, `fraude`, `Sapin II`, `FCPA`, `CJIP`, `sanction pécuniaire`, `commission des sanctions`, `mise en demeure`, `consent order`, `deferred prosecution`, `settlement`, `whistleblower`, `lanceur d'alerte`, `spoofing`, `DORA`, `MiCA`, `Bâle III`, `Basel III`, `Solvabilité II`, `EMIR`, `AMLD`, `UCITS`, `AIFM`, `SFDR`. Résultat : la watchlist RC contient `blanchiment`/`LCB-FT`/`fraude` comme mots de THEME mais l'item n'est classé dans aucun type, donc aucune alerte par type n'est possible sur ces sujets.
- `GEOPOLITICS_SANCTIONS` : manquent `gel des avoirs`, `asset freeze`, `liste noire`, `blacklist`, `SDN`, `entity list`, `export controls`, `contrôles à l'exportation`, `paquet de sanctions`, `sanctions package`, `dual use`, `double usage`, `élections`, `référendum`, `motion de censure`. `UN` (acronyme, sensible à la casse) peut matcher des titres en majuscules en français ("UN HOMME..."). `conflit\w*` matche "conflit d'intérêts" (compliance).
- `INCIDENT` : manquent `vulnérabilité`, `vulnerability`, `CVE`, `zero-day`, `0-day`, `phishing`, `hameçonnage`, `DDoS`, `compromission`, `compromised`, `exfiltration`, `espionnage`, `APT`, `supply chain attack`, `incident de sécurité`, `attaque informatique`, `extorsion`.
- `DEFAULT_RESTRUCTURING` mélange le crédit et le social (`licenciements`, `layoffs`, `plan social`, `suppressions de postes`, `job cuts`) : séparer en `CREDIT_EVENT` (faillite, défaut, redressement) et `LAYOFFS`. Manquent `cessation de paiements`, `impayé`, `covenant`, `moratoire`, `haircut`, `décote`, `bank run`, `résolution bancaire`, `bail-in`, `bailout`, `nationalisation`, `suspension des rachats`.

Nouveaux types proposés :

| Type | Exemples de titres | Lexique FR | Lexique EN |
|---|---|---|---|
| `AML_FINANCIAL_CRIME` | "La commission des sanctions de l'AMF inflige 3 M EUR d'amende" ; "FinCEN fines bank for AML failures" | blanchiment, blanchiment d'argent, LCB-FT, financement du terrorisme, corruption, pot-de-vin, trafic d'influence, fraude fiscale, CJIP, Sapin II, KYC | money laundering, AML, KYC, terrorist financing, bribery, FCPA, tax evasion, deferred prosecution agreement |
| `ENFORCEMENT_FINE` | "L'ACPR sanctionne une banque d'une amende de 5 M EUR" | amende, sanction pécuniaire, commission des sanctions, mise en demeure, transaction homologuée, sanctionne, blâme, interdiction d'exercer | fined, penalty, consent order, settlement, cease and desist, enforcement action |
| `CYBER_VULNERABILITY` (distinct d'`INCIDENT`) | "CISA ajoute 3 failles à son catalogue de vulnérabilités exploitées" | vulnérabilité, faille, zero-day, CVE, correctif, patch, exploitée activement | vulnerability, zero-day, CVE, actively exploited, patch, advisory |
| `DATA_PROTECTION` | "La CNIL sanctionne X pour manquements au RGPD" | RGPD, CNIL, violation de données, notification de violation, données personnelles | GDPR, data protection authority, personal data breach, DPA fine |
| `CREDIT_EVENT` | "Redressement judiciaire de Orpea" | faillite, défaut de paiement, redressement judiciaire, liquidation judiciaire, cessation de paiements, sauvegarde | bankruptcy, default, insolvency, chapter 11, missed payment |
| `LAYOFFS_RESTRUCTURING` | "Plan social chez X : 1 200 postes supprimés" | plan social, licenciements, suppressions de postes, PSE | layoffs, job cuts, restructuring plan |
| `POLITICAL_RISK` | "Coup d'État au Niger", "Élections au Brésil" | coup d'État, junte, élections, référendum, motion de censure, instabilité, manifestations, émeutes | coup, junta, election, referendum, unrest, protests, riots |
| `PRUDENTIAL_RESOLUTION` | "Le SRB lance la résolution de la banque X" | résolution bancaire, recapitalisation, run bancaire, renflouement, Bâle III, ratio de solvabilité, stress tests | bail-in, bailout, bank run, resolution, Basel III, stress test, capital ratio |
| `SANCTIONS_DESIGNATION` (extraction de `GEOPOLITICS_SANCTIONS`) | "L'UE adopte son 20e paquet de sanctions contre la Russie" | paquet de sanctions, gel des avoirs, liste noire, personnes visées, désignations | sanctions package, designations, SDN, asset freeze, entity list |

---

# Résumé exécutif

Top actions P0 (par ordre d'effort/impact) :
1. Corriger les gabarits d'alerte : ajouter `minImpact: 'LOW'` aux règles RATINGS/EARNINGS/REGULATION_LEGAL/GEOPOLITICS (sinon quasi muettes) ET une portée "watchlist" (sinon mondiales).
2. Parseur de dates tolérant + repli sur la date de première capture (FCA, Crisis Group, ESMA, Conseil UE, Europol).
3. Exclusions de mots-clés par entité/règle ; import CSV de noms.
4. Étendre le catalogue : ~75 nouvelles sources vérifiées (partie 1 B.2 et partie 2 B.2), dont les extras Fed/BCE/BoE/BRI, 14 flux FT thématiques, Les Echos (services.lesechos.fr, à retester depuis l'IP prod), AMF offres publiques, EDGAR SC TO/S-1, wires d'émetteurs, UK OFSI/HMT/SFO/NCA/NCSC, Federal Register (OFAC/FinCEN), DOJ/FINRA/OCC, CSSF/FINMA/SRB/FSB, EDPB/CNIL, cyber (Krebs, SecurityWeek, The Record, LeMagIT...), risque pays (FCDO, ReliefWeb, Crisis Group, IRIS).
5. Étendre `signal.ts` : lexiques RATINGS (upgrade, abaisse la note), REGULATION_LEGAL (blanchiment, AML, SFDR...), INCIDENT (vulnérabilité, zero-day), séparer rachat d'actions/M&A, licenciements/défaut ; ajouter ESG, FUNDS_FLOWS, AML_FINANCIAL_CRIME, POLITICAL_RISK.
