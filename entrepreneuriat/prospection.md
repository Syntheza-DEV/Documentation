# Prospection et validation marché : plan au 15/10/2026

Ce document répond à l'objectif obligatoire 2 du compte rendu du 18/09 : premiers prospects, échanges, mails, démos, utilisateurs et engagements, chaque démarche étant documentée. La cible est celle du **pivot B2B d'octobre 2026** (business plan, slides 7 et 8) : **P1 trading et gestion d'actifs**, puis P2 quand P1 est prouvé.

La liste de comptes est dans [`prospects-cibles.xlsx`](./prospects-cibles.xlsx) :

| Onglet | Contenu |
|---|---|
| `Comptes_cibles` | **44 organisations**, dont 40 en P1 et 4 en P2, toutes avec une URL source vérifiée |
| `Communautes_evenements` | 22 communautés et événements |
| `Journal_modele` | modèle de journal vide |

Le dépôt est **public** : on n'y écrit que des organisations, des fonctions et des URL publiques. Aucun nom, e-mail ni téléphone d'une personne.

## 1. Profil de compte idéal (ICP)

| Critère | P1-A : desks de trading | P1-B : gestion d'actifs |
|---|---|---|
| Qui | Prop trading, market makers, hedge funds (dont quant/macro/event-driven) | Sociétés de gestion entrepreneuriales, multi-family offices, gestion de fortune et banques privées indépendantes |
| Où | France, Monaco, Suisse romande, Belgique | idem |
| Taille | 10 à 300 personnes : assez grand pour un budget data, assez petit pour qu'un associé décide seul | Encours de 1 à 30 Md€ ; 5 à 50 gérants et analystes |
| Décideur | Head of trading, CIO, associé | Directeur de la gestion, CIO, associé gérant |
| Utilisateur | Trader, analyste event-driven | Gérant, analyste, gérant privé |
| Signaux d'intérêt | Stratégies event-driven ou macro, publications de recherche, recrutement d'analystes, activité sur LinkedIn | Gestion de conviction (stock picking), plusieurs fonds actions ou crédit, clientèle UHNW qui pose des questions sur l'actualité |
| Exclusion | Grandes banques d'investissement (cycle d'achat long, terminaux imposés), particuliers, conseillers sans gestion discrétionnaire | idem ; acteurs 100 % gestion passive |

**Pourquoi commencer par des indépendants.** Dans une société de gestion entrepreneuriale ou un family office, la personne qui ressent le problème est souvent celle qui signe. Le cycle d'un pilote de 30 jours devient alors compatible avec un jury en février. Le business plan prévoit 2 à 4 mois de cycle de vente, pilote compris (slide 13). Dans une grande banque, il faut compter des mois de référencement fournisseur et de revue sécurité. Les grands acteurs de la liste (vague 2) ne servent qu'à mesurer l'écart.

## 2. Sources de comptes reproductibles

| Source | Usage | Lien |
|---|---|---|
| Liste des membres de l'AFG | Sociétés de gestion françaises | https://www.afg.asso.fr/fr/evenements/ (rubrique membres) |
| Liste des membres de l'AMAF | Banques et sociétés de gestion de Monaco | https://www.amaf.mc/en/membre-list |
| BEAMA | Gestionnaires d'actifs belges | https://www.beama.be/ |
| SUISSENÉGOCE | Négoce de matières premières en Suisse (P2) | https://suissenegoce.ch/ |
| Sites des sociétés | Taille, encours, stratégies, actualités : colonne « taille / encours » avec sa source | voir la colonne `source URL` |
| LinkedIn (recherche manuelle) | Trouver **la fonction** à viser dans chaque compte ; jamais de scraping automatisé ni d'extraction nominative en masse | - |

Le **scraping nominatif de LinkedIn** a été écarté dès le cadrage de juin ([produit-et-roadmap.md](../contexte/produit-et-roadmap.md)). La règle tient toujours.

