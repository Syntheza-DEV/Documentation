> Audit du 10/10/2026 (sources vérifiées en direct depuis un poste local ; l'hôte de prod en bloque certaines, voir centre-operations.md).

# Audit domaine : PDG / dirigeant + Finance d'entreprise

Method: code read (professions.ts, proSourceCatalog.ts, opsTypes.ts, signal.ts, schema.prisma, centre-operations.md), 4 batches of ~400 feed URLs run through `fetchFeed` (SSRF-safe prod fetcher) from the workstation on 2026-10-10 ~18:00 local, plus a classifier probe on 30 CEO-style headlines (`classifyEvents`, `compileTerms`, real CF template). The throwaway test `zz-audit-ceo.test.ts` and the URL lists are deleted. Caveat: fetched from the workstation, not from the OVH prod IP; sites that filter datacentre IPs may behave differently, so the first prod ingestion pass must be checked in SOURCE_HEALTH.

## DECISION: the CEO needs its own profession `DIRECTION_GENERALE`

Do NOT fold the CEO into CORPORATE_FINANCE. Evidence:

1. Different decision, different information diet. CF template (`professions.ts:302-368`) decision = "Couvrir le change ou les taux, réagir à une rumeur": 9 entities, all rates, FX, credit, M&A, tariffs, energy price. The CEO decides on strategy, exposure to a new law or tax, a social conflict, a supplier or logistics rupture, a competitor move, a crisis communication. Probe: of 30 CEO headlines (loi de finances 49.3, surtaxe IS, CSRD, SMIC, pénurie composants, démission PDG, boycott, Pillar Two, cotisations patronales, UE-Mercosur, mer Rouge), **17 get no event type at all and 0 match any CF entity** (only rates/BCE/M&A/tariffs headlines match).
2. Source overlap is ~25 %: CF preset (`professions.ts:111-129`) is central banks + US financial press + 3 FR financial outlets. The CEO preset is Parliament, Sénat, Cour des comptes, DGCCRF, Travail, Commission européenne, WTO, Les Echos/Le Monde/Figaro company desks, supply-chain press, press-release wires. Only ~8 slugs are shared.
3. Cost is low: `User.profession` and `Workspace.profession` are plain `String` (`schema.prisma:28`, `:521`), so no migration. Touchpoints: `PROFESSIONS` in `backend/src/types/opsTypes.ts:4` and `frontend-web/src/services/ops/opsTypes.ts:4`, `opsLabels.ts` label map, `SOURCE_PRESETS` + a template + `PROFESSION_TEMPLATES` in `professions.ts`, test fixture `frontend-web/src/routes/(app)/ops/reglages/sources.test.tsx:69`.
4. The CEO's most valuable entities (own company, competitors, clients, suppliers, sector) are user-specific, so the template is a skeleton and the onboarding must collect them (see gap G1). CF benefits from the same wizard ("mentions of the company").
5. Keep CORPORATE_FINANCE (CFO/treasurer) but fix it: it is a real distinct persona (rates, hedging, credit, issuance, ratings, IPO, accounting standards) and today its template is thin and has precision bugs (section C).

Proposed: `DIRECTION_GENERALE`, label "Direction générale", priority P1, audience "PDG, DG, comités de direction, secrétaires généraux, directeurs de la stratégie", decision "Anticiper un risque réglementaire, fiscal, social ou logistique, réagir à un mouvement d'un concurrent, d'un client ou d'un fournisseur, préparer la communication", criticalInfo "Lois et décrets, fiscalité, social, concurrents, chaîne d'approvisionnement, réputation, macro".

---

## A. Persona

**PDG / dirigeant** of an ETI or grand groupe (200 to 50 000 staff), France + export. Reads the Comex pack, rarely a terminal. Decides: pricing and investment under tax/regulatory change, hiring/PSE, M&A (target or being a target), supplier diversification, public positioning, crisis response, board messaging.
Information that moves them: Finance bill and amendments (PLF/PLFSS, 49.3, motion de censure), decrees in the Journal officiel, EU texts (CSRD, CSDDD, CBAM, AI Act, omnibus), tax (IS surtaxe, Pillar Two, CIR, TVA), labour (SMIC, NAO, assurance chômage, retraites, CGT/CFDT mobilisation, grèves), energy prices, tariffs and trade deals (US, China, Mercosur), sanctions, shipping routes (mer Rouge, Suez), shortages, competitor results/M&A/recalls/CEO changes, client or supplier insolvency (procédures collectives), cyberattack on a peer or subcontractor, press tone about the company, ratings of the sovereign and of peers, rates/FX only as macro backdrop.
Day: 7:15 pre-meeting brief on own company + competitors + political/regulatory agenda of the day (Assemblée/Sénat agenda, JO of the morning); during the day 2-3 glances for "est-ce que quelque chose me concerne" and a push on named triggers; 18:30 close check; Friday/Monday weekly Comex note (what changed in law, tax, social, competition).
Would pay for: (1) never missing a text or a competitor event that touches them; (2) a 5-minute pre-Comex brief organised by theme, copyable/printable; (3) trust labels and corroboration so they do not react to rumour; (4) low noise (exclusions, official-only mode); (5) the same view for the CFO and the communication director.

**CFO / trésorier** (Finance d'entreprise). Decisions: hedge EUR/USD or rates, timing of a bond/Euro PP/Schuldschein/RCF, covenant headroom, bank relations, M&A financing, IPO window, accounting and tax rule changes (IFRS, Pillar Two, CSRD reporting). Information: ECB/Fed decisions and speeches, Euribor/€STR/SOFR, OAT-Bund spread, credit spreads/high yield, rating actions (own, peers, sovereign), bank credit surveys (ECB bank lending survey, Banque de France), issuance market, regulators (AMF, ESMA, EBA), accounting standard setters, FX moves. Day: pre-open rates/FX read at 8:00, ECB/Fed meeting days, month-end funding review, quarterly covenant and rating meetings.

---

## B. Sources

### B1. Current catalogue slugs and the presets

Belong to the CF preset today (`professions.ts:111-129`) and are right: `bce-ecb`, `federal-reserve`, `bank-of-england`, `bis`, `amf`, `esma`, `financial-times`, `la-tribune`, `challenges`, `le-monde`, `le-figaro`, `bfm-economie`.
Questionable in CF: `marketwatch`, `cnbc`, `investing-fr`, `bfm-bourse`, `sec`: US/equity-trading oriented, high volume, low relevance to a French CFO (investing-fr trust 72, stock tips). Keep `sec` only as optional (US listings/144A), keep `cnbc` only with the macro feeds; drop `marketwatch`, `investing-fr`, `bfm-bourse` from the CF default. Add `eba`, `usine-nouvelle`, the new `les-echos` and rates/regulator feeds below.
DIRECTION_GENERALE preset (existing slugs): `le-monde`, `le-figaro`, `la-tribune`, `challenges`, `franceinfo`, `bfm-economie`, `financial-times`, `bbc-news`, `the-guardian`, `usine-nouvelle`, `conseil-ue`, `amf`, `bce-ecb`, `cert-fr`, `al-jazeera`, `france-24`, `un-news`. Not for the CEO: `coindesk`, `cointelegraph`, `oilprice`, `rigzone`, `marketwatch`, `investing-fr`, `bleepingcomputer`, `cisa`.

### B2. NEW sources (all fetched and parsed OK, newest item < 7 days unless a low-frequency note is given)

Trust rationale: primary institution/official 90-96; major press 80-88; specialised/trade 70-80; wires/company press releases and aggregators 60-74.

**Parliament and French institutions (category INSTITUTIONS)**

| name | slug | domain | cat | trust | feed URLs | items | newest |
|---|---|---|---|---|---|---|---|
| Assemblée nationale | assemblee-nationale | assemblee-nationale.fr | INSTITUTIONS | 94 | `http://www2.assemblee-nationale.fr/feeds/detail/documents-parlementaires` ; `.../feeds/detail/crs` (comptes rendus de séance) ; `https://www.assemblee-nationale.fr/dyn/rss/communiques-de-presse.xml` ; commissions: `.../feeds/detail/ID_59048/(type)/instance` (finances*), `ID_419610` (affaires économiques), `ID_420120` (affaires sociales), `ID_59051` (lois*) | 10 / 10 / 30 / 10 each | 1.7 d / 1.0 d / 3.1 d / 4.7, 2.9, 3.3, ~1 d |
| Sénat | senat | senat.fr | INSTITUTIONS | 94 | `https://www.senat.fr/rss/textes.rss` ; `https://www.senat.fr/rss/rapports.rss` ; `https://www.senat.fr/rss/presse.rss` (official index: senat.fr/flux-rss.html also lists ~25 thematic feeds `https://www.senat.fr/themes/rss/therssN.rss`, N=1..21+) | 26 / 23 / 5 | 1.1 d / 0.4 d / 3.0 d |
| Cour des comptes | cour-des-comptes | ccomptes.fr | INSTITUTIONS | 94 | `https://www.ccomptes.fr/rss/publications` ; `https://www.ccomptes.fr/rss/actualites` ; `https://www.ccomptes.fr/rss/general` | 10 each | 1.1 / 2.1 / 1.1 d |
| DGCCRF | dgccrf | economie.gouv.fr | INSTITUTIONS | 92 | `https://www.economie.gouv.fr/dgccrf/rss` | 20 | 1.1 d |
| Bercy DAJ (droit, commande publique) | bercy-daj | economie.gouv.fr | INSTITUTIONS | 90 | `https://www.economie.gouv.fr/daj/rss` | 10 | 5.2 d (low frequency) |
| Ministère du Travail | travail-emploi | travail-emploi.gouv.fr | INSTITUTIONS | 92 | `https://travail-emploi.gouv.fr/rss.xml` | 50 | 1.2 d |
| CNIL | cnil | cnil.fr | INSTITUTIONS | 92 | `https://www.cnil.fr/fr/rss.xml` | 10 | 1.2 d |
| Autorité de la concurrence | autorite-concurrence | autoritedelaconcurrence.fr | INSTITUTIONS | 94 | `https://www.autoritedelaconcurrence.fr/fr/rss.xml` | 10 | 16.1 d (low frequency, < 30 d OK) |
| Élysée | elysee | elysee.fr | INSTITUTIONS | 90 | `https://www.elysee.fr/feed` | 50 | 2.0 d |
| Commission européenne (presscorner) | commission-ue | ec.europa.eu | INSTITUTIONS | 95 | `https://ec.europa.eu/commission/presscorner/api/rss?language=fr` ; `...language=en` | 10 each | 1.0 d |
| OMC / WTO | omc-wto | wto.org | INSTITUTIONS | 92 | `https://www.wto.org/library/rss/latest_news_f.xml` ; `.../latest_news_e.xml` | 8 / 10 | 2.7 d |
| AMF (feeds supplémentaires, même slug `amf`) | amf | amf-france.org | INSTITUTIONS | 96 | `https://www.amf-france.org/fr/flux-rss/display/29` (sociétés concernées par une offre publique: **key for M&A**) ; `/display/23` (communiqués) ; `/display/30` (actualités) ; `/display/25` (publications) | 1 / 200 / 200 / 200 | 8.3 d (low freq) / 8.4 d / 1.1 d / 1.1 d |
| BCE (feeds supplémentaires, slug `bce-ecb`) | bce-ecb | ecb.europa.eu | INSTITUTIONS | 96 | `https://www.ecb.europa.eu/rss/statpress.html` ; `.../rss/wppub.html` ; ECB banking supervision `https://www.bankingsupervision.europa.eu/rss/press.html` | 15 each | 2.2 / 3.3 / 3.4 d |
| BCE taux de change de référence | bce-fxref | ecb.europa.eu | INSTITUTIONS | 98 | `https://www.ecb.europa.eu/rss/fxref-usd.html`, `-gbp`, `-chf`, `-jpy`, `-cny` (daily reference rate, one item per fixing: gives a dated, primary FX figure via keyFigures without any quote licence) | 5 each | 1.1 d |
| ESRB | esrb | esrb.europa.eu | INSTITUTIONS | 94 | `https://www.esrb.europa.eu/rss/press.xml` | 15 | 8.4 d (low freq) |
| FSB | fsb | fsb.org | INSTITUTIONS | 93 | `https://www.fsb.org/feed/` | 10 | 1.4 d |
| Bank of Japan | boj | boj.or.jp | INSTITUTIONS | 95 | `https://www.boj.or.jp/en/rss/whatsnew.xml` | 51 | 1.3 d |
| Banque nationale suisse | snb | snb.ch | INSTITUTIONS | 94 | `https://www.snb.ch/public/en/rss/pressrel` | 20 | 9.0 d (low freq) |
| Fed (feeds supplémentaires, slug `federal-reserve`) | federal-reserve | federalreserve.gov | INSTITUTIONS | 96 | `https://www.federalreserve.gov/feeds/press_monetary.xml` ; `.../feeds/speeches.xml` | 15 each | 2.9 / 2.3 d |
| Bank of England (publications) | bank-of-england | bankofengland.co.uk | INSTITUTIONS | 95 | `https://www.bankofengland.co.uk/rss/publications` | 50 | 1.1 d |
| HM Treasury + gov.uk | uk-gov | gov.uk | INSTITUTIONS | 90 | `https://www.gov.uk/government/organisations/hm-treasury.atom` ; `https://www.gov.uk/search/news-and-communications.atom` | 20 each | 2.7 d / 0.0 d |
| FTC | ftc | ftc.gov | INSTITUTIONS | 92 | `https://www.ftc.gov/feeds/press-release.xml` | 10 | 2.2 d |
| SEC (feeds supplémentaires, slug `sec`) | sec | sec.gov | INSTITUTIONS | 96 | `https://www.sec.gov/enforcement-litigation/litigation-releases/rss` | 25 | 0.9 d |
| US macro (BLS, BEA, Census) | us-macro | bls.gov / bea.gov / census.gov | INSTITUTIONS | 94 | `https://www.bls.gov/feed/bls_latest.rss` (1 item = latest key indicators) ; `https://www.bea.gov/rss/rss.xml` ; `https://www.census.gov/economic-indicators/indicator.xml` | 1 / 48 / 18 | 1.1 / 4.2 / 2.1 d |

(*) Assemblée commission IDs: the official page `assemblee-nationale.fr/dyn/les-fils-rss-de-l-assemblee-nationale` lists 8 permanent commissions in alphabetical order with these IDs in this order: 419604 culturelles, 419610 économiques, 59047 étrangères, 420120 sociales, 59046 défense, 419865 développement durable, 59048 finances, 59051 lois. The mapping of 59048 = finances and 59051 = lois is **[INFERENCE]** from order (item titles: 59048 = PPL on successions/donations tax, 59051 = loi organique inéligibilité, consistent). Verify before shipping. All feeds are served over `http://www2...` and parse; titles are all "Assemblée nationale", so per-feed naming is needed in the catalogue.

**Press (FR and international)**

| name | slug | domain | cat | trust | feed URLs | items | newest |
|---|---|---|---|---|---|---|---|
| Les Échos | les-echos | lesechos.fr | FINANCIAL_PRESS | 88 | `https://services.lesechos.fr/rss/les-echos-economie.xml` ; `.../les-echos-entreprises.xml` ; `.../les-echos-finance-marches.xml` ; `.../les-echos-politique.xml` ; `.../les-echos-monde.xml` ; `.../les-echos-tech-medias.xml` ; `.../les-echos-idees.xml` ; `.../les-echos-patrimoine.xml` ; `.../les-echos-regions.xml` | 20 each | 0.1 to 0.4 d |
| Le Figaro (rubriques) | le-figaro | lefigaro.fr | GENERAL_PRESS | 82 | `.../rss/figaro_conjoncture.xml` ; `figaro_flash-eco.xml` ; `figaro_societes.xml` (title "Bourse de Paris", company news) ; `figaro_politique.xml` ; `figaro_actualite-france.xml` ; `figaro_international.xml` | 20 each | 0.0 to 0.4 d |
| Le Monde (rubriques) | le-monde | lemonde.fr | GENERAL_PRESS | 88 | `https://www.lemonde.fr/entreprises/rss_full.xml` ; `.../economie-francaise/rss_full.xml` ; `.../politique/rss_full.xml` ; `.../idees/rss_full.xml` | 20 each | 0.1 d |
| BFM (rubriques) | bfm-economie | bfmtv.com | FINANCIAL_PRESS | 76 | `https://www.bfmtv.com/rss/economie/entreprises/` ; `.../economie/emploi/` ; `.../politique/` ; `.../international/` | 30 each | 0.0 to 0.3 d |
| franceinfo (rubriques) | franceinfo | francetvinfo.fr | GENERAL_PRESS | 84 | `https://www.francetvinfo.fr/politique.rss` ; `.../monde.rss` | 19 / 20 | 0.0 d |
| L'Express | lexpress | lexpress.fr | GENERAL_PRESS | 80 | `https://www.lexpress.fr/rss/economie.xml` ; `.../rss/entreprise.xml` ; `.../arc/outboundfeeds/rss/alaune.xml` | 100 each | 0.1 to 0.3 d |
| Libération | liberation | liberation.fr | GENERAL_PRESS | 78 | `https://www.liberation.fr/arc/outboundfeeds/rss-all/` | 50 | 0.0 d |
| Mediapart | mediapart | mediapart.fr | GENERAL_PRESS | 78 | `https://www.mediapart.fr/articles/feed` (reputation/affaires; only 10 items) | 10 | 0.0 d |
| RFI | rfi | rfi.fr | GENERAL_PRESS | 82 | `https://www.rfi.fr/fr/economie/rss` | 30 | 0.2 d |
| France 24 (rubriques) | france-24 | france24.com | GENERAL_PRESS | 82 | `https://www.france24.com/fr/economie/rss` ; `.../fr/europe/rss` | 30 each | 0.0 to 0.4 d |
| L'Usine Digitale | usine-digitale | usine-digitale.fr | ENERGY (industry) | 76 | `https://www.usine-digitale.fr/rss` | 10 | 1.0 d |
| Le Revenu | le-revenu | lerevenu.com | FINANCIAL_PRESS | 72 | `https://www.lerevenu.com/rss.xml` | 50 | 0.1 d |
| Journal du Net (économie) | journaldunet | journaldunet.com | FINANCIAL_PRESS | 70 | `https://www.journaldunet.com/economie/rss/` | 60 | 1.1 d |
| Maddyness | maddyness | maddyness.com | GENERAL_PRESS | 72 | `https://www.maddyness.com/feed/` (startups, funding rounds) | 10 | 1.0 d |
| Actu-Juridique | actu-juridique | actu-juridique.fr | FINANCIAL_PRESS | 76 | `https://www.actu-juridique.fr/feed` | 10 | 1.3 d |
| Financial Times (rubriques) | financial-times | ft.com | FINANCIAL_PRESS | 88 | `https://www.ft.com/companies?format=rss` ; `/companies/financials` ; `/markets` ; `/europe` ; `/global-economy` ; `/energy` ; `/technology` ; `/mergers-acquisitions` ; `/world` (all `?format=rss`) | 25 each | 0.1 to 0.9 d |
| Bloomberg (public feeds) | bloomberg | bloomberg.com | FINANCIAL_PRESS | 86 | `https://feeds.bloomberg.com/business/news.rss` ; `/markets/news.rss` ; `/economics/news.rss` ; `/industries/news.rss` ; `/politics/news.rss` ; `/technology/news.rss` | 11-20 each | 0.0 to 0.1 d |
| The Economist | economist | economist.com | FINANCIAL_PRESS | 85 | `https://www.economist.com/finance-and-economics/rss.xml` ; `/business/rss.xml` ; `/europe/rss.xml` ; `/international/rss.xml` ; `/briefing/rss.xml` ; `/leaders/rss.xml` (weekly cadence, 300 items back catalogue, newest 0.9 to 3.8 d; titles empty feed-level) | 300 each | 0.9 to 3.8 d |
| New York Times | nyt | nytimes.com | GENERAL_PRESS | 84 | `https://rss.nytimes.com/services/xml/rss/nyt/Business.xml` ; `.../Economy.xml` ; `.../DealBook.xml` ; `.../Europe.xml` ; `.../World.xml` | 20-58 | 0.0 to 1.0 d |
| The Guardian (rubriques) | the-guardian | theguardian.com | GENERAL_PRESS | 84 | `https://www.theguardian.com/business/economics/rss` ; `.../world/europe-news/rss` ; `.../world/rss` | 19-45 | 0.0 to 0.5 d |
| BBC (Europe) | bbc-news | bbc.co.uk | GENERAL_PRESS | 86 | `https://feeds.bbci.co.uk/news/world/europe/rss.xml` | 25 | 0.0 d |
| Politico Europe | politico-eu | politico.eu | GENERAL_PRESS | 82 | `https://www.politico.eu/feed/` (EU regulation and politics) | 10 | 0.1 d |
| Euronews | euronews | euronews.com | GENERAL_PRESS | 76 | `https://www.euronews.com/rss?format=mrss&level=theme&name=business` ; `https://www.euronews.com/rss?level=theme&name=news` | 50 each | 0.0 to 1.0 d |
| Foreign Policy | foreign-policy | foreignpolicy.com | GENERAL_PRESS | 76 | `https://foreignpolicy.com/feed/` | 25 | 0.8 d |
| Axios | axios | axios.com | GENERAL_PRESS | 74 | `https://www.axios.com/feeds/feed.rss` | 100 | 0.1 d |
| Semafor | semafor | semafor.com | GENERAL_PRESS | 74 | `https://www.semafor.com/rss.xml` | 256 | 1.0 d |
| CNBC (feeds supplémentaires) | cnbc | cnbc.com | FINANCIAL_PRESS | 80 | `https://www.cnbc.com/id/10001147/device/rss/rss.html` (Business) ; `.../10000113/...` (Politics) ; `.../100727362/...` (International) | 30 each | 0.0 to 0.2 d |

**Supply chain and logistics (new category justified: `SUPPLY_CHAIN`; today they would be filed under ENERGY, which is wrong for a CEO UI facet)**

| name | slug | domain | trust | feed URLs | items | newest |
|---|---|---|---|---|---|---|
| Supply Chain Dive | supply-chain-dive | supplychaindive.com | 74 | `https://www.supplychaindive.com/feeds/news/` | 10 | 1.0 d |
| SupplyChainBrain | supplychainbrain | supplychainbrain.com | 70 | `https://www.supplychainbrain.com/rss/articles` | 30 | 0.8 d |
| Supply Chain Magazine (FR) | supply-chain-magazine | supplychainmagazine.fr | 72 | `https://www.supplychainmagazine.fr/rss` | 10 | 1.2 d |
| FreightWaves | freightwaves | freightwaves.com | 72 | `https://www.freightwaves.com/news/feed` | 56 | 0.9 d |
| Splash247 (shipping) | splash247 | splash247.com | 74 | `https://splash247.com/feed/` | 10 | 0.4 d |
| gCaptain (maritime) | gcaptain | gcaptain.com | 70 | `https://gcaptain.com/feed/` | 12 | 0.8 d |

**Corporate disclosure, M&A, restructuring wires (new category justified: `CORPORATE_NEWS`, trust 70-82; company press releases are primary but promotional)**

| name | slug | domain | trust | feed URLs | items | newest |
|---|---|---|---|---|---|---|
| Actusnews Wire (FR information réglementée) | actusnews | actusnews.com | 82 | `https://www.actusnews.com/fr/rss` ; `https://www.actusnews.com/en/rss` | 20 each | 1.0 d |
| GlobeNewswire (topic feeds) | globenewswire | globenewswire.com | 72 | `https://www.globenewswire.com/RssFeed/subjectcode/1-Acquisitions%20And%20Mergers/feedTitle/GlobeNewswire%20-%20Acquisitions%20And%20Mergers` ; `.../subjectcode/27-Mergers%20And%20Acquisitions/...` ; `.../subjectcode/13-Credit%20Ratings/...` ; `.../36-Bankruptcy/...` ; `.../11-Dividend/...` ; `.../18-Financing%20Agreements/...` ; `.../25-Legal/...` ; `.../14-Earnings%20Releases%20And%20Operating%20Results/...` ; `.../country/France/feedTitle/GlobeNewswire%20-%20France` | 20 each | 0.8 to 1.8 d |
| PR Newswire (topic feeds) | prnewswire | prnewswire.com | 72 | `https://www.prnewswire.com/rss/acquisitions-mergers-and-takeovers-latest-news/acquisitions-mergers-and-takeovers-latest-news-list.rss` ; `.../mergers-and-acquisitions-latest-news/...-list.rss` ; `.../bankruptcy-latest-news/...` ; `.../credit-ratings-latest-news/...` ; `.../dividends-latest-news/...` ; `.../earnings-latest-news/...` ; `.../legal-issues-latest-news/...` ; `.../france-latest-news/...` ; `.../europe-news/europe-news-list.rss` ; `https://www.prnewswire.com/rss/financial-services-latest-news/financial-services-latest-news-list.rss` | 20 each | 0.0 d |
| PE Hub | pe-hub | pehub.com | 72 | `https://www.pehub.com/feed/` | 10 | 0.9 d |
| Private Equity Wire | pe-wire | privateequitywire.co.uk | 66 | `https://www.privateequitywire.co.uk/feed/` | 10 | 1.1 d |

Note: `proSourceCatalog.ts:20-23` says GlobeNewswire and Business Wire "refused automated access". GlobeNewswire **topic RSS URLs work** (above); only the home/other paths were refused. Same for Les Échos: `www.lesechos.fr` paths 404/refused but `services.lesechos.fr/rss/*.xml` serves valid feeds. Business Wire not re-tested with a valid feed id.

**Aggregators for "my company / my competitors / reputation" (trust 60-65, category OTHER/CUSTOM unless a new `AGGREGATOR` is created)**
- Google News RSS search: `https://news.google.com/rss/search?q=%22LVMH%22+when:2d&hl=fr&gl=FR&ceid=FR:fr` (47 items, newest 0.1 d), `q=intitle:%22TotalEnergies%22+when:1d` (9 items, 0.1 d), `q=%22OPA%22+OR+%22OPE%22+when:2d` (12, 0.8 d), `q=%22redressement+judiciaire%22+when:2d` (23, 0.1 d), topic `https://news.google.com/rss/headlines/section/topic/BUSINESS?hl=fr&gl=FR&ceid=FR:fr` (70, 0.0 d), `q=site:legifrance.gouv.fr+when:7d` (100, 0.2 d: a partial JORF workaround, see rejected).
- Bing News RSS: `https://www.bing.com/news/search?q=TotalEnergies&format=rss&setmkt=fr-FR` (12 items, 0.8 d).
These are the only RSS-only way to monitor an arbitrary company name. Not suitable for the shared catalogue (query-specific, publisher is inside the title suffix, links go through a redirector, Google's terms target personal reader use: **[INFERENCE] legal risk to check**). Suitable as private per-user custom feeds generated by a "Suivre une société" builder (G2).

### B3. Rejected or unreachable (tested)

| candidate | reason |
|---|---|
| Légifrance / Journal officiel RSS | 403 on `/rss/jorf.xml`; no official RSS; JORF only via PISTE API (OAuth, free) or data.gouv dump: **structural gap** (G9) |
| Vie-publique.fr | host unreachable / not valid feed from the fetcher |
| Banque de France (all guessed URLs), ACPR, AFT, Bpifrance | 403 or 404; no discoverable public RSS; **gap for CFO persona** |
| INSEE (informations rapides) | 500 or no feed; subscription is JS-driven (`insee.fr/fr/information/2414679`); **gap for macro** |
| Euronext, IFRS, EFRAG, EUR-Lex (per-search IDs), Eurogroup, OECD (403), IMF, World Bank | 404/403/invalid; none exposes a public valid feed at the tried URLs |
| Rating agencies (S&P, Moody's, Fitch, Scope) | not valid RSS; use wire topic feeds "Credit Ratings" (GlobeNewswire/PRN) and press (FT, Bloomberg) as proxy |
| Reuters (3 URLs), Euractiv (403), Le Point (403), Option Finance (403/404), Agefi (404/invalid), CFNews (invalid), Capital.fr (404), Décideurs (404), L'Opinion (404), Challenges entreprise sub-feeds (404), Zonebourse (port rule), Medef (404), Dalloz Actualité (timeout), Liaisons sociales (timeout), Lloyd's List (500), JOC (invalid) | unreachable or not RSS |
| Conseil constitutionnel, Conseil d'État | no valid feed at the published URL (HTML 404 / HTML page) |
| WSJ markets and US business | feed alive but newest item 620 d: stale |
| FT supply-chains | newest 23 d: fails 7 d rule |
| Bank of Canada | newest 30.2 d: over the 30 d limit |
| AMF `display/28` (alertes, 52 d) and `/24` (sanctions, 79 d), Arcom (243 d), Bruegel (52 d) | too old for the freshness rule; AMF 28 and 24 are rare-by-nature and worth keeping as an exception (primary, trust 96) if the product accepts it |
| AMF `display/21` (already in catalogue) | did not return OK in my batch 4 run (single try, possible rate/transient): recheck |
| Le Parisien économie, FCA news, Consilium FR | parse but items carry **no dates**: Syntheza cannot order or age them |
| Bloomberg wealth, HATVP, Mergermarket, Octus | feed empty |
| SEC EDGAR 8-K Atom, `usgaap.rss.xml` | raw filings (hundreds of "8-K - COMPANY (cik)" lines), no usable text for the classifier: noise |
| Eurostat statistics-update | 2385 items = dataset updates, not news: noise |

---

## C. Assets and keywords

### C1. Precision problems in the CURRENT CORPORATE_FINANCE template (verified by probe)

| term/rule | problem (observed) | fix |
|---|---|---|
| `rachat` (M&A entity) and `rachat\w*` (EVENT_LEXICON M_AND_A) | "Un rachat de crédit pour votre résidence principale" matches entity AND is typed M_AND_A | replace by `rachat d'entreprise`, `rachète`, `offre de rachat`; lexicon: `rachat(?! de credit| de trimestres)` |
| `acquisition` (entity) | generic ("acquisition de données/clients") | `acquisition de` + company context; keep `acquiert`/`acquires` |
| `stake` (M_AND_A lexicon) | "The deal is at stake" typed M_AND_A + REGULATION_LEGAL | use `takes? a stake|stake in|prise de participation` |
| `fusion\w*` lexicon | "Fusion nucléaire" typed M_AND_A and ENERGY | `fusions?(?: |-)acquisitions?|fusionn\w*|fusion de` |
| `degrade\w*|degradation\w*` (RATINGS lexicon) | "Dégradation de la qualité de l'air à Paris" typed RATINGS | require rating context: `degradation de la note|notation|perspective|abaisse la note` |
| `strikes?` (INCIDENT lexicon) | "Trump strikes deal with Xi on tariffs" typed INCIDENT (and GEOPOLITICS via tariffs) | drop bare `strike`; keep `general strike|workers strike|strike action|walkout`; keep `greve\w*` |
| `Lagarde` (entity BCE + CENTRAL_BANK lexicon) | "Christophe Lagarde défend le projet de loi" matches BCE entity and typed CENTRAL_BANK | use `Christine Lagarde` (entity and lexicon) |
| `EUR/USD` keywords | "L'euro tombe à 1,05 dollar après les propos de Lagarde" **not** matched by EUR/USD (only `euro dollar` adjacent forms) and typed only CENTRAL_BANK, never MARKETS | add `l'euro`, `euro face au dollar`, `euro à \d,\d+ dollar` cannot be regex; add `euro recule`, `euro progresse`, `euro tombe`, `euro/dollar`, `dollar américain`, `dollar` + `devise`; add `euro` to MARKETS lexicon words |
| `Euribor` / rates | "Euribor 3 mois à 2,1 %" matches the rate entity but gets **no event type**; "Taux de refinancement... crédit aux PME" no type | add `euribor|estr|sofr|taux de refinancement|spread|oat bund` to MARKETS or a new CREDIT_FINANCING type |
| `OAT` | OK (uppercase, case-sensitive) | none |
| `tariffs`, `trade war` | OK | none |
| missing | no `€STR/SOFR`, no `émission obligataire`, no `défaillances d'entreprises`, no `Banque de France`, no `notation`/ratings entity, no IPO, no hedging terms | see C3 |

Exclusion needed overall (the product has none per entity: see G3): "rachat de crédit|rachat de trimestres", "at stake", "fusion nucléaire", "qualité de l'air", "Christophe Lagarde", "Washington Post", "Bourse de Pékin" fine.

### C2. DIRECTION_GENERALE default watchlist (24 entities)

Terms are written as they appear in headlines; remember matching rules (`signal.ts:329-358`): ALL-CAPS up to 5 chars and Capitalised up to 3 chars are case-sensitive, other terms accent/case-insensitive on whole words, label ignored when keywords exist.

| # | label | kind | symbol | keywords (FR + EN) | false positives / exclusions |
|---|---|---|---|---|---|
| 1 | Mon entreprise | COMPANY | (ticker) | to fill at onboarding: legal name, brands, ticker, CEO name | e.g. common-word names (Orange, Alstom ok); needs exclusions |
| 2 | Mes concurrents | COMPANY | | to fill (up to 10 names + aliases) | same |
| 3 | Mes clients clés | COMPANY | | to fill | same |
| 4 | Mes fournisseurs clés | COMPANY | | to fill | same |
| 5 | Mon secteur | THEME | | chosen from sector packs (G4) | pack-specific |
| 6 | Loi de finances et budget | THEME | | `loi de finances`, `projet de loi de finances`, `PLF`, `PLFSS`, `budget 2027`, `budget de l'État`, `49.3`, `motion de censure`, `Cour des comptes`, `finance bill`, `budget bill`, `government spending cuts` | `budget` alone too generic (household budget): not used |
| 7 | Fiscalité des entreprises | THEME | | `impôt sur les sociétés`, `surtaxe`, `contribution exceptionnelle`, `CVAE`, `crédit d'impôt recherche`, `CIR`, `taxe minimale`, `Pillar Two`, `redressement fiscal`, `taxe sur les services numériques`, `corporate tax`, `minimum tax`, `windfall tax`, `digital services tax` | `taxe` and `TVA` alone: household noise (taxe foncière, TVA restaurants); keep compound forms |
| 8 | Droit et règlement européens | THEME | | `Commission européenne`, `Parlement européen`, `directive européenne`, `règlement européen`, `omnibus`, `CSRD`, `CSDDD`, `taxonomie`, `AI Act`, `RGPD`, `CBAM`, `DORA`, `NIS2`, `DMA`, `DSA`, `EU directive`, `EU regulation` | `directive` alone; `DMA`/`DSA` (3 caps, case-sensitive OK) |
| 9 | Législation et Journal officiel | THEME | | `Journal officiel`, `JORF`, `projet de loi`, `proposition de loi`, `décret`, `ordonnance`, `amendement`, `Assemblée nationale`, `Sénat`, `Conseil constitutionnel`, `Conseil d'État`, `lawmakers`, `draft law` | `ordonnance` (médicale, rare in news); `Sénat` fine; `décret` ok |
| 10 | Droit social et emploi | THEME | | `SMIC`, `salaire minimum`, `négociation annuelle obligatoire`, `NAO`, `assurance chômage`, `réforme des retraites`, `cotisations patronales`, `charges sociales`, `CSE`, `CGT`, `CFDT`, `FO`, `Medef`, `CPME`, `URSSAF`, `plan de sauvegarde de l'emploi`, `PSE`, `rupture conventionnelle`, `télétravail`, `minimum wage`, `collective bargaining` | `FO` 2 caps case-sensitive may hit headline abbreviations: drop it; `CSE` ok |
| 11 | Grèves et mobilisations | THEME | | `grève générale`, `grève nationale`, `préavis de grève`, `mouvement social`, `blocage`, `general strike`, `walkout` | bare `strike`/`strikes` (airstrike, "strikes deal"): excluded |
| 12 | Conjoncture France | COUNTRY | | `Insee`, `PIB`, `croissance française`, `climat des affaires`, `défaillances d'entreprises`, `confiance des chefs d'entreprise`, `Banque de France`, `inflation en France`, `French economy` | `PIB` ok; `inflation` alone too generic for FR scope |
| 13 | Taux directeurs et crédit | RATE | | `BCE`, `ECB`, `Christine Lagarde`, `taux directeurs`, `Euribor`, `OAT`, `taux à 10 ans`, `crédit aux entreprises`, `coût du crédit` | `Lagarde` alone excluded |
| 14 | Euro / dollar | FX | EUR/USD | `EUR/USD`, `euro/dollar`, `euro dollar`, `euro-dollar`, `l'euro recule`, `l'euro progresse`, `l'euro tombe`, `parité euro` | |
| 15 | Droits de douane et commerce | THEME | | `droits de douane`, `tarifs douaniers`, `surtaxes douanières`, `guerre commerciale`, `OMC`, `anti-dumping`, `Mercosur`, `CBAM`, `Section 232`, `tariffs`, `trade war`, `WTO` | |
| 16 | Chaîne d'approvisionnement | THEME | | `chaîne d'approvisionnement`, `supply chain`, `pénurie`, `rupture d'approvisionnement`, `fret maritime`, `porte-conteneurs`, `mer Rouge`, `canal de Suez`, `canal de Panama`, `goulot d'étranglement`, `semi-conducteurs`, `shortage`, `container shipping`, `Red Sea` | `pénurie` generic (médecins, logement): accept with sector context; `Red Sea` fine |
| 17 | Énergie pour l'industrie | THEME | | `prix de l'électricité`, `prix du gaz`, `ARENH`, `TURPE`, `TTF`, `tarif réglementé`, `prix de l'énergie`, `Brent`, `energy prices`, `power prices` | |
| 18 | Géopolitique et sanctions | THEME | | `sanctions`, `embargo`, `Ukraine`, `Russie`, `Taïwan`, `Moyen-Orient`, `détroit d'Ormuz`, `cessez-le-feu`, `ceasefire`, `OFAC` | `sanctions` includes sports/disciplinary; low weight |
| 19 | Chine | COUNTRY | | `Chine`, `Pékin`, `China`, `Beijing` | `Bourse de Pékin` fine; low risk |
| 20 | États-Unis | COUNTRY | | `États-Unis`, `Maison-Blanche`, `Trump`, `White House`, `Washington` | exclude `Washington Post`, `Washington State` |
| 21 | Marché du contrôle et M&A | THEME | | `OPA`, `OPE`, `OPRA`, `LBO`, `offre publique`, `fusion-acquisition`, `fusions-acquisitions`, `prise de contrôle`, `private equity`, `M&A`, `takeover`, `merger`, `buyout` | `rachat de crédit|rachat de trimestres`, `fusion nucléaire` excluded |
| 22 | Défaillances et procédures collectives | THEME | | `redressement judiciaire`, `liquidation judiciaire`, `procédure de sauvegarde`, `dépôt de bilan`, `faillite`, `Chapter 11`, `insolvency` | |
| 23 | Cyber et IA | THEME | | `cyberattaque`, `rançongiciel`, `ransomware`, `ANSSI`, `NIS2`, `intelligence artificielle`, `IA générative`, `AI Act` | `IA` bare (2 caps) acceptable only case-sensitive |
| 24 | Réputation et gouvernance | THEME | | `boycott`, `bad buzz`, `scandale`, `greenwashing`, `lanceur d'alerte`, `mise en examen`, `parquet national financier`, `PNF`, `démission du PDG`, `départ du PDG`, `CEO steps down`, `CEO resigns`, `activist investor`, `actionnaire activiste` | `polémique` too generic (politics); `scandale` accept |

### C3. CORPORATE_FINANCE watchlist (replace current 9; 22 entities)

| label | kind | symbol | keywords | exclusions / notes |
|---|---|---|---|---|
| EUR/USD | FX | EUR/USD | `EUR/USD`, `euro/dollar`, `euro dollar`, `euro-dollar`, `parité euro`, `l'euro tombe`, `l'euro progresse` | |
| EUR/GBP | FX | EUR/GBP | `EUR/GBP`, `euro livre`, `livre sterling`, `sterling` | |
| EUR/CHF | FX | EUR/CHF | `EUR/CHF`, `franc suisse` | |
| USD/JPY | FX | USD/JPY | `USD/JPY`, `yen` | `yen` also Japan equities; fine |
| EUR/CNY | FX | EUR/CNY | `yuan`, `renminbi`, `EUR/CNY` | |
| Taux BCE | RATE | | `taux directeurs`, `taux de la BCE`, `taux de dépôt`, `deposit facility rate`, `ECB rates`, `ECB rate decision`, `ECB meeting` | |
| Euribor / €STR | RATE | | `Euribor`, `€STR`, `ESTR`, `taux interbancaire`, `taux de refinancement` | |
| SOFR / Fed funds | RATE | | `SOFR`, `Fed funds`, `FOMC`, `taux directeur de la Fed`, `Fed rate` | |
| OAT 10 ans | RATE | | `OAT`, `OAT 10 ans`, `taux à 10 ans`, `rendement des obligations françaises`, `French 10-year yield` | |
| Bund / spread | RATE | | `Bund`, `spread OAT-Bund`, `spread France-Allemagne`, `Bund yield` | |
| Spreads de crédit | RATE | | `spread de crédit`, `crédit corporate`, `high yield`, `investment grade`, `credit spreads`, `iTraxx`, `Crossover` | `Crossover` (album) excluded unless with `iTraxx` |
| Banque centrale européenne | INSTITUTION | | `BCE`, `ECB`, `Christine Lagarde`, `Banque centrale européenne` | not bare `Lagarde` |
| Réserve fédérale | INSTITUTION | | `Fed`, `Réserve fédérale`, `Federal Reserve`, `Jerome Powell` | `fed up` handled by case-sensitivity |
| Banque de France | INSTITUTION | | `Banque de France`, `Villeroy de Galhau`, `Banque de France` | feed not available (B3) |
| Crédit aux entreprises | THEME | | `crédit aux entreprises`, `financement des entreprises`, `crédit aux PME`, `covenant`, `dette d'entreprise`, `leveraged loan`, `prêt syndiqué`, `ligne de crédit`, `RCF` | |
| Marché obligataire corporate | THEME | | `émission obligataire`, `bond issue`, `euro PP`, `Schuldschein`, `obligation convertible`, `OCEANE`, `ORNANE`, `green bond`, `obligation verte`, `refinancement` | |
| Notations | THEME | | `Moody's`, `Fitch`, `S&P Global Ratings`, `notation financière`, `perspective négative`, `note souveraine`, `relèvement de la note`, `credit rating`, `outlook` | `outlook` generic: drop; `dégradation` alone excluded |
| IPO et introductions | THEME | | `introduction en bourse`, `IPO`, `cotation`, `Euronext Growth`, `spin-off`, `scission` | `cotation` generic (quotes) |
| Fusions-acquisitions | THEME | | `OPA`, `OPE`, `OPRA`, `LBO`, `offre publique`, `fusion-acquisition`, `fusions-acquisitions`, `takeover`, `merger`, `buyout` | exclusions as C1 |
| Normes comptables et fiscales | THEME | | `IFRS`, `normes comptables`, `IASB`, `FASB`, `Pillar Two`, `taxe minimale`, `CSRD`, `reporting de durabilité`, `ESEF` | |
| Droits de douane | THEME | | `droits de douane`, `tarifs douaniers`, `guerre commerciale`, `tariffs`, `trade war` | |
| Prix de l'énergie | THEME | | `prix de l'énergie`, `prix du gaz`, `prix de l'électricité`, `TTF`, `energy prices` | |

---

## D. Alert rule templates and widgets

### D1. DIRECTION_GENERALE alerts (event types marked NEW require section F)

| name | entity / keywords | event types | min |
|---|---|---|---|
| Mon entreprise cité | entity "Mon entreprise" | any (empty list = all) | impact MEDIUM |
| Concurrent : événement majeur | entity "Mes concurrents" | EARNINGS, M_AND_A, DEFAULT_RESTRUCTURING, INCIDENT, REGULATION_LEGAL, GOVERNANCE (NEW) | impact MEDIUM |
| Client ou fournisseur en difficulté | entities clients+fournisseurs | DEFAULT_RESTRUCTURING, INCIDENT, RATINGS | impact LOW, trust 50 |
| Loi, décret ou texte européen | entities 8 and 9 | LEGISLATION (NEW), REGULATION_LEGAL | trust 80 (official sources only) |
| Loi de finances et fiscalité | entities 6 and 7 | LEGISLATION (NEW), TAX (NEW), MACRO | impact MEDIUM |
| Social : conflit ou réforme | entity 10 and 11 | LABOUR_SOCIAL (NEW), INCIDENT | impact MEDIUM |
| Rupture d'approvisionnement | entity 16 | SUPPLY_CHAIN (NEW), INCIDENT, GEOPOLITICS_SANCTIONS | impact MEDIUM |
| Sanctions et droits de douane | entities 15 and 18 | GEOPOLITICS_SANCTIONS | trust 60 |
| Cyberattaque chez un pair ou un sous-traitant | entity 23 | INCIDENT | impact MEDIUM |
| Réputation : polémique | entity 24 | REPUTATION (NEW), REGULATION_LEGAL | impact MEDIUM |
| Rumeur d'OPA | entity 21 | M_AND_A | trust < 60 shown as rumour (RUMOR_RADAR); rule on isRumor missing (G5) |

### D2. CORPORATE_FINANCE alerts

| name | entity | event types | min |
|---|---|---|---|
| Décision BCE / Fed | BCE, Fed | CENTRAL_BANK | impact MEDIUM |
| EUR/USD | EUR/USD | MACRO, CENTRAL_BANK, MARKETS | impact MEDIUM |
| Euribor / OAT / Bund | rates entities | MARKETS, CENTRAL_BANK, MACRO | impact MEDIUM |
| Notation (pays, pairs, soi) | Notations | RATINGS | trust 60 |
| Émission et refinancement | Marché obligataire | CREDIT_FINANCING (NEW) | impact LOW |
| Offre publique AMF | Fusions-acquisitions | M_AND_A | impact MEDIUM, from AMF display/29 |
| Normes et fiscalité | Normes comptables | REGULATION_LEGAL, TAX (NEW) | trust 80 |
| Droits de douane | Droits de douane | GEOPOLITICS_SANCTIONS, MACRO | trust 60 |

### D3. Widget layout (existing types only)

`OpsWidgetConfig` supports `scope`, `entityIds`, `eventTypes`, `minTrust`, `minImpact`, `hours` (`opsTypes.ts:76-84`), so one widget type can be instantiated several times with different filters. DIRECTION_GENERALE (12-column grid, w in 4/6/8/12):
1. BRIEFING w6 h2, 2. ALERTS w6 h2,
3. LIVE_WIRE "Mon entreprise et concurrents" w6 h2 (entityIds = entities 1-4),
4. LIVE_WIRE "Lois, décrets, Europe" w6 h2 (eventTypes REGULATION_LEGAL, LEGISLATION, scope all, minTrust 80),
5. LIVE_WIRE "Social et fiscalité" w6 h2 (LABOUR_SOCIAL, TAX),
6. LIVE_WIRE "Chaîne d'approvisionnement et géopolitique" w6 h2 (SUPPLY_CHAIN, GEOPOLITICS_SANCTIONS),
7. WATCHLIST_PULSE w4 h2, RUMOR_RADAR w4 h2, EVENT_MIX w4 h1, DECISION_JOURNAL w6 h2, SOURCE_HEALTH w6 h1.
CORPORATE_FINANCE: BRIEFING w6, ALERTS w6, WATCHLIST_PULSE w4 (rates/FX), LIVE_WIRE w8 (rates, FX, central banks), LIVE_WIRE w6 "Crédit, émissions, notations", LIVE_WIRE w6 "M&A et IPO", RUMOR_RADAR w6, EVENT_MIX w6 h1, DECISION_JOURNAL w6, SOURCE_HEALTH w6 h1 (as today `professions.ts:328-337`, plus the two filtered LIVE_WIRE).
Limit: widget config has **no source/category filter**, so "official sources only" cannot be a widget setting (G6).

---

## E. Feature gaps

| # | need | current state | proposal | prio | feasibility (RSS-only, no AI, no licensed data) |
|---|---|---|---|---|---|
| G1 | **Onboarding "Mon entreprise"**: own company, aliases/brands, ticker, sector, competitors, key clients, suppliers, countries | onboarding picks a profession and applies a static template (`professions.ts` entities are fixed; docs line 69) | wizard asks the 6 inputs, creates COMPANY entities with a role (OWN/COMPETITOR/CLIENT/SUPPLIER, new column on `WatchEntity`, `schema.prisma:537`), sector pack, per-company feeds (G2) and default rules | P0 | fully feasible, no data licence |
| G2 | Monitor an arbitrary company or competitor name across the whole web | custom private RSS exists but user must supply a URL | "Suivre une société" builder generating a Google News / Bing News RSS query URL stored as a private custom channel; show publisher parsed from title suffix; fallback when blocked | P0 | works (tested, 47 items for "LVMH"); Google terms risk **[INFERENCE]**; Bing alternative |
| G3 | **Exclusion keywords** per entity and per alert (kill false positives such as "rachat de crédit", "Washington Post") | `WatchEntity` has only `keywords` (`schema.prisma:544`); `compileTerms` has no negatives (`signal.ts:329`); mutes are global and keyword-level, not per entity (docs line 50) | `excludeKeywords String[]` on `WatchEntity` and `AlertRule`; AND-NOT in `compileTerms` | P0 | trivial |
| G4 | Sector packs (industrie, distribution/conso, énergie, santé, banque-assurance, immobilier, transport-logistique, tech, luxe, agro, défense) | none | static keyword bundles + preset publishers per sector, selectable in onboarding and settings | P1 | static data |
| G5 | Alert conditions richer than entity + 11 event types + trust/impact | `AlertRule` = entity, keywords, eventTypes, minTrust, minImpact (`schema.prisma:555-576`) | add: only official sources (publisher category/trust >= 90), min corroborating sources, rumour only / exclude rumours, source include list, quiet hours, per-rule daily cap | P1 | uses existing fields (`trust.corroboratingSources`, `isRumor`) |
| G6 | Filter by source category / official-only in feeds and widgets | `OpsWidgetConfig` has no publisher filter (`opsTypes.ts:76`); sources are per workspace | add `categories`, `publisherIds`, `officialOnly` to widget config and Flux filters | P1 | easy |
| G7 | **Alert delivery to where executives are**: email, Teams/Slack webhook, mobile push | in-app, sound, desktop notification, tab counter only (`centre-operations.md:48`) | email per rule and a 7:30 pre-Comex digest, outgoing webhook per rule (Teams/Slack/Make) | P1 | SMTP + webhook, no AI |
| G8 | **Weekly executive note** (Comex) by theme: law, tax, social, competitors, supply chain, macro | Brief = pre-open/close window, copy/print (`docs line 21`) | Brief template "hebdo" with sections driven by entity groups, plain-text and PDF/print, optional email | P1 | deterministic grouping; no AI summary needed |
| G9 | **JORF / Légifrance** (decrees, laws as published) | no RSS exists; Légifrance 403 | official PISTE API (OAuth, free) or DILA open data feed, polled as a channel; interim: Google News `site:legifrance.gouv.fr` custom feed (poor) | P1 | needs a non-RSS fetcher (explicit scope exception) |
| G10 | **Legislative watch widget** (texts and reports in progress, commission agendas) | feeds not in catalogue | add Assemblée/Sénat feeds (B2) + widget instance LIVE_WIRE with LEGISLATION filter | P1 | feeds verified |
| G11 | Competitor comparison (mentions, event mix per competitor over 7 days) | WATCHLIST_PULSE per entity (velocity), no grouping | group by entity role; show count, tone, event types per competitor | P1 | computable from current data |
| G12 | Sector and entity sharing / multi-user (CFO, comms, legal see the same dashboard) | per-user entities, one user per workspace | team seats, shared dashboard, shareable item link and "send to Comex" | P2 | product work, no data issue |
| G13 | Entity aliases and legal names (SA, SAS, "Groupe X") auto-variants | keywords typed by hand, short Capitalised terms matched case-sensitively | aliases helper: generate variants (no "Groupe", with/without accents, ticker) | P1 | rule-based |
| G14 | Export: item list CSV, Brief PDF, per-theme pack | only journal CSV and Brief copy/print (docs lines 21-22) | CSV export of Flux and alerts, PDF of Brief | P2 | easy |
| G15 | Calendar of scheduled events (ECB/Fed meetings, Parlement agenda, AMF offers, results) | not delivered by design (`docs line 52`) | static official calendars only (ECB Governing Council dates, FOMC dates, Assemblée agenda from feed titles); no unofficial dates | P2 | partial, only primary publishers |
| G16 | Language preference FR/EN per dashboard, hide non-FR press | none | per-source language flag + toggle | P2 | metadata |
| G17 | Source freshness warning for low-frequency primary sources (AMF alerts 52 d, ESRB) so empty silence is not read as health | SOURCE_HEALTH widget | show "official, low frequency" badge | P2 | easy |

---

## F. Event types and lexicon the classifier lacks

Current EVENT_TYPES (`opsTypes.ts:28-40`): CENTRAL_BANK, MACRO, EARNINGS, M_AND_A, RATINGS, GEOPOLITICS_SANCTIONS, ENERGY_COMMODITIES, REGULATION_LEGAL, DEFAULT_RESTRUCTURING, INCIDENT, MARKETS. Probe result: **17 of 30 CEO headlines got no type**. Add (mirror in the frontend type file; `normText` is accent-stripped, lowercase, apostrophes become spaces: write patterns that way). Weights follow existing scale (15-35).

| new type | label | weight | words (normalised) | acronyms (case-sensitive) | example headlines currently untyped |
|---|---|---|---|---|---|
| LEGISLATION | Loi & politique | 25 | `projets? de loi|propositions? de loi|loi de finances|amendements?|decrets?|ordonnances?|journal officiel|motion de censure|assemblee nationale|senat|conseil constitutionnel|conseil d etat|parlement europeen|directive europeenne|reglement europeen|omnibus|premier ministre|remaniement|dissolution|lawmakers|draft law|bill (?:passed|adopted)|executive order|finance bill` | `PLF`, `PLFSS`, `JORF`, `CSRD`, `CSDDD`, `CBAM`, `DORA`, `NIS2`, `AI Act` | "Le gouvernement engage le 49.3 sur le projet de loi de finances 2027"; "Le Sénat adopte un amendement sur la CSRD"; "Accord UE-Mercosur : le Parlement européen se prononce" (note `49.3` normalises to `49 3`: add `49 3`) |
| TAX | Fiscalité | 25 | `impot sur les societes|surtaxe\w*|contribution exceptionnelle|credit d impot|cvae|redressement fiscal|fiscalite|pillar two|taxe (?:minimale|sur les services numeriques|sur les dividendes)|corporate tax|minimum tax|windfall tax|digital services tax|tax (?:credit|hike|cut)|tax reform` | `TVA`, `VAT`, `IS`? (no: too short), `CIR` | "Surtaxe sur l'impôt sur les sociétés : les grandes entreprises mises à contribution"; "Pillar Two : la taxe minimale de 15 % entre en vigueur" |
| LABOUR_SOCIAL | Social & emploi | 25 | `smic|salaire minimum|minimum wage|negociations? salariales?|negociation annuelle obligatoire|assurance chomage|reforme des retraites|pension reform|cotisations? (?:patronales?|sociales?)|syndicats?|collective bargaining|code du travail|rupture conventionnelle|teletravail|greve generale|general strike|plan de sauvegarde de l emploi|labou?r (?:law|shortage|unions?)` | `CGT`, `CFDT`, `NAO`, `URSSAF`, `PSE`, `SMIC` | "Hausse du SMIC de 2 % au 1er novembre"; "Les cotisations patronales augmentent de 1 point"; (the Medef assurance chômage headline only got MACRO via "chômage") |
| SUPPLY_CHAIN | Chaîne d'approvisionnement | 25 | `chaines? d approvisionnement|supply chains?|penuries?|shortages?|rupture d approvisionnement|fret|freight|porte conteneurs?|container ships?|mer rouge|red sea|canal de (?:suez|panama)|suez canal|panama canal|goulots? d etranglement|bottlenecks?|logistique|semi conducteurs?|semiconductors?|nearshoring|relocalisation|reshoring` | | "Mer Rouge : les armateurs détournent leurs porte-conteneurs, les tarifs de fret flambent"; "Pénurie de composants : l'usine de Renault à l'arrêt" |
| CREDIT_FINANCING | Financement & crédit | 30 | `emissions? obligataires?|bond (?:issue|offering|sale)|refinancement|refinancing|credit syndique|syndicated loan|ligne de credit|revolving credit|titrisation|securiti[sz]ation|schuldschein|covenants?|spreads? de credit|credit spreads?|high yield|investment grade|obligations? convertibles?|convertible bonds?|obligation verte|green bond|placement prive|private placement|augmentation de capital|capital increase|rights issue|rachat d actions|share buybacks?|euribor|estr|sofr|taux de refinancement` | `RCF`, `OCEANE`, `ORNANE`, `ESTR`, `SOFR` | "Émission obligataire de 2 milliards d'euros : TotalEnergies lève de la dette à 7 ans"; "Euribor 3 mois à 2,1 %"; "Taux de refinancement : le coût du crédit aux PME baisse" |
| CORPORATE_GOVERNANCE | Gouvernance & dirigeants | 25 | `demission du pdg|depart du pdg|nouveau pdg|nomination (?:du|d un) (?:nouveau )?(?:pdg|directeur general|president)|ceo (?:steps down|resigns|to step down|appointed)|new ceo|conseil d administration|board of directors|assemblee generale|annual general meeting|actionnaires? activistes?|activist investor|proxy fight|remuneration des dirigeants|executive pay|golden parachute|say on pay` | | "Le PDG de Casino démissionne après un désaccord avec le conseil d'administration" (today none) |
| REPUTATION | Réputation & controverse | 25 | `boycott\w*|polemiques?|scandales?|bad buzz|greenwashing|lanceurs? d alerte|whistleblowers?|mis(?:e)? en examen|corruption|fraudes?|pratiques? commerciales? trompeuses?|class action|action de groupe|controvers\w*|backlash|outcry` | `PNF` | "Boycott : la marque visée par une polémique après un bad buzz" (today none) |

Fixes to existing lexicons, same table logic as C1:
- MACRO: add `defaillances? d entreprises|climat des affaires|confiance des (?:menages|chefs d entreprise)|enquete de conjoncture|balance des paiements`; acronym `INSEE` exists, add `Insee` via normalised `insee`. Probe: "Les défaillances d'entreprises atteignent un record selon la Banque de France" got **no type**; it must be MACRO and DEFAULT_RESTRUCTURING.
- MARKETS: add `euro` + `dollar` forms and `euribor|estr|sofr|spread` (or route to CREDIT_FINANCING); today the euro/dollar move headline is never MARKETS.
- GEOPOLITICS_SANCTIONS: add `mercosur|libre echange|free trade (?:deal|agreement)|section 232|anti dumping|omc|wto|etats unis chine`; keep `droits de douane`.
- REGULATION_LEGAL stays for regulators and courts (enforcement: AMF, SEC, Autorité de la concurrence, CNIL, DGCCRF, "enquête", "amende"); LEGISLATION holds the law-making side. Both may apply.
- EARNINGS: add `resultats? du (?:premier|deuxieme|troisieme|quatrieme) (?:trimestre|semestre)|publie ses resultats|results? for the quarter` for FR phrasing; `chiffre d affaires` present.
- IMPACT: `computeImpact` uses type weights; set the new types as above so they are not under-ranked versus CENTRAL_BANK (35); a LEGISLATION item from an official source (trust >= 90) should be boosted.

Also rumour lexicon (`RUMOR`, `signal.ts:269`) misses FR press formulas typical of M&A: `aurait|serait|selon les echos|selon le figaro|selon bloomberg|aurait fait une offre|approche|sonde|discussions exclusives|en lice pour`. Add (conditional "aurait/serait" is common: risk of over-flagging; limit to titles with M_AND_A or CORPORATE_GOVERNANCE).
