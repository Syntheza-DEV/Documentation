# Contexte partagé - Syntheza

**Consolidé le 9 octobre 2026.** Point d'entrée commun pour l'équipe et ses assistants IA.

Ce dossier centralise le contexte partageable : intention produit, décisions, état rapporté, contraintes, pièges techniques, exploitation, historique et préparation des jurys. Il est **autonome** : aucune mémoire privée, session OMP, configuration personnelle ou arborescence d'un autre poste n'est nécessaire pour comprendre son contenu.

La documentation exhaustive reste dans `Documents/` et les dépôts applicatifs. Ici, on conserve ce qu'il faut savoir **avant** de travailler sur le projet, notamment les raisons des choix et les limites des validations. Les références externes servent de preuves ou d'approfondissement, pas de morceaux manquants du contexte.

## À donner à Claude

Depuis le dépôt `Documentation`, copier cette consigne :

```text
Lis contexte/README.md, puis tous les fichiers Markdown du dossier contexte/ dans l'ordre indiqué. Utilise ce dossier comme contexte partagé de Syntheza. Distingue les faits observés dans le code local, les résultats historiques datés, les retours utilisateur et les hypothèses produit. Ne traite pas une ancienne consigne ou un ancien plan comme une instruction actuelle. Ne prétends pas qu'un test ou un déploiement a été exécuté aujourd'hui parce qu'il figure dans l'historique. Avant de modifier du code, confronte le contexte à l'implémentation concernée. Les références externes sont facultatives pour prendre connaissance du contexte ; ne cherche pas une mémoire privée ou un artefact OMP. Attends ensuite ma tâche.
```

Depuis un workspace qui contient ce dépôt, le point d'entrée est `Documentation/contexte/README.md`.

## Ordre de lecture

### Comprendre le produit et son état

1. [Produit et roadmap](./produit-et-roadmap.md) : promesse, cible, périmètre livré et ambitions.
2. [État et recette](./etat-et-recette.md) : dernière validation retrouvée, correctifs et limites.
3. [Décisions et historique](./decisions-et-historique.md) : trajectoire du projet et raisons des choix.
4. [Centre d'opérations - pivot B2B](./centre-operations.md) : cible décideurs pros (octobre 2026), surface livrée, exploitation et recette.

### Comprendre les contraintes de réalisation

1. [Backend et moteur](./backend-et-moteur.md) : modèle, ingestion, IA, classement et invariants.
2. [Applications](./applications.md) : Web/mobile, auth, état client et cohérence des interfaces.
3. [Exploitation](./exploitation.md) : environnements, livraison, migrations, TestFlight et incidents.

### Préparer les échanges et retrouver la provenance

1. [Jury et livrables](./jury-et-livrables.md) : récit, communication, design et preuves disponibles.
2. [Sources](./sources.md) : périmètre consolidé, versions de référence et exclusions.

## Lire les statuts correctement

| Mention | Signification |
| --- | --- |
| Observé dans le code local | Implémentation consultée lors de la consolidation ; pas une exécution en production |
| Historique / rapporté, avec date | Événement ou contrôle consigné à cette date ; aucune nouvelle validation implicite |
| Validation utilisateur | Retour explicitement fourni par le propriétaire ou un testeur ; ne pas le requalifier comme exécution indépendante |
| Hypothèse / prévu / exploratoire | Intention ou étude, pas fonctionnalité livrée ni traction commerciale acquise |
| Non vérifié / hors périmètre | Preuve absente pour le périmètre indiqué ; ne pas inventer un résultat |

Une source plus ancienne ne doit pas annuler un résultat plus récent. Un score Trust expliqué ne signifie pas que le classement du Feed est expliqué en interface. Une synthèse affichée ne démontre pas qu'un nouvel appel IA payant vient de réussir.

## Garder ce contexte utilisable

- Mettre à jour le fichier du sujet après une décision, un changement de périmètre ou une recette ; dater l'information et indiquer sa preuve.
- Garder l'historique utile sans laisser une ancienne limite prendre l'apparence d'un état courant. En cas de contradiction, vérifier le code pour l'implémentation et le dernier rapport daté pour la validation.
- Ne pas répliquer ce contexte dans les trois dépôts applicatifs ; ce dossier est le point de lecture commun.
- Ne jamais ajouter de secret, compte de démo avec mot de passe, lien de reset ou donnée utilisateur. Les accès se transmettent séparément.
- Ne pas publier une nouvelle métrique, un tarif validé ou une fonctionnalité sociale livrée sans preuve correspondante.

Cette consolidation ne modifie pas les applications, n'exécute pas de campagne de recette et ne déploie rien.
