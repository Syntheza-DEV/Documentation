# Comparatif des solutions d'ingestion pour la veille stratégique Syntheza

> Document de référence pour les décisions d'architecture, de budget et de roadmap concernant la collecte de données (réseaux sociaux + articles de presse + sources alternatives) sur Syntheza.
>
> Établi en mai 2026. Prix indicatifs constatés sur les sites officiels ; à ré-évaluer chaque trimestre.

---

## 1. Contexte et objectif

Syntheza est une plateforme de veille stratégique assistée par IA. Le pipeline actuel ingère uniquement des flux RSS (3 sources seed, ~20 articles testés). Pour passer à l'échelle "veille stratégique réelle", il faut couvrir :

- Les réseaux sociaux (X/Twitter, Instagram, TikTok, LinkedIn, Facebook, YouTube, Reddit, Threads, Bluesky)
- Les articles de presse mondiaux (médias mainstream + médias spécialisés)
- Les blogs, newsletters, podcasts, forums techniques
- Optionnellement : publications académiques, dépôts régulateurs, transcriptions vidéo

Ce document compare les fournisseurs de données pertinents, leurs coûts réels, et propose **3 paliers de couverture** avec leurs trajectoires de scaling.

---

## 2. Volumétrie mondiale brute vs. pertinent

Avant de chiffrer un coût, il faut comprendre qu'**aucun acteur (pas même Meltwater ou Brandwatch) ne capture l'intégralité d'internet**. La veille consiste à filtrer le pertinent dans un océan.

| Catégorie | Volume mondial brut / jour | Volume "pertinent" filtré |
|---|---|---|
| X/Twitter | ~500 M posts | ~5 M (comptes vérifiés, FR/EN) |
| Instagram | ~100 M posts | ~500 k (comptes >10k followers) |
| TikTok | ~50 M vidéos | ~300 k |
| LinkedIn | ~10 M posts | ~200 k (entreprises + influenceurs) |
| Facebook | ~50 M posts publics | ~100 k (pages >10k abonnés) |
| YouTube | ~3 M vidéos | ~50 k |
| Reddit | ~3 M posts + commentaires | ~300 k |
| Articles presse | ~5 M (Google News : 80k sources) | ~500 k |
| Blogs + newsletters (Substack, Medium, RSS) | ~2 M posts | ~50 k |
| Podcasts | ~100 k épisodes | ~10 k (transcription nécessaire) |
| Forums tech (HN, Stack Overflow, Discords publics) | ~1 M | ~50 k |
| **Total "pertinent"** | — | **~7-8 M items / jour** |
| | | **≈ 220 M / mois** |

Conclusion : "tout savoir" en couvrant 100% est économiquement impossible. La vraie question est : **quel pourcentage de couverture + quelle latence**.

---

## 3. Fournisseurs comparés

### 3.1 Apify (scraping géré, store d'actors)

**Site** : https://apify.com
**Modèle** : plateforme cloud + marketplace d'actors (scrapers) tarifés au résultat ou à l'exécution.

#### Plans plateforme

| Plan | Prix mensuel | Crédits inclus | RAM | Concurrent runs | Support |
|---|---|---|---|---|---|
| Free | $0 | $5 | 8 GB | 25 | Communauté |
| Starter | $29 | $29 | 32 GB | 32 | Chat |
| Scale | $199 | $199 | 128 GB | 128 | Priority chat |
| Business | $999 | $999 | 256 GB | 256 | Account manager |
| Enterprise | Sur devis | — | — | — | Dédié |

Les crédits inclus sont dépensables sur les actors. Au-delà : pay-as-you-go au même tarif (pas de markup). Les plans payants donnent des discounts sur le Store (Bronze / Silver / Gold).

#### Prix des actors "réseaux sociaux"

