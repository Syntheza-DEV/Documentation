---
title:          Beta Test Plan — Syntheza
subtitle:       Beta (MVP) — Frontend Web + Mobile
author:         Team Syntheza
module:         G-EIP-700
version:        1.0
document:       beta_test_plan.md
repository:     https://github.com/Syntheza-DEV
---

## 1. Contexte du projet
Syntheza est une plateforme SaaS de veille stratégique assistée par IA. Elle centralise la collecte d’informations depuis plusieurs sources (ex. Twitter, RSS), permet de consulter un flux unifié, d’explorer/rechercher du contenu, et d’interagir avec les informations (engagement, suivi, sauvegarde) depuis une interface web et une application mobile. L’objectif de la bêta (MVP) est de démontrer un parcours utilisateur complet, stable et exploitable en conditions réelles.

Scope bêta (MVP) : Frontend Web + Frontend Mobile, axé sur l’authentification, la consultation du feed, la recherche, la configuration des sources, et les interactions essentielles.

## 2. User roles
| Role name | Description |
|---|---|
| Utilisateur (Beta) | Professionnel utilisant Syntheza pour consulter un flux de veille, rechercher du contenu, configurer ses sources, et interagir (like, commentaire, bookmark, follow). |
| Équipe Syntheza (Support) | Utilisateurs internes qui testent, reproduisent des bugs, vérifient la cohérence UX et la stabilité (sans fonctionnalités “admin” dédiées dans la bêta). |

## 3. Feature table (organisée par user flow)
Toutes les fonctionnalités listées ci-dessous seront démontrées pendant la présentation beta.

| Feature ID | User role | Feature name | Short description |
|---|---|---|---|
| F1 | Utilisateur (Beta) | S’inscrire | Créer un compte et accéder à l’application (mobile). |
| F2 | Utilisateur (Beta) | Se connecter | Se connecter avec identifiants valides (mobile + web). |
| F3 | Utilisateur (Beta) | Réinitialiser un mot de passe | Déclencher “mot de passe oublié” et réinitialiser via écran dédié (mobile). |
| F4 | Utilisateur (Beta) | Accéder à des routes protégées | Redirection automatique si l’utilisateur n’est pas authentifié (web). |
| F5 | Utilisateur (Beta) | Rafraîchir une session automatiquement | Gestion expiration token + refresh pour éviter la déconnexion intempestive (web). |
| F6 | Utilisateur (Beta) | Se déconnecter | Logout complet avec nettoyage session (web). |
| F7 | Utilisateur (Beta) | Consulter le feed unifié | Afficher un flux principal de contenus (mobile). |
| F8 | Utilisateur (Beta) | Charger plus de contenus | Infinite scroll sur le feed (mobile). |
| F9 | Utilisateur (Beta) | Rechercher du contenu | Écran Discover/Search pour explorer (mobile). |
| F10 | Utilisateur (Beta) | Filtrer une recherche | Rechercher des posts/articles dans la recherche (mobile). |
| F11 | Utilisateur (Beta) | Configurer des sources | Accéder à une configuration des sources de veille (mobile). |
| F12 | Utilisateur (Beta) | S’abonner à des sources | Subscribe/unsubscribe à des sources pour personnaliser le feed (mobile). |
| F13 | Utilisateur (Beta) | Liker un contenu | Ajouter/retirer un like (mobile). |
| F14 | Utilisateur (Beta) | Commenter un contenu | Ajouter un commentaire sur un contenu (mobile). |
| F15 | Utilisateur (Beta) | Sauvegarder un contenu | Bookmark/unbookmark sur un post (mobile). |
| F16 | Utilisateur (Beta) | Suivre un utilisateur | Follow/unfollow d’autres utilisateurs (mobile). |
| F17 | Utilisateur (Beta) | Voir un Trust Factor | Afficher un indicateur “Trust Factor” sur le contenu (mobile). |
| F18 | Utilisateur (Beta) | Gérer son profil | Écran profil (mobile) : affichage infos principales. |
| F19 | Utilisateur (Beta) | Mettre à jour une photo de profil | Upload d’une photo de profil (mobile). |
| F20 | Utilisateur (Beta) | Changer son mot de passe | Modifier le mot de passe via API (mobile). |
| F21 | Utilisateur (Beta) | Accéder aux paramètres | Écran Settings (mobile) : accès et navigation cohérente. |
| F22 | Utilisateur (Beta) | Gérer les états applicatifs | Gestion globale loading/empty/error + erreurs réseau (web). |

