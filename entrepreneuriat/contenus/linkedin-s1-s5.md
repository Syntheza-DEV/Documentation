# Contenus S1 à S5 : LinkedIn et Instagram

Créneaux des semaines 1 à 5 du calendrier éditorial (du 20/10/2026 au 30/10/2026). Tout est prêt à publier **après relecture humaine** : les visuels sont générés avec le CLI `syntheza-skills/insta/bin/insta-editorial.js`, qui ne publie jamais.

## Vue d'ensemble

| Créneau | Date et heure | Canal et compte | Pilier | Sujet |
|---|---|---|---|---|
| S1 | mar. 20/10/2026, 11:00 | LinkedIn, profil fondateur | `BUILD` | Pourquoi on pivote |
| S2 | jeu. 22/10/2026, 08:30 (LinkedIn) et 12:30 (Instagram) | LinkedIn page + Instagram | `DECODE` | La BCE relève ses taux |
| S3 | mar. 27/10/2026, 08:30 (LinkedIn) et 13:00 (Instagram) | LinkedIn page + Instagram | `FIABLE` | Faux tweet AP de 2013 |
| S4 | jeu. 29/10/2026, 08:30 | LinkedIn, profil fondateur | `PILOTE` | 3 desks pour un pilote de 30 jours |
| S5 | ven. 30/10/2026, 12:30 | Instagram | `BUILD` | Coulisses de l'équipe |

**Heures** : elles suivent le [calendrier éditorial](../calendrier-editorial.xlsx), qui fait foi ; leur justification est dans la [stratégie de communication](../strategie-communication.md). Ce sont des hypothèses, à remplacer par les meilleurs créneaux observés une fois les KPI du compte mesurés.

**Profil fondateur** : le calendrier attribue S1 et S4 à Ilhan ; l'équipe peut changer ce choix. **Personas** : P1 trading (traders, prop traders, hedge funds, quants) et gestion d'actifs (gérants, analystes, family offices, banque privée), puis P2 finance d'entreprise, énergie et matières premières, risque et conformité.

**Liens** : sur LinkedIn, le lien est à placer **en premier commentaire** (hypothèse de portée à tester) ; sur Instagram, il est en bio.

**Visuels** : 1080×1350 (portrait 4:5, format par défaut du dépôt). Le CLI limite un carrousel à 5 slides carrées ; pour tenir 5 à 7 slides en 4:5, chaque slide est un bundle CLI distinct (validation des sources par slide). **Le CLI ne sait pas produire de PDF** : le PDF LinkedIn de chaque carrousel est assemblé à partir des PNG (Pillow) et n'est pas une sortie du générateur. Chaque visuel est à relever par un humain avant publication.

---

## S1 : LinkedIn, profil fondateur, `BUILD`

| Champ | Valeur |
|---|---|
| Canal / compte | LinkedIn, profil d'un fondateur |
| Date / heure | mardi 20/10/2026, 11:00 (hypothèse) |
| Pilier | `BUILD` « Pourquoi on pivote : du lecteur de veille au dashboard des décideurs » |
| Objectif | Poser le positionnement B2B, gagner de la crédibilité et des premières conversations (objectif, sans valeur chiffrée) |
| Persona | P1 trading et gestion d'actifs, puis P2 |
| Format | Texte seul (1454 caractères, hashtags compris) |
| Lien UTM | Aucun (CTA discret : message privé) |

### Texte final

