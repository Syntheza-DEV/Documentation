# [Weekly] – Responsive Web, Routes Backend & Roadmap Prochaines Etapes

**Date** : 25/03/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Reunion de suivi hebdomadaire. Le frontend web a bien progresse cote responsive et implementation. De nouvelles routes backend ont ete ajoutees. L'equipe fait le point sur les avancees et definit les priorites pour la suite : fiabilisation backend, creation de sources de donnees, finalisation responsive, vue profil et Google Auth web.

---

## Revue des objectifs

* ✅ Backend MVP finalise (code review + doc technique) : **Fait** (27 issues patchees, doc complete)
* ✅ Preferences integrees cote frontend web : **Fait** (Raphael a branche les endpoints)
* 🔄 Bouton Google Auth visible sur le web : **En cours** (endpoint backend pret, integration frontend a finaliser)
* 🔄 Plan d'integration feed/likes/comments cote frontend : **En cours** (feed web encore partiellement sur mock data)

---

## Ordre du jour

1. Avancement du frontend web (responsive + implementation)
2. Nouvelles routes backend implementees
3. Objectifs pour la prochaine phase du projet
4. Repartition des taches

---

## Discussions & Decisions

### Avancement frontend web – Responsive & Implementation

* **Discussion** :
  Raphael a bien avance sur l'implementation du frontend web. Le responsive est complet et bien avance sur la version web : les pages s'adaptent correctement aux differentes tailles d'ecran (desktop, tablette, mobile). Le travail couvre l'ensemble des vues existantes.

* **Decision** :
  Continuer sur cette lancee. Il reste a finaliser les derniers ajustements responsive et passer a l'implementation de la vue profil utilisateur.

* **Responsable** : @Raphael
* **Echeance** : 01/04/2026

---

### Nouvelles routes backend

* **Discussion** :
  De nouvelles routes backend ont ete implementees pour completer le MVP. Ces routes viennent renforcer la couverture fonctionnelle de l'API et preparent le terrain pour l'integration frontend.

* **Decision** :
  Les routes sont operationnelles. Prochaine etape : fiabiliser l'ensemble du backend (tests supplementaires, gestion d'erreurs, robustesse) avant de passer a la phase d'integration avec de vraies sources de donnees.

* **Responsable** : @Ilhan

---

### Objectifs pour la suite

* **Discussion** :
  L'equipe a defini les priorites pour les prochaines semaines :
  1. **Fiabiliser le backend** : renforcer les tests, la gestion d'erreurs et la robustesse des endpoints existants
  2. **Creer les premieres sources de donnees** : mettre en place de vraies sources (RSS, etc.) pour alimenter le feed et commencer a travailler sur l'algorithme de recommandation
  3. **Finir le responsive du frontend web** : derniers ajustements pour une couverture complete
  4. **Vue profil cote frontend web** : implementer la page de profil utilisateur avec les informations et preferences
  5. **Google Auth cote frontend web** : ajouter le bouton Google Sign-In sur la page de login

* **Decision** :
  Ces 5 chantiers sont valides comme roadmap immediate. La fiabilisation backend et les sources de donnees sont prioritaires car elles conditionnent le fonctionnement du feed (coeur du produit).

* **Responsable** : @Ilhan (backend, sources, algorithme), @Raphael (responsive, profil, Google Auth web)

---

## Actions a mener

| Action | Responsable | Echeance | Statut |
|--------|-------------|----------|--------|
| Fiabiliser le backend (tests, robustesse, error handling) | @Ilhan | 08/04/2026 | ⏳ A faire |
| Creer les premieres sources de donnees (RSS, feed reel) | @Ilhan | 08/04/2026 | ⏳ A faire |
| Travailler sur l'algorithme de recommandation du feed | @Ilhan | 15/04/2026 | ⏳ A faire |
| Finaliser le responsive du frontend web | @Raphael | 01/04/2026 | 🔄 En cours |
| Implementer la vue profil utilisateur (frontend web) | @Raphael | 08/04/2026 | ⏳ A faire |
| Ajouter le bouton Google Sign-In sur le frontend web | @Raphael | 08/04/2026 | ⏳ A faire |

---

## Points en suspens

- Tristan toujours indisponible, pas de visibilite sur son retour
- Tests E2E / integration pas encore ecrits
- Twitter ingestion : API v2 payante, stub en place
- 2FA : flag en DB seulement, mecanisme TOTP pas implemente
- Refresh token rotation non implemente

---

## Objectifs prochaine reunion

1. Backend fiabilise avec tests supplementaires en place
2. Premieres sources de donnees operationnelles (feed avec vraies donnees)
3. Responsive web finalise
4. Maquette ou premier jet de la vue profil web
5. Google Auth fonctionnel sur le frontend web

---

## Prochaine reunion

**Date prevue** : 01/04/2026
**Sujets** : Demo sources de donnees, validation responsive final, avancement vue profil, Google Auth web

---

## Notes brutes

- Implementation frontend faite par Raphael, responsive complet bien avance sur la version web
- Implementation de routes backend
- Objectif pour la suite : fiabiliser le backend et creer les premieres sources de donnees pour degager un feed / algorithme, finir le responsive du front, implementation de la vue profil cote frontend web et implementation de la Google Auth