## 3. Communautés et événements

Le détail figure dans l'onglet `Communautes_evenements`. Dates vérifiées sur les sites officiels le 15/10/2026 :

| Quand | Quoi | Pourquoi |
|---|---|---|
| 03/11/2026 | AGEFI AM Tech Day, Paris | Asset managers, fonctions technologie et données : profil acheteur |
| 05/11/2026 | AGEFI Forum Chief Compliance Officers | Valider l'angle traçabilité et MiFID II du journal de décisions |
| 26–27/11/2026 | Journées de l'AFTE, Paris La Défense | Trésoriers (P2) : entretiens de découverte, pas de vente |
| 03/12/2026 | FinTech Belgium, Digital Finance Summit, Bruxelles | Entrée en Belgique |
| permanent | Réseau Epitech Alumni, CFA Society France, SFAF, A.C.I. Monaco, AMAF, Monaco Economic Board | Introductions chaudes : le canal au meilleur taux de réponse attendu (**hypothèse**) |

Les conditions d'accès (invitation, tarif étudiant, bénévolat) sont à demander pour chaque événement. Aucune inscription n'a été faite.

## 4. Séquences d'approche

Les codes `seq-desks`, `seq-gerants`, `seq-fo` et `seq-communaute` sont repris dans la colonne « canal d'approche » de la liste et dans les liens UTM. Remplacer les crochets ; chaque message cite **un fait précis** du compte, tiré de la colonne « pourquoi eux ».

**Invitation LinkedIn (300 caractères au plus)**

```text
Bonjour, je suis l'un des trois fondateurs de Syntheza, un projet Epitech qui aide les [desks / gérants] à trier l'info de marché : synthèse, score de fiabilité expliqué, alertes. Votre [fait précis] m'a donné envie d'avoir votre regard de praticien. Puis-je vous écrire ?
```

**Message après acceptation**

```text
Merci d'avoir accepté. Je ne vous vends rien aujourd'hui : on cherche à comprendre comment [votre desk / votre équipe de gestion] traite une info qui tombe en séance (vraie ou rumeur, quel effet sur une position) et ce qui vous fait perdre du temps.

Auriez-vous 20 minutes dans les deux semaines pour un échange ? Si le sujet vous parle, on propose aussi un pilote gratuit de 30 jours sur vos propres actifs : https://syntheza.ovh/pilote?utm_source=linkedin&utm_medium=outbound&utm_campaign=seq-[desks|gerants|fo]

Syntheza informe et ne recommande jamais d'acheter ou de vendre.
```

**E-mail à froid** (adresse professionnelle publiée par la société, par exemple un contact générique ou une page équipe)

```text
Objet : 20 minutes sur la façon dont [société] trie l'info de marché

Bonjour,

Nous sommes trois étudiants-fondateurs (Epitech, promo 2027) et nous construisons Syntheza, un tableau de bord qui, pour chaque actif suivi, résume chaque info, la note par un Trust Factor expliqué (source, recoupements, horodatage) et garde une piste d'audit des décisions.

Nous cherchons à comprendre les pratiques réelles de quelques [desks / équipes de gestion] avant de figer la suite du produit. J'ai vu [fait précis]. Accepteriez-vous un échange de 20 minutes ?

Si l'outil vous intéresse, nous ouvrons 3 pilotes gratuits de 30 jours : https://syntheza.ovh/pilote?utm_source=email&utm_medium=outbound&utm_campaign=seq-[desks|gerants|fo]

[Prénom Nom], cofondateur, Syntheza - https://syntheza.ovh
Vous ne souhaitez plus recevoir de message de notre part ? Répondez « stop » : nous ne vous écrirons plus.
```

**Relance J+4**

```text
Je me permets une relance courte : 20 minutes suffisent, à l'heure qui vous arrange (avant l'ouverture ou après la clôture). Si ce n'est pas le bon interlocuteur chez [société], qui me conseilleriez-vous ?
```