| Réseau | Actor recommandé | Tarif unitaire |
|---|---|---|
| X / Twitter | Tweet Scraper V2 | **$0.25 - $0.40 / 1 000 tweets** |
| Instagram | Instagram Scraper (Apify officiel) | **$1.50 / 1 000 posts** ($2.30/1k commentaires) |
| TikTok | TikTok Scraper (Pay Per Result) | **$0.30 / 1 000 posts** |
| LinkedIn | LinkedIn Post Scraper | **$1.00 / 1 000 résultats** |
| Facebook | Facebook Posts Scraper | **$2.00 / 1 000 posts** |
| YouTube | YouTube Scraper | **$2.40 / 1 000 vidéos** |
| Reddit | Reddit Scraper Lite | ~$0.40 - $0.50 / 1 000 posts |
| Facebook Ad Library | Facebook Ad Library Scraper | $0.75 / 1 000 résultats |

#### Forces
- Pas d'infra à gérer (proxy, retries, captchas, parsing inclus)
- Marketplace avec actors maintenus (et notés)
- API REST + webhooks + SDK Node/Python natifs
- Tarification au résultat = budget prévisible

#### Faiblesses
- Coûte cher à très grande échelle (>10M items/mois)
- Dépendance forte aux mainteneurs d'actors tiers (un actor peut casser sans préavis)
- Proxies datacenter par défaut ; le résidentiel (nécessaire pour LinkedIn/Instagram/Facebook) coûte extra
- Risque légal sur certaines plateformes (Apify ne couvre pas la responsabilité ToS)

---

### 3.2 APIs officielles des plateformes

| Plateforme | Offre | Prix | Verdict |
|---|---|---|---|
| X / Twitter API | Basic ($100/mois) → 10k tweets | $100 / mois | Trop limité |
| X / Twitter API | Pro ($5 000/mois) → 1M tweets | $5 000 / mois | OK pour MVP scale |
| X / Twitter API | Enterprise (firehose) | $42 000+ / mois | Tier 3 only |
| Meta (Graph API) | Pages + Instagram Business | Gratuit avec ToS strict | Limité aux comptes business pré-autorisés |
| LinkedIn API | Marketing Developer Platform | Gratuit + approval Microsoft | Quasi-impossible d'avoir un accès large |
| YouTube Data API v3 | Quota 10k units/jour | Gratuit | ~100 vidéos détaillées/jour, sinon $50 par 100k quota |
| Reddit API | OAuth, 100 req/min | Gratuit (commercial = paywall depuis 2023) | $0.24 / 1k requests pour usage commercial |
| TikTok Research API | Réservé académiques | Gratuit mais restreint | Inutilisable pour Syntheza (besoin approval) |
| Bluesky AT Protocol | Firehose public | Gratuit | À exploiter activement |
| Mastodon | Streaming API par instance | Gratuit | Volume marginal mais signal qualité |

**Verdict global** : les APIs officielles sont attractives en théorie mais (1) coûtent souvent plus cher qu'Apify pour le même volume, (2) imposent des restrictions ToS lourdes, (3) demandent un workflow d'approbation incertain. Apify reste plus pragmatique pour 90% des cas, sauf X Enterprise et Twitter Basic pour des cas précis.

---

### 3.3 Agrégateurs de news

| Service | Couverture | Prix | Verdict pour Syntheza |
|---|---|---|---|
| **GDELT 2.0** | 250k articles/jour, 100+ langues | **Gratuit** | Indispensable, base de notre tier 1 |
| Google News RSS | Top hits par requête | Gratuit (non officiel, fragile) | OK pour MVP |
| NewsAPI.org | 80k sources, max 1 mois historique | $449/mois (Business) | Trop cher vs alternatives |
| NewsAPI.ai (Event Registry) | Articles enrichis NLP + événements | $500-2 000/mois | Recommandé pour Tier 1 |
| Mediastack | 7 500 sources | $250/mois (Standard) | Alternative budget |
| Common Crawl | Snapshots web mensuels (~3 PB) | Gratuit (S3 requester pays) | Utile pour audits, pas le temps réel |
| Feedly Enterprise | RSS curé + AI | $750-2 500/mois | Bon ROI si peu de devs RSS in-house |
| Diffbot Knowledge Graph | Web semantic crawl | $300-3 000/mois | Overkill sauf besoin de KG |
| webz.io (anciennement webhose) | News + forums | $500-5 000/mois | Concurrent direct, utile en complément |

