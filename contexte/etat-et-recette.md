# État du projet et recette

Consolidation : **9 octobre 2026**. Dernière recette Web retrouvée : **9 septembre 2026**. Aucun nouveau test applicatif, contrôle de production ou déploiement n'a été exécuté pour cette consolidation.

## Ce qu'un nouveau membre doit retenir

- Le Web a fait l'objet d'une recette en production le 9 septembre. Le rapport corrigé contient **18 cas marqués PASS : 17 vérifiés dans le navigateur utilisateur et le Digest validé par le propriétaire du produit**.
- Le propriétaire a confirmé avoir testé le Digest **trois fois le même jour**. Ce retour est une validation utilisateur, pas une exécution indépendante ajoutée après coup.
- Les derniers correctifs ont été observés en production, mais **les 18 parcours n'ont pas tous été rejoués ensemble après les derniers changements**. Ce n'est pas un certificat de non-régression au 9 octobre.
- L'application mobile sur appareil, le partage natif macOS de bout en bout et une nouvelle génération IA payante ne sont pas couverts par cette recette Web.
- Le code local, les anciens plans et les documents de recette correspondent à des moments différents. Pour le statut historique, utiliser la chronologie ci-dessous ; pour une modification, confronter le contexte à l'implémentation concernée.

## Pourquoi les anciens dossiers paraissent contradictoires

| Source et moment | Statut enregistré | Interprétation correcte |
| --- | --- | --- |
| Paquet ATP du 1er septembre | P0 bloqué ; poursuite acceptée par le propriétaire sans exécution indépendante complète | Préparation de campagne, pas résultat final de septembre |
| Recette locale `fix-test-cycle-1`, 9 septembre, première phase | 17 cas locaux validés ; F12/F13 sur données contrôlées ; F14 bloqué faute de clé IA locale | Preuve sur base et SMTP isolés, pas preuve de production |
| Premier rapport production du 9 septembre | 16 PASS, Digest bloqué, explication Trust en échec | État intermédiaire avant corrections et confirmation utilisateur |
| Rapport production corrigé du 9 septembre + fin de session | 18 PASS Web, avec les limites explicites de ce fichier | Dernier état de recette retrouvé ; ne pas conserver les blocages précédents comme état final |

Le CSV d'index de preuves du paquet préparatoire ne contient que ses en-têtes. Son absence de lignes ne prouve pas que la recette ultérieure n'a jamais eu lieu : celle-ci est consignée dans un autre rapport, dont la substance utile est rapatriée ici.

## Résultats Web conservés

Ces lignes résument le rapport `Syntheza_ATP_resultats_2026-09-09_corriges.xlsx`. Ce sont des **résultats historiques rapportés**, pas des scénarios rejoués pendant l'export.

### Accès et collecte

| Cas | Résultat au 9 septembre | Preuve / limite utile |
| --- | --- | --- |
| F1 — Créer un compte | PASS | Inscription réelle et session ouverte sur une page privée |
| F2 — Se connecter | PASS | Formulaire e-mail/mot de passe et accès à Accueil |
| F3 — Réinitialiser le mot de passe | PASS | Expéditeur Brevo vérifié, domaine authentifié, mail reçu dans Gmail, lien utilisé même avec session existante, reset puis connexion réussis ; pas de garantie de livraison à toutes les messageries |
| F4 — Protéger les pages privées | PASS | Redirection sans session, accès après authentification |
| F5 — Se déconnecter | PASS | Fermeture via menu et accès privé de nouveau refusé |
| F6 — Découvrir des éditeurs | PASS | Cartes d'éditeurs avec nom et domaine ; pas de page de détail séparée exigée par ce contrôle |
| F7 — Gérer ses abonnements | PASS | Abonnement/désabonnement et Feed reflétant les éditeurs suivis |
| F8 — Suivre la collecte | PASS | Écran Système et contenus déjà collectés inspectés ; tâches en pause au moment du contrôle, pas preuve d'une collecte planifiée active |
| F9 — Lire le Feed personnalisé | PASS | Article d'un éditeur suivi, source, auteur et date visibles |

### Lecture et interactions

| Cas | Résultat au 9 septembre | Preuve / limite utile |
| --- | --- | --- |
| F10 — Charger plus de contenus | PASS Web | Découvrir/Récents : 20 puis 40 cartes distinctes, aucun doublon, arrêt à la dernière page du jeu observé |
| F11 — Rechercher | PASS | Recherche par mot-clé puis ouverture d'un résultat |
| F12 — Lire une synthèse IA | PASS | Synthèse existante française et cohérente, sans Markdown brut ; ne prouve pas un nouvel appel fournisseur payant |
| F13 — Lire le Digest | PASS utilisateur | Digest essayé et confirmé par le propriétaire, trois fois le même jour ; pas de nouveau test indépendant ni de preuve du cron planifié |
| F14 — Comprendre le Trust Factor | PASS avec limite IA | Justification persistée puis relue après recharge sans relancer l'IA ; exemple neutre 50 faute de source de recoupement, pas une génération IA payante |
| F15 — Interagir | PASS | Like, commentaire valide et favori persistants ; interactions aussi dans Découvrir ; commentaire insultant rejeté |
| F16 — Profil et préférences | PASS | Bio persistante et avatar téléversé ; thème sombre également contrôlé pendant la recette locale |
| F17 — Notifications | PASS | Notification créée via le service réel sur compte contrôlé, lue, état et compteur persistants |
| F18 — Administration | PASS | Listes/fiches, changement de rôle puis restauration ; accès opérateur distinct du parcours lecteur |