## 4. Success criteria (à mesurer pendant la phase bêta)
| Feature ID | Key success criteria | Indicator/metric | Result achieved |
|---|---|---|---|
| F1 | Un utilisateur peut créer un compte sans erreur bloquante | 20 inscriptions, ≥ 95% de succès | TBD |
| F2 | Connexion réussie et session persistante | 30 connexions, ≥ 95% succès | TBD |
| F3 | Le reset mène à un changement de mot de passe effectif | 10 resets, 0 blocage, ≥ 90% succès | TBD |
| F4 | Les pages protégées redirigent correctement sans fuite | 20 accès non-auth, 20 redirections correctes | TBD |
| F5 | La session se renouvelle automatiquement sans action utilisateur | 30 minutes de navigation, 0 déconnexion non voulue | TBD |
| F6 | Déconnexion efface la session et empêche l’accès aux pages privées | 15 logouts, 0 accès résiduel | TBD |
| F7 | Le feed s’affiche et est utilisable dès l’arrivée | 20 ouvertures, 0 écran vide inattendu | TBD |
| F8 | Le scroll charge des contenus sans doublons ni blocage | 30 scrolls, 0 crash, < 2s pour charger | TBD |
| F9 | L’écran Search/Discover est accessible et interactif | 20 accès, 0 crash, navigation fluide | TBD |
| F10 | Une recherche renvoie des résultats cohérents | 20 requêtes, ≥ 90% résultats attendus | TBD |
| F11 | L’utilisateur peut accéder/modifier sa config de sources | 15 actions, 0 perte de config | TBD |
| F12 | Les abonnements impactent le feed (prise en compte) | 20 subscribe/unsubscribe, cohérence ≥ 95% | TBD |
| F13 | Le like se reflète immédiatement et persiste | 50 likes, 0 désynchro, persistance OK | TBD |
| F14 | Un commentaire est posté et visible | 30 commentaires, ≥ 95% succès | TBD |
| F15 | Un bookmark est ajouté et retrouvé | 30 bookmarks, 0 disparition après relance | TBD |
| F16 | Follow/unfollow se reflète sur le profil et la liste | 30 actions, cohérence ≥ 95% | TBD |
| F17 | Le Trust Factor s’affiche sans latence anormale | 30 affichages, < 1s, 0 valeur manquante | TBD |
| F18 | Le profil s’affiche avec les infos attendues | 20 ouvertures, 0 erreur bloquante | TBD |
| F19 | Photo uploadée et persistante après redémarrage | 10 uploads, ≥ 90% succès, persistance OK | TBD |
| F20 | Le mot de passe est modifié et permet une reconnexion | 10 changements, 10 reconnexions OK | TBD |
| F21 | Settings accessible et actions non bloquantes | 20 sessions, ≥ 90% tâches sans aide | TBD |
| F22 | Les états loading/empty/error sont cohérents (pas de “silence”) | 20 coupures réseau simulées, 0 freeze | TBD |

## 5. Limites assumées (bêta)
- Le scope se concentre sur un parcours utilisateur complet côté frontend (web + mobile) avec stabilité et UX cohérente.
- Les optimisations avancées (ex. extension navigateur, intégrations externes avancées) sont hors scope de cette bêta MVP.