---

### 3.4 Proxy & infra de scraping (si on bypasse Apify)

| Service | Type | Prix | Usage Syntheza |
|---|---|---|---|
| **Bright Data** | Résidentiel + ISP + datacenter | $15/GB résidentiel | Si scraping in-house |
| Smartproxy | Résidentiel | $7/GB | Alternative low-cost |
| Oxylabs | Premium résidentiel + scraper APIs | $12-15/GB | Si exigences SLA |
| Zyte (Scrapy Cloud) | Concurrent direct d'Apify | Variable | Alternative crédible à Apify |
| ScraperAPI | API simple | $49-249/mois | Pour scraping ponctuel léger |

---

### 3.5 Briques IA & enrichissement (post-ingestion)

Une fois les items collectés, il faut les transformer en signal exploitable. Coûts à ne pas oublier :

| Brique | Solution | Prix indicatif |
|---|---|---|
| Embeddings | OpenAI text-embedding-3-small | $0.02 / 1M tokens (~$0.004 par 1k items) |
| Embeddings | Voyage AI / Cohere | $0.05-0.10 / 1M tokens (meilleur ranking) |
| Vector DB | Qdrant Cloud | $50-1 500/mois selon volume |
| Vector DB | Pinecone | $70-2 000/mois |
| Résumé IA | Gemini 2.5 Flash (déjà utilisé Syntheza) | $0.075 input + $0.30 output / 1M tokens |
| Résumé IA | Claude Haiku 4.5 | $1 input + $5 output / 1M tokens |
| Transcription | OpenAI Whisper API | $0.006 / minute audio |
| Transcription | Deepgram Nova-2 | $0.0043 / minute (volume) |
| Search engine | Meilisearch self-hosted | $50-300/mois VM |
| Search engine | Elastic Cloud | $200-2 000/mois |
| Search engine | Typesense Cloud | $100-1 000/mois |

---

## 4. Trois paliers de couverture avec budget réel

### Tier 0 — MVP / Démo (50 articles/jour ≈ 1 500/mois)

Cible : valider techniquement le pipeline de bout en bout sans engager de budget.

| Brique | Coût mensuel |
|---|---|
| Apify Free (crédits $5) | $0 |
| RSS in-house (3-20 sources) | $0 |
| GDELT free | $0 |
| Reddit + HackerNews API gratuites | $0 |
| Embeddings + Gemini (volume négligeable) | <$5 |
| Postgres existant (Prisma) | inclus |
| **Total** | **$0 - $10 / mois** |

Couverture estimée : <0.001% du volume mondial pertinent. Suffit pour démo et acquisition early adopters.

---

### Tier 1 — Newsroom Pro (10 k items/jour ≈ 300 k/mois)

Cible : produit commercialisable pour des équipes comm, marketing ou intelligence économique avec 5-50 utilisateurs internes.

| Brique | Coût mensuel |
|---|---|
| Apify Starter ($29) + dépassements (~$50) | $80 |
| Proxy résidentiel léger (5-10 GB) | $75-150 |
| NewsAPI.ai (Standard) | $500 |
| GDELT free + Feedly Pro+ ($12) | $12 |
| Storage (10 GB/mois cumulé) | $50 |
| Embeddings (300 k × 200 tokens) | $12 |
| Résumés Gemini Flash (300 k items) | $90 |
| Vector DB (Qdrant managed Small) | $80 |
| Transcription audio/vidéo (~3 000 vidéos × 3 min) | $540 |
| Search (Meilisearch self-hosted) | $80 |
| Compute (1 VM workers) | $100 |
| Monitoring (Grafana Cloud free) | $0 |
| **Total infra** | **~$1 700 / mois** |

Couverture estimée : ~5% du volume pertinent mondial. Latence : 30 min à 2h selon source.

---

### Tier 2 — Intelligence Économique (300 k items/jour ≈ 9 M/mois)

Cible : SaaS payant à $100-500/mois par utilisateur ou contrats enterprise mid-market.

