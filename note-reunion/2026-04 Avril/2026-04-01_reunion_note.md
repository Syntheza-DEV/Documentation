# [Weekly] – Google Auth, Responsive & Plan de fiabilisation backend

**Date** : 01/04/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Raphael

---

## Contexte

Réunion hebdomadaire de suivi. Point sur la finalisation du responsive et de
Google Auth côté web, et cadrage du chantier de fiabilisation backend (tests,
robustesse) avant l'activation des vraies sources de données. Tristan toujours
indisponible.

## Revue des objectifs

* 🔄 Finaliser le responsive du frontend web : **En cours** (quasi complet)
* 🔄 Bouton Google Sign-In sur le web : **En cours** (endpoint backend prêt depuis mars)
* ⏳ Vue profil utilisateur web : **À faire**
* ⏳ Premières sources de données réelles (feed) : **À planifier**

## Ordre du jour

1. Avancement responsive + Google Auth web (Raphael)
2. Plan de fiabilisation backend + activation IA (Ilhan)
3. Sources de données réelles pour le feed
4. Organisation avec l'absence prolongée de Tristan

## Discussions & Décisions

### Responsive & Google Auth web
- **Discussion** : Le responsive couvre désormais l'ensemble des vues. Raphael
  intègre le bouton Google Sign-In sur la page de login (l'endpoint
  `POST /api/user/google` est prêt côté backend).
- **Décision** : Finaliser Google Auth + derniers ajustements responsive cette semaine.
- **Responsable** : @Raphael
- **Échéance** : 08/04/2026

### Fiabilisation backend & activation IA
- **Discussion** : Ilhan propose un sprint d'industrialisation : migration vers
  pnpm, montée en charge de la couverture de tests, et activation des résumés IA
  (clé Gemini en prod) avec un garde-fou de budget.
- **Décision** : Lancer le sprint d'industrialisation backend dans la foulée.
- **Responsable** : @Ilhan

### Absence de Tristan
- **Discussion** : Tristan reste injoignable (examens). Le backend repose
  entièrement sur Ilhan depuis février.
- **Décision** : Ilhan assume la totalité du backend, documentation rigoureuse maintenue.

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Finaliser Google Auth + responsive web | @Raphael | 08/04/2026 | 🔄 En cours |
| Lancer migration pnpm + suite de tests backend | @Ilhan | 08/04/2026 | ⏳ À faire |
| Activer les résumés IA (Gemini) en prod + budget | @Ilhan | 18/04/2026 | ⏳ À faire |
| Préparer les premières sources RSS réelles | @Ilhan | 18/04/2026 | ⏳ À faire |

## Points en suspens

- Tristan toujours indisponible, pas de visibilité sur son retour
- Vue profil web à démarrer

## Objectifs prochaine réunion

1. Google Auth + responsive web livrés
2. Sprint d'industrialisation backend lancé (pnpm + tests)
3. Plan des premières sources de données validé

## Prochaine réunion

**Date prévue** : 08/04/2026
**Sujets** : Démo Google Auth web, avancement sprint backend, sources de données

---

## 📝 Notes brutes

- Raphael finalise google auth + responsive, endpoint backend déjà prêt
- Ilhan lance le gros chantier pnpm + tests + activation IA gemini
- Toujours pas de nouvelles de Tristan, ilhan tient tout le backend
- Besoin de vraies sources RSS pour alimenter le feed et bosser le ranking
