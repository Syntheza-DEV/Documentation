# [Weekly] – Admin V2 : pilotage des crons & budget IA

**Date** : 03/06/2026
**Heure** : 15h - 16h
**Participants** : Raphael, Ilhan
**Animateur** : Ilhan
**Rédacteur** : Ilhan

---

## Contexte

Suite à une facture Google plus élevée que prévu (~16 €/mois), la priorité est
le pilotage de la consommation IA. Mise en place de la page Système admin et
correction du suivi de budget Gemini.

## Revue des objectifs

* ✅ Admin Panel V1 en prod : **OK**
* ✅ Page Système admin : **Livrée** (toggles crons + jauge budget IA)
* 🔄 Correctifs feed (résumé IA) : **OK**

## Ordre du jour

1. Page Système admin (crons + budget IA)
2. Correction du pricing / suivi budget Gemini
3. Bug feed (résumé IA non retourné)
4. Préparation jury blanc

## Discussions & Décisions

### Page Système admin
- **Discussion** : Nouvelle page `/admin/system` avec toggle ON/OFF par cron
  (`CronConfig` en DB) et jauge de consommation IA (`AiUsage` en DB, alerte à
  80 % du budget).
- **Décision** : Pilotage IA opérationnel depuis l'admin.
- **Responsable** : @Ilhan

### Pricing & suivi budget Gemini
- **Discussion** : Le pricing Gemini 2.5 était sous-estimé et le compteur en RAM
  se réinitialisait à chaque redémarrage (garde-fou inopérant).
- **Décision** : Pricing corrigé + persistance de l'usage en base de données.
- **Responsable** : @Ilhan

### Bug feed — résumé IA
- **Discussion** : Le feed ne retournait pas le résumé IA (`summary` codé en dur
  à `null`), et le contenu n'était pas nettoyé du HTML.
- **Décision** : Correctif appliqué (retour du résumé + strip HTML).
- **Responsable** : @Ilhan

### Préparation jury blanc
- **Discussion** : Le jury blanc approche, il faut préparer le support et le
  scénario de démo.
- **Décision** : Cadrer la préparation lors des prochaines réunions.
- **Responsable** : Équipe

## Actions à mener

| Action | Responsable | Échéance | Statut |
|--------|-------------|----------|--------|
| Surveiller la conso IA via la jauge admin | @Ilhan | Continu | 🔄 En cours |
| Préparer le support de présentation jury blanc | Équipe | 17/06/2026 | ⏳ À faire |
| Cadrer le scénario de démo (web + mobile) | @Raphael | 17/06/2026 | ⏳ À faire |

## Points en suspens

- Charte design mobile à réaligner sur le web (divergence post-rebuild v2)
- Migration jamais validée sur un gros volume réel

## Objectifs prochaine réunion

1. Préparation jury blanc avancée
2. Cadrage du sprint final (charte mobile iso)

## Prochaine réunion

**Date prévue** : 10/06/2026
**Sujets** : Prépa jury blanc, stabilisation prod, cadrage charte mobile

---

## 📝 Notes brutes

- facture google ~16€/mois -> on pilote la conso IA
- page /admin/system : toggle ON/OFF par cron + jauge budget IA (alerte 80%)
- pricing gemini 2.5 sous-estimé + compteur RAM resetté au restart -> persistance DB
- bug feed : summary hardcodé null + pas de strip HTML -> corrigé
- jury blanc approche, faut préparer le support