| Brique | Coût mensuel |
|---|---|
| Apify Scale ($199) + dépassements (~$3 000) | $3 200 |
| Proxy résidentiel (50 GB) | $750 |
| News APIs cumulées (NewsAPI.ai + Mediastack) | $750 |
| GDELT + sources RSS étendues | inclus |
| Storage (100 GB/mois) | $300 |
| Embeddings (9 M × 200 tokens) | $36 |
| Résumés Gemini Flash (9 M items, batch) | $900 |
| Vector DB managed (Qdrant Pro) | $400 |
| Transcription (15 k vidéos × 3 min) | $2 700 |
| Search (OpenSearch managed) | $400 |
| Compute (cluster K8s, 3-5 nodes) | $800 |
| CDN + assets | $100 |
| Monitoring (Datadog APM) | $200 |
| **Total infra** | **~$10 500 / mois** |
| + Équipe (2 data eng + 1 ML, FR salaires bruts chargés) | ~$25 000 |
| **Grand total avec équipe** | **~$36 000 / mois** |

Couverture estimée : ~15-20% du volume pertinent. Latence : 5-30 min.

---

### Tier 3 — Panoptique (10 M items/jour ≈ 300 M/mois)

Cible : concurrencer Meltwater, Brandwatch, Talkwalker, Cision sur le marché enterprise. Contrat moyen $50k-500k/an par client.

| Brique | Coût mensuel |
|---|---|
| Apify Enterprise (négocié) | $80 000 - 150 000 |
| Twitter API Enterprise (firehose) | $42 000 |
| Proxies résidentiels + ISP (1-2 TB) | $20 000 |
| News APIs full firehose | $10 000 - 30 000 |
| ClickHouse cluster + S3 archives (3+ TB) | $8 000 |
| Embeddings + reranking | $2 000 |
| IA résumés + classification topics (modèles dédiés) | $25 000 |
| Transcription massive (100k+ vidéos) | $40 000 |
| Search distribué (ES cluster prod) | $8 000 |
| Compute (50-100 nodes K8s) | $15 000 |
| Observabilité + alerting | $3 000 |
| **Total infra** | **~$250 000 - 350 000 / mois** |
| + Équipe (10-20 personnes : data, ML, ops, support) | ~$200 000 |
| **Grand total** | **~$500 000 / mois ≈ $6 M / an** |

Couverture estimée : 40-60% du volume pertinent. C'est ce que Meltwater opère (CA ~$500M, marge ~30%).

---

## 5. Comparatif consolidé

| Critère | Tier 0 MVP | Tier 1 Newsroom | Tier 2 IE | Tier 3 Panoptique |
|---|---|---|---|---|
| Items/jour | 50 | 10 000 | 300 000 | 10 000 000 |
| Items/mois | 1 500 | 300 000 | 9 000 000 | 300 000 000 |
| Couverture mondiale | 0.001% | ~5% | ~15-20% | 40-60% |
| Latence typique | 1-6 h | 30 min - 2 h | 5-30 min | <5 min |
| Budget infra mensuel | $0-10 | $1 700 | $10 500 | $250 000-350 000 |
| Équipe nécessaire | 1 dev (solo) | 1-2 devs | 3-4 devs spécialisés | 10-20 personnes |
| Budget total mensuel | $10 | $1 700 | $36 000 | $500 000 |
| Budget annuel | $120 | $20 k | $430 k | $6 M |
| Seuil de rentabilité (ARR break-even) | n/a | ~$50 k ARR | ~$700 k ARR | ~$15 M ARR |

---

## 6. Recommandation pour Syntheza

### État actuel
- Backend mature (55 routes, 370 tests, déployé prod, Swagger)
- Frontend web + mobile fonctionnels, en partie connectés
- Pipeline RSS opérationnel, ingestion Twitter en stub
- Équipe : 3 personnes (Ilhan algo/infra, Tristan back, Raphael front)
- Pas de clients payants identifiés à date

### Trajectoire conseillée

1. **Phase 1 — Maintenant à 3 mois (Tier 0)**
 Brancher Apify Starter ($29/mois), ingérer 5-10 actors ciblés (Twitter, Reddit, LinkedIn Posts, YouTube), garder GDELT + RSS en complément. Coût total : **<$100/mois**. Objectif : prouver la couverture multi-sources et le trust scoring.