```text
On change de cap, et on préfère le dire clairement.

Jusqu'ici, Syntheza se présentait comme un lecteur de veille grand public : « Moins de flux. Plus de recul. » En octobre, on a tranché : le produit s'adresse désormais aux décideurs professionnels, ceux qui engagent de l'argent sur une information. Traders, gérants, analystes, trésoriers, équipes risque et conformité.

Pourquoi ? Parce que l'information coûte cher à celui qui doit décider vite, et que « vite » ne vaut rien si l'info est fausse ou reprise en boucle.

Ce qu'on a appris en reconstruisant le produit :
→ Une info ne se juge pas à son titre mais à sa source, à ses recoupements et à son horodatage.
→ Un score de fiabilité doit s'expliquer. Sinon, c'est un chiffre de plus à croire sur parole.
→ Un exemple concret : en production, 199 infos sur 200 tombaient entre 40 et 69 sur notre ancien barème, parce qu'une absence de preuve était notée comme une mauvaise note. On a recalibré le 11 octobre.

Ce qu'on a aujourd'hui : un dashboard en production, une synthèse de chaque info, un Trust Factor expliqué, des alertes, un journal de décisions.
Ce qu'on n'a pas encore : la preuve par des équipes qui l'utilisent chaque jour. C'est ce qu'on va chercher dans les prochaines semaines.

Syntheza informe, ne recommande jamais d'acheter ou de vendre.

Si vous travaillez sur un desk ou en gestion et que ce sujet vous parle, mon DM est ouvert.

#Syntheza #Veille #Trading #AssetManagement
```