## Correctifs de septembre à ne pas annuler par accident

### Feed et pagination

- Conserver les abonnements comme périmètre du Feed personnalisé ; le retrait temporaire de ce filtre était une mauvaise interprétation, pas une décision produit.
- Retirer la limite de 48 heures et le quota de cinq articles par éditeur sans retirer le classement par pertinence.
- Le scroll de l'Accueil a été observé jusqu'à 138 articles des abonnements. Ce nombre décrit le jeu du jour, pas une limite produit.
- Les nouvelles pages arrivent à la demande en bas de liste ; ne pas charger automatiquement tout le catalogue. Un contrôle ultérieur a observé 20 → chargement → 40, sans enchaînement de toutes les pages.
- Le bouton « S'abonner » de la colonne « À découvrir » doit appeler l'abonnement, conserver son état après recharge et rester une vraie action, pas un lien déguisé.

### Erreurs, sécurité et e-mails

- Connexion refusée : **« Adresse e-mail ou mot de passe incorrect. »**, pas un code `unknown` affiché à l'utilisateur.
- Réseau, session expirée, droits et formulaires : messages compréhensibles ; erreur inattendue : repli sans détails internes.
- Le reset doit être accessible même si une session existe déjà. SMTP accepté n'est pas synonyme de mail reçu : la recette finale a vérifié Gmail après correction de l'expéditeur Brevo.
- Les quotas de connexion, inscription et reset ont été séparés ; les connexions réussies ne doivent pas épuiser le quota des échecs. Ce contrôle n'est pas un test de charge.
- Modération lexicale : commentaires insultants rejetés à la création et à l'édition, brouillon conservé en cas d'échec. Cette protection n'est pas une modération exhaustive de toute forme d'abus.

### Trust Factor et rendu

- Archiver la justification et sa provenance avec l'évaluation ; la consultation après recharge ne doit pas déclencher une nouvelle génération.
- Un ancien score sans justification archivée est signalé comme tel : ne pas lui inventer rétrospectivement une explication.
- Indisponibilité du fournisseur : ne pas écraser un ancien score réel par une valeur de repli. Le cas historique contrôlé conservait un score de 75.
- Délai historique corrigé : serveur 45 secondes, clients 50 secondes ; test local d'une réponse simulée à 11 secondes. Cela ne prouve pas la disponibilité réelle du fournisseur.
- Thème sombre : logo blanc, modales opaques lisibles, infobulles/légendes et statuts contrastés ; tableau admin avec colonnes accessibles. Les survols des rôles ont été exercés, pas seulement leurs libellés statiques.

## Ce qui reste hors de cette preuve

| Sujet | Limite exacte |
| --- | --- |
| Mobile iOS/Android sur appareil | Compilation/tests locaux et existence de TestFlight ne remplacent pas une recette sur appareil ; aucune recette complète sur appareil retrouvée pour ce cycle |
| Partage natif macOS | Fenêtre ouverte puis annulée ; transmission effective non vérifiée |
| Nouvelle génération IA payante | Budget non augmenté pour la recette ; lecture de données existantes et résultat neutre sans corroboration ne valident pas un nouvel appel payant |
| Digest planifié | Validation utilisateur du Digest conservée ; aucun cron quotidien actif démontré par ce rapport |
| Couverture après les dernières corrections | Correctifs finaux vérifiés, mais pas de rejeu complet des 18 cas sur une unique dernière version figée |

Les comptes de recette temporaires ont été supprimés après les contrôles, avec vérification des données associées. Aucun identifiant ni lien de reset n'est repris dans ce dossier.

## Compteurs techniques : conserver leur date

La session se termine avec **59 tests Web** rapportés réussis. Le premier workbook local mentionne 54 tests Web et 88 tests mobile ; des étapes intermédiaires en mentionnent 55 à 58 côté Web. Ce sont des étapes successives, pas des compteurs simultanés.

Les états backend rapportés diffèrent selon la commande et l'environnement : fichiers de tests, tests ciblés, typage et suite complète ne sont pas interchangeables. Le workspace documente des échecs préexistants liés au chargement de Prisma sans `DATABASE_URL` ; le workbook local consigne aussi un échec `authMiddleware.test.ts` sur le préfixe Bearer. Aucun de ces échecs n'est déclaré corrigé par cette consolidation, et aucune suite n'a été relancée ici.

## Sources et provenance

- Paquet préparatoire : `ATP-readiness.md` et `README.txt` (1er septembre), index CSV vide ; contexte rapatrié ci-dessus.
- Workbook local : `Syntheza_ATP_fix-test-cycle-1.xlsx` (9 septembre).
- Workbook corrigé : `Syntheza_ATP_resultats_2026-09-09_corriges.xlsx`, onglets Résultats et Vérifications, relu pendant la consolidation ; le binaire original n'est pas nécessaire à la lecture de ce contexte.
- Session du 9 septembre : rapports finaux de corrections, clarification du périmètre du Feed, confirmation utilisateur du Digest et clarification finale des limites ATP.
- Exécutions CI historiques citées dans le workbook : [backend](https://github.com/Syntheza-DEV/backend/actions/runs/34368794020) et [web](https://github.com/Syntheza-DEV/frontend-web/actions/runs/34369289792). Ces liens identifient des exécutions de septembre, pas une vérification d'octobre.
- [Inventaire des sources et exclusions](./sources.md).
