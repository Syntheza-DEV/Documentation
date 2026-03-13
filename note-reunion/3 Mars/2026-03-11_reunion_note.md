# [Weekly] – Backend MVP : Features restantes & Préparation intégration front

**Date** : 11/03/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Raphael
**Rédacteur** : Ilhan

---

## Contexte

Réunion de suivi centrée sur l'avancement du backend MVP. Tristan toujours indisponible. L'objectif est de finaliser les dernières features backend (préférences, Google Auth, routes sociales, feed) pour pouvoir passer à l'intégration côté frontend web et mobile.

---

## Revue des objectifs

* ✅ Confirmer déploiements frontend à jour : **Fait** (problème de cache résolu)
* ✅ Démo des 5 pages mobiles avec backend connecté : **Fait** (login/register/logout/settings/password)
* ✅ Logout web fonctionnel en prod : **Fait** (validé par Raphael)
* 🔄 Popup trust factor en prod : **Fonctionnelle** mais UX à améliorer selon retours

---

## Ordre du jour

1. Point sur les nouvelles routes backend ajoutées
2. Système de préférences utilisateur
3. Google Auth côté backend
4. Routes sociales (likes, commentaires, notifications)
5. Prochaines étapes pour finir le MVP backend

---

## Discussions & Décisions

### Nouvelles routes backend

* **Discussion** :
  Ilhan a ajouté plusieurs groupes de routes au backend depuis la dernière réunion : les préférences utilisateur (GET/PATCH), le système de likes (toggle + count), les commentaires CRUD, et les notifications (liste, mark read, delete). Au total une quinzaine de nouveaux endpoints.

* **Décision** :
  Les routes sont fonctionnelles et testées unitairement. Raphael va commencer à les intégrer côté frontend web petit à petit.

* **Responsable** : @Ilhan (backend), @Raphael (intégration frontend)

---

### Système de préférences utilisateur

* **Discussion** :
  Le backend gère maintenant les préférences utilisateur avec 18 champs regroupés en 4 catégories : apparence (thème, langue, taille texte), vie privée (visibilité profil, etc.), notifications (email, push, digest), et sécurité (2FA flag, alertes login). Le tout est validé avec Zod pour les enums et les types.

* **Décision** :
  Le mobile a déjà intégré les préférences via le Zustand store. Le web doit suivre, Raphael doit aligner les écrans Settings avec les endpoints.

* **Responsable** : @Raphael (frontend web)
* **Échéance** : 18/03/2026

---

### Google Auth backend

* **Discussion** :
  L'endpoint Google Auth (`POST /api/user/google`) est prêt côté backend. Il accepte un `idToken` Google, vérifie via la librairie `google-auth-library`, crée le compte ou le lie si l'email existe déjà. Le mobile l'utilise déjà. Côté web il manque le bouton Google dans l'interface.

* **Décision** :
  Raphael ajoute le bouton Google Sign-In sur la page login du web. L'endpoint backend ne change pas.

* **Responsable** : @Raphael
* **Échéance** : 18/03/2026

---

### Routes sociales (likes, commentaires, notifications)

* **Discussion** :
  Le backend expose maintenant le système social complet : like toggle (un seul endpoint pour like/unlike), commentaires CRUD avec ownership check, et notifications avec mark read / mark all read. Le feed retourne déjà les counts de likes et commentaires par item.

* **Décision** :
  L'intégration côté front est priorisée après les préférences et le Google Auth. Pour l'instant le feed web utilise encore des données mock, il faut le brancher sur les vrais endpoints.

* **Responsable** : @Raphael (web), @Ilhan (mobile quand le web sera stable)

---

### Finalisation MVP backend

* **Discussion** :
  On a fait le point sur ce qu'il reste côté backend : la majorité des features MVP sont implémentées (43 endpoints, 77 tests unitaires). Il reste à faire une passe de code review complète, écrire la doc technique, et potentiellement ajouter la clé Gemini en prod pour activer les résumés IA.

* **Décision** :
  Ilhan fait la code review + doc technique cette semaine. Objectif : le backend MVP est considéré comme terminé et on se concentre sur l'intégration front.

* **Responsable** : @Ilhan
* **Échéance** : 14/03/2026

---

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Code review complète du backend MVP | @Ilhan | 14/03/2026 | ⏳ À faire |
| Écrire la documentation technique backend | @Ilhan | 14/03/2026 | ⏳ À faire |
| Intégrer les préférences dans le frontend web | @Raphael | 18/03/2026 | ⏳ À faire |
| Ajouter le bouton Google Sign-In sur le web | @Raphael | 18/03/2026 | ⏳ À faire |
| Brancher le feed web sur les vrais endpoints backend | @Raphael | 25/03/2026 | ⏳ À faire |
| Ajouter GEMINI_API_KEY dans les secrets GitHub prod | @Ilhan | 14/03/2026 | ⏳ À faire |

---

## Points en suspens

- Tristan toujours indisponible, pas de visibilité sur son retour
- Tests E2E / intégration pas encore écrits
- Twitter ingestion : API v2 payante, stub en place pour l'instant
- 2FA : flag en DB seulement, pas de mécanisme TOTP implémenté

---

## Objectifs prochaine réunion

1. Backend MVP finalisé (code review + doc technique terminés)
2. Préférences intégrées côté frontend web
3. Bouton Google Auth visible sur le web
4. Plan d'intégration feed/likes/comments côté frontend

---

## Prochaine réunion

**Date prévue** : 18/03/2026
**Sujets** : Validation MVP backend, démo préférences web, Google Auth web, plan intégration social

---

## 📝 Notes brutes

- Ilhan a ajouté les routes préférences, likes, commentaires, notifications côté backend
- 18 champs de préférences avec validation Zod (thème, langue, digest, vie privée, etc.)
- Google Auth prêt côté backend, le mobile l'utilise déjà, manque le bouton sur le web
- Like toggle fait en un seul endpoint, retourne liked + count
- Commentaires avec ownership check, notifications avec mark read / mark all
- Feed retourne les counts likes et comments par item
- Raphael doit commencer à brancher le frontend web sur les vrais endpoints
- On a toujours pas de nouvelles de Tristan
- Faut ajouter la clé Gemini en prod pour que les résumés IA marchent
- Le feed web utilise encore des données mock, priorité pour le brancher