**Relance J+10 (dernière)**

```text
Dernier message de ma part sur ce sujet. Si un jour une rumeur vous coûte du temps en séance, notre pilote de 30 jours reste ouvert. Merci pour votre attention.
```

**Message à une communauté** (A.C.I. Monaco, CFA Society France, AMAF, réseau alumni…)

```text
Bonjour, nous sommes trois fondateurs étudiants (Epitech) et nous cherchons 5 à 10 professionnels des marchés (trading, gestion) pour des entretiens de 20 minutes sur la manière dont ils traitent l'info de marché. Aucune vente : nous voulons valider (ou invalider) notre hypothèse. Pourriez-vous relayer cet appel auprès de vos membres ? Contact : [adresse] - https://syntheza.ovh/pilote?utm_source=email&utm_medium=outbound&utm_campaign=seq-communaute
```

Règles :
- au plus 3 messages par personne ;
- un « non » ou un « stop » est définitif et noté comme motif de perte ;
- jamais de promesse de latence, de client ou de chiffre que [`strategie-communication.md`](./strategie-communication.md) §7 interdit.

## 5. Guide d'entretien de découverte (30 min, méthode « Mom Test »)

Le principe : parler de **ce que la personne a fait**, pas de ce qu'elle ferait. On ne présente pas le produit avant la fin, et seulement si on nous le demande.

