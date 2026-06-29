# [Weekly] – Point court : période examens PGE5

**Date** : 29/04/2026
**Heure** : 15h - 15h30
**Participants** : Raphael, Ilhan
**Animateur** : Raphael
**Rédacteur** : Raphael

---

## Contexte

Point hebdomadaire court. Raphael et Ilhan sont en pleine période d'examens
PGE5, le développement est volontairement ralenti. Réunion centrée sur le
maintien du cap et l'amorce de la prochaine grande étape technique.

## Revue des objectifs

* ✅ Jalons industrialisation + produit branché : **Actés** (réunion du 22/04)
* ℹ️ Développement : **En pause partielle** (examens)
* 🔄 Réflexion modèle de données : **Amorcée**

## Ordre du jour

1. Organisation pendant les examens
2. Amorce de la refonte du modèle de données
3. Surveillance prod (conso IA)

## Discussions & Décisions

### Organisation examens
- **Discussion** : Semaine d'examens, peu de disponibilité côté dev. Aucun
  nouveau chantier lourd lancé cette semaine.
- **Décision** : Pause partielle assumée, reprise pleine prévue le 13/05. Pas de
  weekly dense la semaine du 06/05 (examens).
- **Responsable** : Équipe

### Amorce refonte data model
- **Discussion** : Le modèle actuel (Source / IngestedItem) montre ses limites :
  difficile de gérer proprement un éditeur possédant plusieurs flux, et la
  déduplication des articles est fragile.
- **Décision** : Cadrer à la reprise un refacto `Publisher / Channel / Item`
  avec un vrai moteur de déduplication.
- **Responsable** : @Ilhan
- **Échéance** : 13/05/2026

### Surveillance prod
- **Discussion** : La prod tourne, l'IA est active. Surveillance de la
  consommation Gemini.
- **Décision** : Garder un œil sur le budget pendant la pause.
- **Responsable** : @Ilhan

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Préparer le cadrage du refacto Publisher/Channel/Item | @Ilhan | 13/05/2026 | ⏳ À faire |
| Surveiller la conso IA en prod | @Ilhan | Continu | 🔄 En cours |
| Reprise pleine du développement | Équipe | 13/05/2026 | ⏳ En attente |

## Points en suspens

- Examens en cours (disponibilité réduite)
- Refonte data model à valider à la reprise

## Objectifs prochaine réunion

1. Reprise complète post-examens
2. Refonte Publisher/Channel/Item cadrée et validée
3. Plan du rebuild frontend web

## Prochaine réunion

**Date prévue** : 13/05/2026
**Sujets** : Reprise, validation refacto data model, rebuild web

---

## 📝 Notes brutes

- semaine d'exams, on ralentit, pas de gros chantier
- pas de weekly dense la semaine du 06/05 (exams)
- le modèle Source/IngestedItem coince : multi-flux + dedup fragile
- idée : refacto Publisher/Channel/Item avec moteur de dedup, à cadrer le 13/05
- prod ok, on surveille la conso gemini
