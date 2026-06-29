# [Weekly] – Lancement du sprint d'industrialisation (pnpm, tests, IA)

**Date** : 08/04/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Raphael
**Rédacteur** : Ilhan

---

## Contexte

Google Auth et le responsive web sont livrés. La réunion acte le lancement du
gros chantier d'industrialisation backend + mobile (migration pnpm, tests,
activation IA) et la suppression des données mock côté front.

## Revue des objectifs

* ✅ Google Auth + responsive web livrés (Raphael) : **OK**
* 🔄 Migration pnpm + suite de tests backend : **Lancée**
* ⏳ Sources de données réelles : **À démarrer**

## Ordre du jour

1. Validation Google Auth + responsive web
2. Cadrage sprint industrialisation (pnpm, tests, IA)
3. Refonte mobile par phases (connexion API réelle)
4. Préparation des sources RSS

## Discussions & Décisions

### Google Auth & responsive — livrés
- **Discussion** : Le bouton Google Sign-In est opérationnel sur le web, le
  responsive est finalisé sur l'ensemble des vues.
- **Décision** : Chantier clos côté web, on bascule sur l'intégration des
  fonctionnalités sociales et la stabilisation.
- **Responsable** : @Raphael

### Sprint industrialisation
- **Discussion** : Migration des 3 repos vers pnpm, structuration des suites de
  tests, enrichissement du seed de données. Objectif : une base saine avant
  d'activer l'IA et les vraies sources.
- **Décision** : Sprint lancé, priorité backend puis mobile.
- **Responsable** : @Ilhan
- **Échéance** : 18/04/2026

### Refonte mobile par phases
- **Discussion** : Ilhan structure la refonte mobile en phases : fondation
  (interceptors, QueryClient, stores), services API, connexion de tous les
  écrans au vrai backend (fin des mocks), suppression du dead code, puis tests.
- **Décision** : Avancer phase par phase, mobile branché sur l'API réelle.
- **Responsable** : @Ilhan

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Migration pnpm backend + mobile | @Ilhan | 18/04/2026 | 🔄 En cours |
| Connexion des écrans mobile à l'API réelle (phases) | @Ilhan | 18/04/2026 | 🔄 En cours |
| Activer Gemini en prod + AI budget limiter | @Ilhan | 18/04/2026 | ⏳ À faire |
| Stabiliser l'intégration sociale côté web | @Raphael | 15/04/2026 | ⏳ À faire |

## Points en suspens

- Sources RSS réelles à mettre en place
- Tristan toujours absent

## Objectifs prochaine réunion

1. Sprint industrialisation avancé (pnpm migré, tests en hausse)
2. Mobile connecté à l'API réelle (premières phases)
3. IA activable en prod

## Prochaine réunion

**Date prévue** : 15/04/2026
**Sujets** : Avancement sprint, démo mobile connecté, état activation IA

---

## 📝 Notes brutes

- Google auth + responsive web OK, chantier web clos
- On lance le sprint indus : pnpm sur les 3 repos + tests + seed riche
- Mobile refait en phases : fondation, services, connexion API, clean, tests
- Objectif : base saine avant d'activer gemini et les vraies sources
