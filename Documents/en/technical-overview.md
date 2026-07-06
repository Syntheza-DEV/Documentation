# Syntheza — Technical Overview (EN)

Syntheza is an AI-assisted strategic watch platform. It aggregates content from
multiple publishers, deduplicates it, summarizes it and scores its trustworthiness,
then delivers a ranked, personalized feed on web and mobile.

## Stack

- **Backend**: Node 20, TypeScript, Express, Prisma, PostgreSQL 15, Zod, JWT (dual web/mobile)
- **Web**: React 19, Vite, Tailwind v4, Radix
- **Mobile**: React Native 0.81, Expo SDK 54, Tamagui, i18next (7 languages)
- **AI**: Gemini 2.5 Flash (summarization + trust scoring)
- **Infra**: Docker, Traefik, GitHub Actions (CI/CD), Cloudflare

## Watch engine

Deterministic cron polling -> RSS ingestion -> SHA-256 deduplication ->
AI step (summary + Trust Factor) -> deterministic ranking
(freshness 35% / engagement 25% / trust 25% / completeness 15%).

The LLM never drives polling: cron jobs handle acquisition, the LLM only runs
bounded summarization and trust steps.

## Trust Factor

- **V1 (production)**: pure corroboration via Gemini across other publishers.
  When no similar source exists, the score defaults to a neutral 50 without an AI call.
- **V2 (roadmap)**: weighted composite (source reputation, quantified corroboration,
  metadata quality, bounded LLM judgment) for an auditable, explainable score.

## Data model

`Publisher` (global media) -> one or more `Channel` (RSS feed, etc.) -> `Item`
(article, deduplicated per publisher via fingerprint + canonical URL). Users
subscribe to Publishers, not to individual sources.

## Deployment

Two OVH VPS (API + web), Docker Compose behind Traefik with automatic TLS,
GitHub Actions deploying on push to main. Mobile is built locally (Xcode) and
distributed to TestFlight.
