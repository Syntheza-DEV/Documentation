# [Weekly] – Reprise post-examens & cadrage refonte data model

**Date** : 13/05/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Raphael

---

## Contexte

Reprise pleine après les examens. La réunion valide la refonte du modèle de
données (`Source → Publisher / Channel / Item`) et décide d'enchaîner sur un
rebuild complet du frontend web, dont le scaffold initial (CRA) est à bout de
souffle.

## Revue des objectifs

* ✅ Période examens terminée : **Reprise effective**
* 🔄 Cadrage refacto data model : **À valider en séance**
* ⏳ Rebuild frontend web : **À planifier**

## Ordre du jour

1. Reprise post-examens
2. Validation du refacto Publisher/Channel/Item
3. Décision rebuild frontend web
4. Améliorations mobile (refresh token, résumé article)

## Discussions & Décisions

### Refonte Publisher/Channel/Item
- **Discussion** : Séparer l'éditeur (`Publisher`) de ses flux (`Channel`), et
  unifier les contenus dans un modèle `Item` doté d'un moteur de déduplication
  (canonicalisation d'URL + fingerprint SHA-256, contrainte d'unicité
  `(publisherId, fingerprint)`). Migration en mode additif + dual-read pour ne
  rien casser en prod.
- **Décision** : Refacto validé, mené en TDD. Migration one-shot prévue.
- **Responsable** : @Ilhan
- **Échéance** : 20/05/2026

### Rebuild frontend web
- **Discussion** : Le scaffold CRA initial est daté. Décision de reconstruire le
  web sur Vite + React 19 + TS strict, en réutilisant le design system et les
  services du mobile (parité visuelle).
- **Décision** : Lancer le rebuild v2 juste après le refacto data model.
- **Responsable** : @Ilhan (base technique), @Raphael (intégration / fixes)
- **Échéance** : 20/05/2026

### Améliorations mobile
- **Discussion** : Support du refresh token (header `X-Refresh-Token` / body) et
  logout UI sur échec de refresh ; mise en avant du résumé IA dans l'article.
- **Décision** : Intégré côté mobile et backend.
- **Responsable** : @Ilhan

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Implémenter le refacto Publisher/Channel/Item (TDD) | @Ilhan | 20/05/2026 | 🔄 En cours |
| Script de migration one-shot Sources→Publishers | @Ilhan | 20/05/2026 | ⏳ À faire |
| Démarrer le rebuild v2 du frontend web | @Ilhan | 20/05/2026 | ⏳ À faire |
| Adapter le mobile au nouveau modèle (publisher/channel) | @Ilhan | 20/05/2026 | ⏳ À faire |

## Points en suspens

- Migration à valider sur de vraies données
- Rebuild web : ampleur importante à anticiper

## Objectifs prochaine réunion

1. Refacto Publisher/Channel/Item livré (backend + mobile)
2. Rebuild v2 web démarré et avancé
3. Migration testée

## Prochaine réunion

**Date prévue** : 20/05/2026
**Sujets** : Démo refacto data model, avancement rebuild v2 web

---

## 📝 Notes brutes

- reprise post-exams, on repart à fond
- validé : refacto Source -> Publisher/Channel/Item + dedup (canonical url + fingerprint)
- migration additive + dual-read pour pas casser la prod
- on enchaîne sur un rebuild v2 du web (Vite + React 19 + TS strict, iso mobile)
- mobile : refresh token + logout sur échec, résumé IA dans l'article