### Sources
- Cible, pivot et offre : documentation interne Syntheza (`Documentation/contexte/centre-operations.md`, mis à jour le 10/10/2026 ; business plan d'octobre 2026).
- Recalibrage du Trust Factor (199 infos sur 200 entre 40 et 69 ; recalibrage du 11/10/2026) : même document, section Trust Factor V2. Source interne : pas de lien public.

### Checklist de relecture
- [ ] AMF : aucune formulation d'achat, de vente ou de conservation ; « information, pas un conseil » présent.
- [ ] Chiffres : chacun relié à une source citée ci-dessus (lien + date) ; aucun chiffre de latence, de client ou d'utilisateur.
- [ ] Charte : noir/blanc/gris, Space Grotesk, aucune couleur sémantique (aucun état réel affiché).
- [ ] Relecture humaine des visuels avant publication (le générateur ne publie jamais).
- [ ] Le chiffre « 199 sur 200 » vient du constat de production du 10-11/10/2026 : le vérifier avant publication.

---

## S2 : LinkedIn page + Instagram, `DECODE`

| Champ | Valeur |
|---|---|
| Canal / compte | LinkedIn, page entreprise Syntheza (document, page à créer avant le 22/10) + Instagram `@syntheza.eip` (carrousel) |
| Date / heure | jeudi 22/10/2026, 08:30 (LinkedIn) et 12:30 (Instagram), hypothèses |
| Pilier | `DECODE` « Une news, 60 secondes : comment Syntheza la lit » |
| Objectif | Montrer le produit sur un cas réel et faire comprendre le Trust Factor (objectif, sans valeur chiffrée) |
| Persona | P1 trading et gestion d'actifs ; P2 trésoriers et DAF (taux) |
| Lien UTM LinkedIn | https://syntheza.ovh/pilote?utm_source=linkedin&utm_medium=social&utm_campaign=decode |
| Lien UTM Instagram | https://syntheza.ovh/pilote?utm_source=instagram&utm_medium=social&utm_campaign=decode |

### News utilisée
Décisions de politique monétaire de la BCE, communiqué du **10/09/2026** : hausse de 25 points de base des trois taux directeurs (2,50 %, 2,65 %, 2,90 % à compter du 16/09/2026), projections d'inflation et de croissance. Choisie parce qu'elle est officielle, vérifiable et suivie par les trésoriers comme par les gérants. Elle date de plus d'un mois : à remplacer par la décision suivante de la BCE si elle tombe avant le 22/10 (calendrier BCE à vérifier).

### Texte LinkedIn (page), avec le PDF en pièce jointe

```text
Une news, une minute : la BCE relève ses taux.

Le 10/09/2026, le Conseil des gouverneurs de la BCE a décidé d'augmenter de 25 points de base ses trois taux directeurs : dépôt 2,50 %, refinancement 2,65 %, prêt marginal 2,90 %, à compter du 16/09/2026.

Voici comment Syntheza lit cette info (carrousel en document) :
1. La news brute : le communiqué officiel, daté.
2. La synthèse : la décision, les nouveaux taux, le motif cité.
3. L'impact, décrit sans avis : la BCE projette une inflation de 3,0 % en 2026 et 2,5 % en 2027, une croissance de 0,9 % et 1,4 %, avec des risques à la hausse pour l'inflation et à la baisse pour la croissance.
4. Le Trust Factor : qui parle (ici, la source officielle), qui recoupe (reprises par d'autres institutions), et pourquoi le score est ce qu'il est.

Transparence : la synthèse du carrousel est rédigée à la main pour illustrer le format. Ce n'est pas une capture de l'application.

Information, pas un conseil en investissement.
Sources : BCE, communiqué du 10/09/2026 ; Banque de France, Bulletin économique de la BCE n°6/2026 (24/09/2026).

Question pour les équipes de desk : sur une décision de banque centrale, qu'est-ce qui vous manque le plus dans les premières minutes ?

#Syntheza #BCE #Macro #Trading
```

### Légende Instagram

```text
Une news, une minute. Le 10/09/2026, la BCE a relevé de 25 points de base ses trois taux directeurs (dépôt 2,50 %, refinancement 2,65 %, prêt marginal 2,90 %). Dans Syntheza, chaque info est résumée, rattachée aux actifs concernés et notée par un Trust Factor expliqué. Ici : source officielle, reprise par d'autres institutions. La synthèse du carrousel est rédigée à la main pour illustrer le format. Information, pas un conseil en investissement. Source : BCE, communiqué du 10/09/2026. Pilote gratuit de 30 jours : lien en bio.
#syntheza #bce #tauxdinteret #macro #finance
```

### Visuels (6 slides, 1080×1350)
| # | Fichier | Texte du visuel |
|---|---|---|
| 1 | `s2-decode/s2-slide-01.png` | Titre : « La BCE relève ses taux. » (10 septembre 2026) |
| 2 | `s2-decode/s2-slide-02.png` | La news brute : citation du communiqué BCE |
| 3 | `s2-decode/s2-slide-03.png` | La synthèse : décision, nouveaux taux, motif cité (mention « rédigée à la main ») |
| 4 | `s2-decode/s2-slide-04.png` | L'impact, sans avis : projections BCE inflation et croissance, risques cités |
| 5 | `s2-decode/s2-slide-05.png` | Le Trust Factor : qui parle, qui recoupe, pourquoi ce score (mécanisme, sans valeur) |
| 6 | `s2-decode/s2-slide-06.png` | Signature « Décider vite. Décider juste. » et appel au pilote |

PDF LinkedIn : `s2-decode/s2-document-linkedin.pdf`. Alt texts : dans les bundles du CLI (`alt-text.json`), repris des descriptions du tableau.

### Sources
- BCE, Décisions de politique monétaire, 10/09/2026 : https://www.ecb.europa.eu/press/pr/date/2026/html/ecb.mp260910~314e508016.fr.html
- Banque de France, Bulletin économique de la BCE n°6/2026, 24/09/2026 : https://www.banque-france.fr/fr/publications-et-statistiques/publications/bulletin-economique-de-la-bce-ndeg62026
- Mécanisme du Trust Factor : `Documentation/contexte/backend-et-moteur.md` et `centre-operations.md` (palier de source, corroboration indépendante, confirmation officielle). **Aucune valeur de Trust n'est affichée** : le produit ne la fournit pas pour cette news dans ce dossier.

### Points d'attention
- La synthèse du slide 3 est **rédigée à la main** et le dit sur le visuel : le budget IA de production est à 0 depuis le 10/10/2026, donc ne pas la présenter comme une sortie de l'IA.
- Le « 60 secondes » du pilier se lit « une minute » sur le visuel : ne pas en faire une promesse de latence.

### Checklist de relecture
- [ ] AMF : aucune formulation d'achat, de vente ou de conservation ; « information, pas un conseil » présent.
- [ ] Chiffres : chacun relié à une source citée ci-dessus (lien + date) ; aucun chiffre de latence, de client ou d'utilisateur.
- [ ] Charte : noir/blanc/gris, Space Grotesk, aucune couleur sémantique (aucun état réel affiché).
- [ ] Relecture humaine des visuels avant publication (le générateur ne publie jamais).

---

## S3 : LinkedIn page + Instagram, `FIABLE`

| Champ | Valeur |
|---|---|
| Canal / compte | LinkedIn, page entreprise Syntheza (document) + Instagram `@syntheza.eip` (carrousel) |
| Date / heure | mardi 27/10/2026, 08:30 (LinkedIn) et 13:00 (Instagram), hypothèses |
| Pilier | `FIABLE` « Rumeur ou info ? Lire un Trust Factor » |
| Objectif | Pédagogie de la vérification : ce que dit et ne dit pas un score (objectif, sans valeur chiffrée) |
| Persona | P1 trading et gestion d'actifs ; P3 médias et communication |
| Lien UTM LinkedIn | https://syntheza.ovh/pilote?utm_source=linkedin&utm_medium=social&utm_campaign=fiable |
| Lien UTM Instagram | https://syntheza.ovh/pilote?utm_source=instagram&utm_medium=social&utm_campaign=fiable |

### Cas utilisé
Faux tweet du compte AP piraté, 23/04/2013 : le Dow Jones perd plus de 140 points puis les récupère en six minutes environ ; perte de capitalisation du S&P 500 estimée par Reuters à 136,5 Md$ (CNBC). Second cas, sans chiffre : fausse image d'explosion près du Pentagone, 22/05/2023 (NPR).

### Texte LinkedIn (page), avec le PDF en pièce jointe

```text
Rumeur ou info ? Le 23/04/2013, un faux tweet a fait chuter le Dow Jones en quelques minutes.

Vers 13 h 07 (heure de New York), le compte Twitter de l'agence AP, piraté, annonce « deux explosions à la Maison Blanche » et le président blessé. Le Dow Jones perd plus de 140 points, puis les récupère en six minutes environ. Selon Reuters, la perte de capitalisation du S&P 500 a atteint 136,5 Md$ avant de se résorber (CNBC, 23/04/2013).

Ce qui a rendu la rumeur crédible : une source d'ordinaire fiable, un contexte tendu, des machines qui lisent les titres. Ce qui la trahissait : aucune autre grande agence ne la relayait.

Dix ans plus tard, même schéma : le 22/05/2023, une fausse image d'explosion près du Pentagone, probablement générée par IA, a fait reculer brièvement le marché (NPR, 22/05/2023).

Lire un Trust Factor, c'est lire :
- ce qu'il dit : qui source l'info, qui la recoupe, à quel palier de source ;
- ce qu'il ne dit pas : que l'info est vraie, et encore moins quoi en faire.
Un score de corroboration aide à douter au bon endroit. Il ne remplace ni le jugement ni la vérification à la source.

Information, pas un conseil en investissement.
Sources : CNBC (23/04/2013), The Guardian (23/04/2013), NBC News (24/04/2013), NPR (22/05/2023).

Quelle est la dernière rumeur qui a fait réagir votre desk ?

#Syntheza #Desinformation #Marches #Trading
```

### Légende Instagram

```text
Rumeur ou info ? Le 23/04/2013, un faux tweet publié depuis le compte piraté d'AP annonçait des explosions à la Maison Blanche : le Dow Jones a perdu plus de 140 points avant de les récupérer en six minutes environ (CNBC, 23/04/2013). Un score de fiabilité ne dit pas si une info est vraie : il montre qui la source, qui la recoupe, et pourquoi. Information, pas un conseil en investissement. Pilote gratuit de 30 jours : lien en bio.
#syntheza #desinformation #marches #veillefinanciere #trustfactor
```

### Visuels (7 slides, 1080×1350)
| # | Fichier | Texte du visuel |
|---|---|---|
| 1 | `s3-fiable/s3-slide-01.png` | Titre : « Un faux tweet fait chuter le Dow. » (23 avril 2013) |
| 2 | `s3-fiable/s3-slide-02.png` | Ce qui s'est passé : compte AP piraté, faux message |
| 3 | `s3-fiable/s3-slide-03.png` | Chiffres : plus de 140 points, six minutes, 136,5 Md$ (Reuters via CNBC) |
| 4 | `s3-fiable/s3-slide-04.png` | Pourquoi ça a marché : source crédible, contexte, lecture automatique |
| 5 | `s3-fiable/s3-slide-05.png` | 22 mai 2023 : fausse image près du Pentagone |
| 6 | `s3-fiable/s3-slide-06.png` | Lire un Trust Factor : ce qu'il dit / ne dit pas |
| 7 | `s3-fiable/s3-slide-07.png` | Un score aide à douter juste, appel au pilote |

PDF LinkedIn : `s3-fiable/s3-document-linkedin.pdf`.

### Sources
- CNBC, « False Rumor of Explosion at White House Causes Stocks to Briefly Plunge », 23/04/2013 : https://www.cnbc.com/2013/04/23/false-rumor-of-explosion-at-white-house-causes-stocks-to-briefly-plunge-ap-confirms-its-twitter-feed-was-hacked.html (13 h 07 ET, plus de 140 points, six minutes, 136,5 Md$ selon Reuters, autres agences n'ont pas relayé).
- The Guardian, « AP Twitter hack causes panic on Wall Street », 23/04/2013 : https://www.theguardian.com/business/2013/apr/23/ap-tweet-hack-wall-street-freefall
- NBC News, « Dow Jones plunges but recovers after fake AP tweet », 24/04/2013 : https://www.nbcnews.com/id/wbna51643095
- NPR, « AI was likely behind faked images of an explosion at the Pentagon », 22/05/2023 : https://www.npr.org/2023/05/22/1177590231/fake-viral-images-of-an-explosion-at-the-pentagon-were-probably-created-by-ai
- Trust Factor (corroboration, pas certificat de vérité) : `Documentation/contexte/backend-et-moteur.md`.

### Points d'attention
- Le slide 6 ne prétend pas que le Trust Factor aurait détecté le faux de 2013 : il rappelle seulement qu'aucune autre grande agence ne l'avait relayé.
- Le slide 3 attribue l'estimation de 136,5 Md$ à Reuters (via CNBC), pas à Syntheza.

### Checklist de relecture
- [ ] AMF : aucune formulation d'achat, de vente ou de conservation ; « information, pas un conseil » présent.
- [ ] Chiffres : chacun relié à une source citée ci-dessus (lien + date) ; aucun chiffre de latence, de client ou d'utilisateur.
- [ ] Charte : noir/blanc/gris, Space Grotesk, aucune couleur sémantique (aucun état réel affiché).
- [ ] Relecture humaine des visuels avant publication (le générateur ne publie jamais).

---

## S4 : LinkedIn, profil fondateur, `PILOTE`

| Champ | Valeur |
|---|---|
| Canal / compte | LinkedIn, profil d'un fondateur (texte + visuel) |
| Date / heure | jeudi 29/10/2026, 08:30 (hypothèse) |
| Pilier | `PILOTE` « On cherche 3 desks pour un pilote de 30 jours » |
| Objectif | Obtenir des demandes de pilote qualifiées : indicateur `demandes_pilote` et `visites_pilote` (objectif chiffré à fixer par l'équipe) |
| Persona | P1 : desk de trading, prop trading, équipe de gestion, family office |
| Lien UTM | https://syntheza.ovh/pilote?utm_source=linkedin&utm_medium=social&utm_campaign=pilote |
| Visuel | `s4-pilote/s4-slide-01.png` (1080×1350) : « On cherche 3 desks pour un pilote gratuit. » |

### Texte final (lien en premier commentaire)

```text
On cherche 3 desks ou équipes de gestion pour un pilote gratuit de 30 jours.

Syntheza est un dashboard temps réel par actif : chaque info est résumée, notée par un Trust Factor expliqué (source, recoupements), avec alertes et journal de décisions.

Le principe : testez 30 jours sur vos actifs. Si vos équipes ne décident pas plus vite sur une info plus fiable, on arrête. Un pilote gratuit de 30 jours précède tout contrat annuel.

Les équipes qu'on cherche :
- un desk de trading, de prop trading ou une équipe de gestion d'actifs (gérants, analystes, family office, banque privée) ;
- qui suit des actifs précis et subit déjà le bruit des sources ;
- prêtes à donner un retour à J+7 et J+25 (court questionnaire NPS).

Ce qu'on vous promet : un cadre clair et un rapport de pilote à la fin. Ce qu'on ne fait pas : recommander d'acheter ou de vendre. Syntheza informe.

Demande de pilote : https://syntheza.ovh/pilote?utm_source=linkedin&utm_medium=social&utm_campaign=pilote

#Syntheza #Trading #AssetManagement #Pilote
```

### Sources
- Offre pilote de 30 jours avant contrat annuel, message « si vos équipes ne décident pas plus vite sur une info plus fiable, on arrête », retour à J+7 et J+25 : business plan d'octobre 2026 et `Documentation/contexte/centre-operations.md` (programme du 11/10/2026).
- Page de demande : https://syntheza.ovh/pilote

### Checklist de relecture
- [ ] AMF : aucune formulation d'achat, de vente ou de conservation ; « information, pas un conseil » présent.
- [ ] Chiffres : chacun relié à une source citée ci-dessus (lien + date) ; aucun chiffre de latence, de client ou d'utilisateur.
- [ ] Charte : noir/blanc/gris, Space Grotesk, aucune couleur sémantique (aucun état réel affiché).
- [ ] Relecture humaine des visuels avant publication (le générateur ne publie jamais).
- [ ] Le critère « retour à J+7 et J+25 » correspond au NPS du back-office ; les critères d'éligibilité sont une proposition de l'équipe, à valider.

---

## S5 : Instagram, post, `BUILD`

| Champ | Valeur |
|---|---|
| Canal / compte | Instagram `@syntheza.eip` |
| Date / heure | vendredi 30/10/2026, 12:30 (hypothèse) |
| Pilier | `BUILD` coulisses de l'équipe |
| Objectif | Humaniser la marque et montrer la répartition réelle des rôles (objectif, sans valeur chiffrée) |
| Persona | P1/P2 qui jugent l'équipe derrière l'outil ; réseau Epitech |
| Lien UTM | https://syntheza.ovh/pilote?utm_source=instagram&utm_medium=social&utm_campaign=build (en bio) |
| Visuel | `s5-build/s5-slide-01.png` (1080×1350) |

### Légende

```text
Trois fondateurs, un produit. Ilhan s'occupe de l'algo, de l'infra et du mobile, Tristan du backend, Raphaël du web. On construit Syntheza en public : un dashboard pour les décideurs qui engagent de l'argent sur une info. Suivez le chantier ; pour tester : lien en bio.
#syntheza #buildinpublic #startup #epitech #fintech
```

### Visuel
| # | Fichier | Texte du visuel |
|---|---|---|
| 1 | `s5-build/s5-slide-01.png` | « Trois fondateurs, un produit. » Ilhan : transverse, algo, infra, mobile. Tristan : backend. Raphaël : web. |

Aucune photo : le visuel est typographique. Une vraie photo d'équipe pourra être ajoutée par l'équipe, sans mise en scène inventée. PDF LinkedIn : `s5-build/s5-document-linkedin.pdf` (une page, optionnel).

### Sources
- Répartition des rôles : donnée fournie par l'équipe (brief de cette tâche), à confirmer par les trois fondateurs. Aucun chiffre sur le visuel.

### Checklist de relecture
- [ ] AMF : aucune formulation d'achat, de vente ou de conservation ; « information, pas un conseil » présent.
- [ ] Chiffres : chacun relié à une source citée ci-dessus (lien + date) ; aucun chiffre de latence, de client ou d'utilisateur.
- [ ] Charte : noir/blanc/gris, Space Grotesk, aucune couleur sémantique (aucun état réel affiché).
- [ ] Relecture humaine des visuels avant publication (le générateur ne publie jamais).
- [ ] Les trois fondateurs valident l'usage de leur prénom et de leur rôle.
