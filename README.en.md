# Syntheza — Project Documentation (English)

Syntheza is an AI-assisted strategic watch platform. It aggregates content from many publishers, deduplicates and summarizes it with AI, and scores the reliability of each item through a Trust Factor.

## Shared context — start here

Read [`contexte/README.md`](./contexte/README.md) for the cleaned, self-contained project context in French: product intent, decisions, acceptance history, constraints, operations and jury preparation. The entry point includes a reading order and a ready-to-copy Claude instruction. No private memory or agent session is required; this context is separate from the technical reference documentation.

## Applications
- **Backend API** — Node 20, Express, Prisma, PostgreSQL
- **Web app** — React 19, Vite, Tailwind v4, Radix
- **Mobile app** — React Native, Expo, Tamagui, 7 languages

## Core engine
- Deterministic polling (scheduled RSS ingestion via cron jobs)
- SHA-256 deduplication + URL canonicalization
- AI layer (Gemini): article summary and Trust Factor
- Deterministic ranking: freshness 35 / engagement 25 / trust 25 / completeness 15

## Trust Factor
- **V1 (production)** — pure source corroboration by the AI across other publishers.
- **V2 (roadmap)** — weighted composite: source reputation, quantified corroboration, metadata, bounded AI judgment.

## Infrastructure
Docker Compose, Traefik, GitHub Actions CI/CD, single-VPS deployment behind Cloudflare.