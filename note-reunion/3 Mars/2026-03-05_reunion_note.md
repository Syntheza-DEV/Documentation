## Contexte

Réunion hebdomadaire centrée sur la résolution de problèmes de déploiement, l’avancement du frontend mobile et les améliorations côté frontend web et trust factor.

## Revue des objectifs

- ✅ Corriger les problèmes de déploiement identifiés : Fait (corrigé par @Ilhan)
- ✅ Connecter le mobile au backend pour l’authentification : Fait (Login / Register / Logout / Changement de mot de passe)
- 🔄 Améliorer le comportement du logout côté frontend web : En cours (implémentation refaite)
- 🔄 Finaliser l’intégration du trust factor (UX + déploiement) : En cours (popup OK, problème de déploiement)

## Ordre du jour

1. Problème de déploiement et correctif
2. Avancement frontend mobile (5 pages + connexion backend)
3. Comportement du logout côté frontend web
4. Évolution du trust factor (popup explicative)
5. Problèmes de déploiement côté Raphael

## Discussions & Décisions

### Problème de déploiement
- **Discussion** : Un problème au niveau du déploiement a été identifié et remonté.  
  Ilhan a pris en charge l’investigation et le correctif.
- **Décision** : Le problème de déploiement est corrigé et la procédure de déploiement reste inchangée pour l’instant.
- **Responsable** : @Ilhan

### Avancement frontend mobile
- **Discussion** :  
  Ilhan a bien avancé le frontend mobile avec 5 pages, et la connexion backend est faite pour :
  - Login
  - Register
  - Logout
  - Changement de mot de passe
- **Décision** : Considérer la base mobile + auth comme fonctionnelle. Prochaine étape : stabilisation et tests utilisateurs.
- **Responsable** : @Ilhan

### Logout frontend web
- **Discussion** :  
  Le logout côté frontend web a été refait pour s’assurer que le nettoyage de la session se fasse correctement.
- **Décision** : Nouveau comportement adopté. À surveiller en prod pour confirmer qu’il n’y a plus de résidus de session.
- **Responsable** : @Raphael

### Trust factor – Popup explicative
- **Discussion** :  
  Le trust factor affiche maintenant une page/popup qui explique en quoi la source est “trusted” pour l’utilisateur.
- **Décision** : Valider cette première version pour amélioration de la pédagogie utilisateur. Reste à fiabiliser le déploiement.
- **Responsable** : @Raphael

### Problème de déploiement côté Raphael
- **Discussion** :  
  Raphael rencontre un problème côté déploiement : les mises à jour ne se propagent pas (le trust factor ne se met pas à jour en prod).
- **Décision** :  
  - Analyser la pipeline / le cache côté hébergeur.  
  - Vérifier que la bonne branche / build est déployée.  
  - Mettre en place un check systématique après déploiement.
- **Responsable** : @Raphael, avec support de @Ilhan si besoin

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Vérifier et stabiliser le flux de déploiement pour les changements frontend | @Raphael, @Ilhan | Prochaine semaine | 🔄 En cours |
| Tester les 5 pages mobiles avec les flows Login / Register / Logout / Changement de mot de passe | @Ilhan | Prochaine semaine | ⏳ À faire |
| Surveiller le nouveau comportement du logout web en prod | @Raphael | Prochaine semaine | ⏳ À faire |
| Vérifier que la popup trust factor est bien déployée et visible en prod | @Raphael | Prochaine semaine | ⏳ À faire |

## Points en suspens

- Fiabilité complète du déploiement côté frontend (cache, build, pipeline)
- Validation UX de la popup trust factor auprès des futurs testeurs

## Objectifs prochaine réunion

1. Confirmer que les déploiements frontend se mettent bien à jour (plus de décalage entre code et prod)
2. Démo complète des 5 pages mobiles avec backend connecté
3. Vérifier le bon fonctionnement du logout web en conditions réelles
4. Confirmer la présence et le comportement de la popup trust factor en prod

## Prochaine réunion

**Date prévue** : À définir  
**Sujets** : Suivi des problèmes de déploiement, validation flows mobile, validation trust factor

## 📝 Notes brutes

- Problème au niveau du déploiement, corrigé par Ilhan  
- Ilhan a bien avancé le frontend mobile avec 5 pages + connexion backend faite pour login / register / logout / changement de mot de passe  
- Logout côté frontend web refait pour bien nettoyer la session  
- Trust factor affiche maintenant une page/popup qui explique en quoi la source est “trusted”  
- Problème pour Raphael en déploiement : les mises à jour ne se mettent pas à jour en prod (trust factor)

