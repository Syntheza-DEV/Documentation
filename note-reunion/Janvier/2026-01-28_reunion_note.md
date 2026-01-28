# [Weekly] – Avancement produit & technique

**Date** : 28/01/2026
**Heure** : 10:00 – 11:00
**Participants** : Raphael, Ilhan, Tristan
**Animateur** : Tristan
**Rédacteur** : Tristan

---

## Contexte

Réunion hebdomadaire de suivi du projet visant à faire un point sur l’avancement des travaux **frontend, mobile et backend**, à identifier les prochaines actions et à aligner les équipes sur les priorités techniques à court terme.

---

## Revue des objectifs

* ✅ Reprise et avancement du backend : **en cours**
* ✅ Démarrage du projet mobile : **réalisé**
* ⏳ Harmonisation UI web/mobile : **en cours**

---

## Ordre du jour

1. Avancement UI & backend web
2. Avancement mobile
3. État du backend et des issues en cours
4. Points techniques transverses (auth, multilangue)

---

## Discussions & Décisions

### UI & Backend Web

* **Discussion** :
  Raphael a modifié l’UI de la page *Settings* et avancé sur son implémentation côté backend. Une réflexion est en cours pour harmoniser l’interface entre le web et le mobile.
* **Décision** :
  Définir une **palette de couleurs commune web/mobile** pour assurer une cohérence visuelle globale.
* **Responsable** : @Raphael
* **Échéance** : Prochain weekly

---

### Mobile

* **Discussion** :
  Ilhan a initialisé le projet mobile et commencé une veille sur des templates de composants. Les différentes options identifiées ont été partagées dans le channel *veille* sur Discord.
* **Décision** :
  Continuer la phase d’exploration et préparer une base de composants réutilisables.
* **Responsable** : @Ilhan
* **Échéance** : Prochain weekly

---

### Backend & Authentification

* **Discussion** :
  Tristan a repris le backend et démarré la réalisation des tâches définies précédemment. Les tests sont planifiés les 29 et 30 janvier sur le **modèle de données initial** ainsi que sur l’authentification (inscription, connexion, JWT).
* **Décision** :
  Prioriser les issues **31, 32 et 34** après la phase de test.
* **Responsable** : @Tristan
* **Échéance** : Semaine en cours

---

### Multilangue

* **Discussion** :
  Le besoin de gestion multilingue a été abordé.
* **Décision** :
  Utiliser le package **i18n** comme solution standard de traduction.
* **Responsable** : Équipe
* **Échéance** : À intégrer progressivement

---

## Actions à mener

| Action                                      | Responsable | Échéance         | Statut      |
| ------------------------------------------- | ----------- | ---------------- | ----------- |
| Finaliser l’implémentation backend Settings | @Raphael    | Prochain weekly  | 🔄 En cours |
| Définir palette UI commune web/mobile       | @Raphael    | Prochain weekly  | ⏳ À faire   |
| Continuer veille & templates mobile         | @Ilhan      | Prochain weekly  | 🔄 En cours |
| Tester modèle de données + auth (JWT)       | @Tristan    | 29–30/01/2026    | ⏳ À faire   |
| Avancer sur les issues 31, 32, 34           | @Tristan    | Semaine en cours | ⏳ À faire   |
| Mettre en place i18n                        | Équipe      | À planifier      | ⏳ À faire   |

---

## Points en suspens

* Validation finale de la palette UI commune
* Choix définitif des composants mobiles

---

## Objectifs prochaine réunion

1. Backend auth fonctionnel et testé
2. Palette UI commune validée
3. Base solide du projet mobile définie

---

## Prochaine réunion

**Date prévue** : À confirmer (prochain weekly)
**Sujets** :

* Démo backend auth
* Validation UI web/mobile
* Avancement mobile

---

## Notes brutes (non modifiées)

* raph a changé l'ui de la page settings et l'implémentation du backend
* raph prévois de terminer son implementation backend et definir une palette de couleur ui commune pour web/mobile pour le prochain weekly
* ilhan a init le mobile et a commencé a regarder des emplate de composant en recensant les options dans le channel veille de notre discord.
* Tristan a pris en main le backend est a attaqué la realisation des taches etablies precedemment. Passant en test les 29 et 30 concernant le modele de données initial ainsi que auth: inscription/connexion + jwt
* Tristan prévois de taffer les issues 31 32 34.
* utiliser ca pour le multilangue : packet trad : i18n
