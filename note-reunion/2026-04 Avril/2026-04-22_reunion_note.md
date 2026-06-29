# [Weekly] – Bilan sprint : industrialisation & produit branché end-to-end

**Date** : 22/04/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Réunion de bilan du sprint d'industrialisation. Deux jalons structurants sont
actés : la base technique est industrialisée (pnpm, tests, IA), et le produit
est désormais branché de bout en bout (web + mobile sur la vraie API). La
période d'examens PGE5 arrive, ralentissement à prévoir.

## Revue des objectifs

* ✅ Migration pnpm + suite de tests : **OK** (321 tests backend, 65 mobile)
* ✅ Mobile connecté à l'API réelle (phases 1 à 6) : **OK**
* ✅ IA activée en prod : **OK** (Gemini 2.5, trust scoring robuste)

## Ordre du jour

1. Bilan jalon « industrialisation »
2. Bilan jalon « fin des mocks / produit branché »
3. Upgrade Gemini + trust scoring
4. Organisation période examens

## Discussions & Décisions

### Jalon 1 — Industrialisation
- **Discussion** : Migration pnpm sur les 3 repos, 321 tests Vitest backend, 65
  tests Jest mobile, seed de données riche, AI budget limiter et crons
  automatiques (auto trust score, auto ranking).
- **Décision** : Jalon acté. La base technique est saine et testée.
- **Responsable** : @Ilhan

### Jalon 2 — Produit branché de bout en bout
- **Discussion** : Web et mobile sont branchés sur le vrai backend, plus aucun
  mock. Fonctionnalités sociales complètes (bookmarks, follow, subscriptions,
  commentaires, notifications, avatar), TrustBadge interactif, dark mode,
  contraste WCAG AA, tests E2E Maestro côté mobile.
- **Décision** : Jalon acté. Le parcours utilisateur fonctionne réellement.
- **Responsable** : @Ilhan (mobile/backend), @Raphael (web)

### Upgrade Gemini & trust scoring
- **Discussion** : Passage de Gemini 2.0 à 2.5 Flash, trust scoring robuste
  (fallback NaN, parsing regex du JSON). README mis à jour (370 tests, 55
  endpoints, pnpm).
- **Décision** : Modèle 2.5 retenu pour le ratio coût/qualité.
- **Responsable** : @Ilhan

### Période examens
- **Discussion** : Raphael et Ilhan entrent en examens PGE5.
- **Décision** : Ralentissement du dev sur ~2 semaines, suivi léger maintenu.

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Geler les nouveaux chantiers pendant les examens | Équipe | 13/05/2026 | ⏳ Planifié |
| Réfléchir aux limites du modèle de données actuel | @Ilhan | 13/05/2026 | ⏳ À faire |
| Surveiller la conso IA / budget Gemini en prod | @Ilhan | Continu | 🔄 En cours |

## Points en suspens

- Twitter : API v2 payante, stub en place
- 2FA : flag en DB seulement, TOTP non implémenté
- Refresh token rotation non implémenté

## Objectifs prochaine réunion

1. Reprise post-examens
2. Cadrage de la refonte du modèle de données
3. Plan du rebuild frontend web

## Prochaine réunion

**Date prévue** : 29/04/2026 (point court avant examens)
**Sujets** : Organisation période examens, amorce refonte data model

---

## 📝 Notes brutes

- bilan sprint : 321 tests back + 65 mobile, gemini activé, budget limiter
- web + mobile branchés sur le vrai backend, plus de mock du tout
- social complet : bookmarks, follow, subs, comments, notifs, avatar
- gemini 2.0 -> 2.5, trust scoring robuste (nan fallback + regex)
- exams pge5 -> on ralentit 2 semaines
- a creuser : le modèle Source/IngestedItem commence à coincer
