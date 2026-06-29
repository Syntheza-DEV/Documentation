# [Weekly] – Sprint final livré : charte mobile iso, Admin V3 & clôture de phase

**Date** : 24/06/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Réunion de clôture de la phase de développement pré-jury. Le sprint final est
livré : charte mobile alignée sur le web, Admin Panel V3 (crons avancé), et
finitions prod déployées. Le développement a été porté par Ilhan, Raphael en
appui sur la préparation de la présentation.

## Revue des objectifs

* ✅ Charte mobile iso web : **Livrée et mergée sur main**
* ✅ Admin Panel V3 (crons avancé) : **Livré**
* ✅ Finitions prod (avatar CORP, CI force-recreate) : **Déployées**

## Ordre du jour

1. Bilan charte mobile iso web
2. Admin Panel V3 (crons avancé + test notification)
3. Finitions prod déployées
4. Clôture de la phase de développement pré-jury

## Discussions & Décisions

### Charte mobile iso web
- **Discussion** : Alignement complet livré et mergé sur `main` : tokens, ~22
  composants et ~19 écrans alignés sur la charte web, gradient sur le prénom
  d'accueil, `ScreenHeader` unifié, padding container à 16, clôture de
  l'adaptation publisher/channel. 82 tests verts, compilation TS OK, validé sur
  simulateur.
- **Décision** : Charte mobile iso actée. Déploiement device (EAS) manuel si
  besoin (pas de CI mobile).
- **Responsable** : @Ilhan

### Admin Panel V3
- **Discussion** : Administration avancée des crons — lancement manuel d'un job
  (même en pause), historique d'exécutions (`CronRun`), édition de fréquence à
  chaud (`CronConfig.schedule`), métriques par job, et envoi d'une notification
  de test à tous les admins.
- **Décision** : Admin V3 livré et déployé.
- **Responsable** : @Ilhan

### Finitions prod
- **Discussion** : Avatar corrigé (URLs absolues côté front + header CORP
  `cross-origin` sur `/uploads` côté backend). Bug CI corrigé (`--force-recreate`
  sur l'API : le conteneur n'était jamais recréé sur tag `:latest` inchangé).
  Bouton retour ajouté sur le détail article web.
- **Décision** : Correctifs déployés en prod.
- **Responsable** : @Ilhan

### Clôture de phase
- **Discussion** : La phase de développement pré-jury est terminée. Tristan
  n'est pas revenu sur le projet ; le développement a été assuré par Ilhan,
  Raphael en appui sur la préparation de la présentation.
- **Décision** : On bascule sur la préparation finale du jury.
- **Responsable** : Équipe

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Enregistrer le backup vidéo de la démo (web + mobile) | Équipe | Veille du jury | ⏳ À faire |
| Finaliser le support de présentation | Équipe | Veille du jury | 🔄 En cours |
| Déploiement device mobile (EAS) si nécessaire | @Ilhan | Avant démo | ⏳ À faire |

## Points en suspens

- Migration Publisher/Channel jamais validée sur un gros volume de vraie donnée
- Twitter : API v2 payante, stub en place
- 2FA : flag en DB seulement, TOTP non implémenté
- Refresh token rotation non implémenté
- Bouton Google Auth web (endpoint prêt, UI à finaliser)

## Objectifs prochaine réunion

1. Préparation finale du jury (support + démo + backup vidéo)
2. Répétition du déroulé de présentation

## Prochaine réunion

**Date prévue** : À définir (semaine du jury)
**Sujets** : Répétition générale, checklist pré-jury

---

## 📝 Notes brutes

- charte mobile iso web mergée sur main : tokens + ~22 composants + ~19 écrans
- gradient prénom home + ScreenHeader unifié + padding 16, 82 tests verts
- admin v3 : lancer une cron à la main, historique (CronRun), édition fréquence à chaud, notif test
- fixes prod : avatar (url absolue + CORP cross-origin) + CI force-recreate api + back button article web
- fin de la phase dev pré-jury, tristan pas revenu, dev tenu par ilhan, raph en appui prépa
- penser au backup vidéo de la démo la veille
