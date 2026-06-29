# [Weekly] – Cadrage du sprint final : charte mobile iso & finitions prod

**Date** : 17/06/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Réunion de cadrage du sprint final avant le jury. L'objectif principal : aligner
complètement la charte design du mobile sur le web, clôturer l'adaptation
publisher/channel mobile, et corriger les dernières finitions prod identifiées.

## Revue des objectifs

* ✅ Préparation jury blanc : **Avancée** (support + scénario démo)
* 🔄 Charte mobile iso web : **Cadrée** (à exécuter)
* ⏳ Finitions prod (avatar, CI) : **Identifiées**

## Ordre du jour

1. Cadrage du chantier charte mobile iso web
2. Clôture de l'adaptation publisher/channel mobile
3. Finitions prod : avatar + CI déploiement
4. Préparation finale du jury

## Discussions & Décisions

### Charte mobile iso web
- **Discussion** : Aligner le design system mobile sur la charte web : tokens
  (primary, neutres, échelle de radius), wordmark, harmonisation de ~22
  composants et ~19 écrans, gradient sur le prénom de l'écran d'accueil (parité
  web), `ScreenHeader` unifié (flèche retour + titre), padding container à 16.
- **Décision** : Sprint exécuté d'un bloc, avec tests verts et compilation TS.
- **Responsable** : @Ilhan
- **Échéance** : 24/06/2026

### Finitions prod
- **Discussion** : Deux points identifiés — l'avatar (URLs API relativisées côté
  front + header CORP `same-origin` bloquant l'image cross-origin sur
  `/uploads`), et un bug CI de déploiement (le conteneur n'est jamais recréé
  quand le tag `:latest` est inchangé).
- **Décision** : Corriger l'avatar (URL absolue + CORP `cross-origin`) et le CI
  (`--force-recreate` sur l'API).
- **Responsable** : @Ilhan

### Préparation finale jury
- **Discussion** : Support de présentation quasi prêt, scénario de démo défini.
- **Décision** : Finaliser et prévoir un backup vidéo de la démo.
- **Responsable** : Équipe

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Exécuter l'alignement charte mobile iso web | @Ilhan | 24/06/2026 | 🔄 En cours |
| Clôturer l'adaptation publisher/channel mobile | @Ilhan | 24/06/2026 | ⏳ À faire |
| Corriger avatar (URL absolue + CORP cross-origin) | @Ilhan | 24/06/2026 | ⏳ À faire |
| Corriger le CI déploiement (force-recreate api) | @Ilhan | 24/06/2026 | ⏳ À faire |
| Finaliser support + backup vidéo démo | Équipe | 24/06/2026 | 🔄 En cours |

## Points en suspens

- Backup vidéo de la démo à enregistrer la veille du jury
- Migration jamais validée sur un gros volume réel

## Objectifs prochaine réunion

1. Charte mobile iso web livrée et mergée
2. Finitions prod déployées (avatar, CI)
3. Clôture de la phase de développement pré-jury

## Prochaine réunion

**Date prévue** : 24/06/2026
**Sujets** : Bilan sprint final, clôture phase pré-jury

---

## 📝 Notes brutes

- sprint final : aligner toute la charte mobile sur le web (tokens, ~22 composants, ~19 écrans)
- gradient prénom home + ScreenHeader unifié + padding container 16
- clôturer l'adaptation publisher/channel mobile
- fixes prod : avatar (url absolue + CORP cross-origin /uploads) + CI (force-recreate api)
- finaliser support jury + backup vidéo démo