| Temps | Questions | Signal à noter |
|---|---|---|
| 0–3 min | Rôle, périmètre (actifs, horizon), taille de l'équipe | Fait partie de l'ICP ou non |
| 3–12 min | « Racontez-moi la dernière fois qu'une info importante est tombée sur un actif suivi. Comment l'avez-vous apprise, qu'avez-vous vérifié, combien de temps cela a-t-il pris ? » | Sources réellement utilisées, délai, étapes de vérification |
| 12–18 min | « La dernière fois qu'une rumeur ou une info fausse vous a coûté du temps ou de l'argent ? » | Fréquence et coût du problème (en temps ou en euros, s'il est donné) |
| 18–23 min | « Quels outils payez-vous aujourd'hui pour l'info ? Qui a décidé de l'achat, et comment ? » | Budget existant, décideur, processus d'achat |
| 23–27 min | « Devez-vous prouver ce que vous saviez au moment d'une décision (conformité, client, comité) ? Comment faites-vous ? » | Besoin de piste d'audit |
| 27–30 min | « Qui d'autre devrais-je interroger ? Accepteriez-vous un essai de 30 jours si l'outil répondait à ce que vous venez de décrire ? » | Introduction, engagement de pilote = **signal fort** |

**Grille de synthèse**, à remplir le jour même dans le journal :
1. problème confirmé (oui / non / partiel) et sa fréquence ;
2. coût cité ;
3. solution actuelle et son prix ;
4. décideur ;
5. engagement obtenu (aucun, introduction, démo, pilote) ;
6. phrase marquante, **notée mot pour mot pendant l'entretien et jamais reconstituée** ;
7. hypothèse du persona confirmée ou corrigée.

## 6. Script de démo (15 min)

Le tableau de démo est prévu dans la [feuille de route](./feuille-de-route-fonctionnalites.md), P0-3. Le scénario : *« une news tombe sur un actif que vous suivez »*.

| Min | Étape | Ce qu'on montre |
|---|---|---|
| 0–2 | Contexte | Rappeler en une phrase le problème entendu en entretien |
| 2–5 | Tableau de bord métier | Actifs du prospect (pris dans l'entretien), fil temps réel, séances, alertes |
| 5–9 | Une info arrive | Ligne d'info : impact et raisons, actifs touchés, Trust Factor et ses composantes, sources concordantes, horodatage publié / capté |
| 9–11 | Décider et tracer | Consigner une décision dans le journal (ajout seul, chaîne de hachage), export, dossier PDF |
| 11–13 | Équipe et intégration | Brief, partage d'un tableau de bord, Slack ou Teams, API (Excel, Power BI) |
| 13–15 | Proposition | Pilote de 30 jours, critères de succès, date de démarrage |

La démo est réussie si le prospect cite un actif ou un cas à tester, et si une date de démarrage du pilote est fixée.

## 7. Offre pilote de 30 jours

| Étape | Contenu | Outil (en production) |
|---|---|---|
| Demande | Formulaire `/pilote` ou suite d'un entretien | Page `/pilote`, pipeline de prospects |
| J0 | Onboarding de 30 min : métier, actifs, règles d'alerte, sources ; critères de succès écrits | Organisation pilote (offre Pro ou Équipe, statut Pilote) |
| J+7 | NPS et point de 15 min | Questionnaire NPS intégré |
| J+25 | NPS, rapport de pilote, proposition commerciale | Rapport de pilote PDF du back-office |
| J+30 | Décision : contrat annuel ou arrêt ; à l'échéance, le compte passe en lecture seule, rien n'est supprimé | Statuts Pilote, Actif, Expiré |

**Critères de succès**, proposés au prospect et fixés à J0 :
- utilisation au moins 3 jours par semaine ;
- au moins une alerte jugée utile par semaine ;
- délai entre la capture d'une info et sa première lecture mesuré par le rapport ;
- réponse à la question « décidez-vous plus vite sur une info plus fiable ? »

Message du business plan : « Testez 30 jours : si vos équipes ne décident pas plus vite sur une info plus fiable, on arrête. »

**Après le pilote** (hypothèses du business plan, HT) : Pro 185 €/siège/mois ; Équipe 140 €/siège dès 5 sièges ; Entreprise (API + SLA) sur devis. Contrat annuel. Pas de paiement en ligne : facturation hors plateforme.

## 8. Objections fréquentes

| Objection | Réponse factuelle |
|---|---|
| « On a déjà Bloomberg / LSEG » | Syntheza ne remplace pas un terminal : c'est une couche de tri, de fiabilité et de traçabilité. Le business plan situe un terminal autour de 2 000 $ par mois et par siège (chiffre à revérifier avant de le citer), Syntheza à 185 €. Le pilote sert à vérifier si cette couche fait gagner du temps. |
| « Une note de fiabilité, c'est un conseil en investissement » | Syntheza informe et ne recommande jamais : aucun ordre, aucun objectif de cours. Les titres formulés comme un conseil sont signalés. Un avis juridique sur la presse et l'AMF est prévu avant janvier 2027 (business plan, slide 17). |
| « Votre temps réel n'est pas prouvé » | C'est vrai, et on le dit. Médiane mesurée le 10/10 en régime normal : 4 min, p90 22 min, surtout à cause des éditeurs. L'objectif est < 60 s sur les sources officielles d'ici décembre. Le pilote mesure le délai réel sur vos actifs. |
| « Sécurité, confidentialité » | Double authentification, SSO (OIDC ou SAML) pour l'offre Entreprise, registre des accès conservé 400 jours, journal de décisions scellé. Hébergement chez OVH (pays du centre de données à confirmer avant de l'annoncer). Le SSO de bout en bout n'a pas encore été vérifié avec un vrai fournisseur. |
| « Vous êtes étudiants » | C'est un projet de fin d'études avec un produit en production, livré en continu. On demande un pilote gratuit, pas un engagement : le risque pour vous est nul. |

## 9. Conformité de la prospection

- **E-mail B2B** : autorisé sans consentement préalable si la personne est **informée**, si le message est **en rapport avec sa profession** et si elle peut **s'opposer** simplement à chaque envoi. Source : CNIL, « La prospection commerciale par courrier électronique », 04/02/2022, https://www.cnil.fr/fr/la-prospection-commerciale-par-courrier-electronique. D'où la ligne « stop » dans chaque e-mail.
- On n'utilise que des adresses professionnelles publiées par la société ou données par la personne.
- Pas de scraping de LinkedIn, pas d'outil d'extraction d'e-mails.
- Une opposition est enregistrée comme motif de perte, et la personne n'est plus jamais recontactée.

## 10. Preuves pour le jury

| Preuve | Où elle vit | Ce qu'on montre en février |
|---|---|---|
| Chaque échange (date, organisation, fonction, canal, résumé, prochaine action, motif de perte) | **Pipeline de prospects du back-office** (responsable, prochaine action, motif de perte), base privée | Export CSV mensuel, anonymisé pour la slide |
| Messages envoyés et réponses | Boîtes mail et LinkedIn des fondateurs | Captures horodatées, noms masqués |
| Entretiens | Grille de synthèse (§5) | Nombre d'entretiens, enseignements, changements de persona ou de produit qui en découlent |
| Démos et pilotes | Back-office : organisations pilotes, rapport PDF, NPS | Rapports de pilote, avec accord écrit du client ou anonymisés |
| Engagements | Lettre d'intention, bon de commande | Le document lui-même |

**Le journal nominatif réel n'entre jamais dans ce dépôt public.** L'onglet `Journal_modele` n'est qu'un modèle.

## 11. Objectifs du tunnel jusqu'au jury

Ce sont des **objectifs**, au prorata du tunnel du business plan (300 → 60 → 25 → 12 → 8 en septembre 2027). Ils sont identiques à ceux de [`kpi-suivi.xlsx`](./kpi-suivi.xlsx), onglet `Objectifs` :

| KPI | Fin nov. | Fin déc. | Fin janv. | Jury fév. |
|---|---|---|---|---|
| `comptes_cibles` | 60 | 90 | 110 | 120 |
| `contactes` | 45 | 80 | 100 | 115 |
| `reponses` | 9 | 16 | 20 | 23 |
| `entretiens_decouverte` (hypothèse) | 7 | 12 | 15 | 17 |
| `demos` | 4 | 7 | 8 | 10 |
| `demandes_pilote` | 2 | 3 | 4 | 5 |
| `pilotes_actifs` | 1 | 2 | 3 | 3 |
| `contrats` | 0 | 0 | 0 | 1 |

Point de départ au 15/10/2026 : 44 comptes identifiés. **Aucun contact n'est consigné** dans les sources consultées. Si l'équipe a déjà eu des échanges, il faut les saisir dans le pipeline avec leur date réelle.

## 12. Plan d'action : semaines 43 et 44

| Qui | Semaine 43 (19–23/10) | Semaine 44 (26–30/10) |
|---|---|---|
| **Ilhan** | Qualifier 15 comptes Monaco et Suisse (fonction à viser, fait précis) ; message aux communautés AMAF et A.C.I. Monaco ; 5 invitations (`seq-gerants`, `seq-fo`) | 5 invitations de plus ; relances J+4 ; caler 2 entretiens |
| **Tristan** | Qualifier 15 comptes desks et hedge funds (`seq-desks`) ; 5 invitations ; message à CFA Society France ; P0-1, P0-2 et P0-6 de la feuille de route (parcours de pilote, e-mails, origine des demandes) | 5 invitations ; relances ; caler 2 entretiens ; mener le premier entretien |
| **Raphaël** | Qualifier 14 comptes sociétés de gestion françaises et Belgique ; 5 invitations ; demande de mise en relation au service Epitech Alumni ; P0-3 (tableau de démo) | 5 invitations ; relances ; caler 1 entretien ; répéter la démo de 15 min |
| **Tous** | Saisir chaque contact le jour même dans le pipeline ; relevé KPI du lundi | Premier bilan : taux d'acceptation des invitations, première réponse ; ajuster les messages |

Fin de semaine 44, **objectif** : 44 comptes qualifiés, 30 contactés, au moins 3 entretiens calés.