2. **Phase 2 — 3 à 9 mois (Tier 1)**
 Premiers clients beta payants. Ajouter proxies résidentiels, NewsAPI.ai, transcription des vidéos top trust. Budget cible : **$1 500-2 000/mois infra**, justifié dès 5 clients à $200/mois.

3. **Phase 3 — 9-18 mois (Tier 2)**
 Décollage SaaS, contrats mid-market. Recrutement d'un data engineer dédié à l'ingestion. Budget : **~$10k/mois infra + équipe**. Cible ARR ~$500k.

4. **Phase 4 — au-delà (Tier 3)**
 À envisager uniquement avec une levée Série A et un positionnement enterprise clair. Pas avant 24 mois.

### Pièges à éviter

- **Surinvestir tôt dans le scraping** : la valeur de Syntheza est le **filtrage intelligent** (trust scoring, dédup, ranking personnalisé), pas la couverture brute. Mieux vaut 5% de sources avec excellente UX que 50% noyés dans le bruit.
- **Dépendre à 100% d'Apify** : un changement de pricing ou la désactivation d'un actor peut casser le pipeline. Conserver un fallback RSS + GDELT pour les sources critiques.
- **Négliger la conformité** : RGPD pour les données nominatives, ToS LinkedIn/Meta pour les scrapes massifs. Prévoir un volet juridique avant de monter en tier.
- **Oublier les coûts cachés** : transcription audio/vidéo et embeddings explosent vite à grande échelle (parfois > coût d'ingestion brute).
- **Croire au "tout exhaustif"** : même les leaders du marché n'atteignent pas 60% de couverture. La narrative produit doit être "le plus pertinent", pas "tout".

### Décision recommandée à court terme

**Activer Apify Starter ($29/mois) cette semaine.** Coût marginal négligeable, débloque la diversité des sources, et permet de chiffrer précisément le volume réel ingéré avant de passer au Tier 1.

---

## 7. Annexes

### 7.1 Lexique
- **Actor (Apify)** : programme de scraping packagé sur la plateforme Apify, lancé via API.
- **Firehose** : flux temps réel exhaustif d'une plateforme (ex. Twitter Enterprise).
- **PPR (Pay Per Result)** : facturation à l'item extrait, vs. facturation au temps de compute.
- **CU (Compute Unit)** : unité de calcul Apify, $0.13 à $0.20 selon le plan.
- **GDELT** : projet open data trackant les événements mondiaux via la presse.
- **Common Crawl** : crawl ouvert du web, snapshots mensuels disponibles sur S3.

### 7.2 Sources consultées
- Apify pricing : https://apify.com/pricing
- Apify Store catégorie réseaux sociaux : https://apify.com/store?categories=SOCIAL_MEDIA
- Bright Data pricing : https://brightdata.com/pricing
- GDELT 2.0 : https://www.gdeltproject.org
- Twitter API tiers : https://developer.x.com/en/products/twitter-api
- NewsAPI.ai : https://newsapi.ai
- OpenAI pricing : https://openai.com/api/pricing
- Gemini pricing : https://ai.google.dev/pricing
- Deepgram pricing : https://deepgram.com/pricing

### 7.3 Hypothèses de calcul
- Mix sources réseaux sociaux : 35% Twitter, 15% Instagram, 10% TikTok, 10% LinkedIn, 10% Facebook, 15% YouTube, 5% Reddit
- Coût transcription : 3 min/vidéo en moyenne, Whisper API
- Coût résumé IA : 500 tokens input + 200 output / item, Gemini 2.5 Flash
- Salaires France : data engineer ~7 000 € brut chargé / mois, ML engineer ~9 000 €
- Taux de change : 1 USD ≈ 0.92 EUR (mai 2026)
- Pas inclus dans les calculs : marketing, ventes, juridique, frais généraux, marges fournisseurs IT

---

*Document à mettre à jour trimestriellement. Dernière révision : mai 2026.*
