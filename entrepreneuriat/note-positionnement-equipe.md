# Note de positionnement de l'équipe

**Brouillon du 15/10/2026, à compléter et valider par les trois membres.** Le guide Tech 5 attend cette note à la séance d'octobre : « Positioning note: roles, complementarity, legitimacy, gaps, story ». Les faits viennent de la documentation du projet et du CV de Tristan. Les sections **« À compléter »** demandent des informations que seule l'équipe détient : on ne les invente pas.

## 1. Répartition des rôles

> « Divide the roles: who owns what (product, technical, sales, communication, user relations) and how you make decisions. »

Dominantes observées jusqu'ici (`contexte/decisions-et-historique.md`) : Ilhan transverse, algorithmes, infrastructure et mobile ; Tristan backend, DevOps et base de données ; Raphaël frontend web. Elles ont évolué au fil de l'année et ne sont pas exclusives.

**Proposition de rôles pour le trimestre** (à valider) :

| Domaine du guide | Responsable proposé | Ce que ça couvre |
|---|---|---|
| Produit | Ilhan | Priorités ([feuille de route](./feuille-de-route-fonctionnalites.md)), protocoles des deux tours de test, journal d'itérations |
| Technique | Tristan | Backend, données, sécurité et conformité (2FA, SSO, journal scellé), déploiements |
| Ventes | Tristan | Prospection P1, pipeline du back-office, démos, engagement écrit |
| Communication | Raphaël | Site vitrine, identité visuelle, calendrier éditorial, page LinkedIn |
| Relation utilisateurs | Ilhan | Recrutement et suivi des 20 bêta-testeurs, canal direct, relance des décrocheurs |

**Mode de décision** (proposition) :
- chaque responsable tranche dans son domaine ;
- une décision qui touche deux domaines ou plus (cible, prix, identité, changement de périmètre) se prend au point hebdomadaire, à la majorité ;
- toute décision est écrite dans une issue du board 1858, avec sa raison et sa date ;
- le journal d'itérations reprend ces issues.

Cette organisation prolonge ce qui marchait déjà en 2025–2026 : réunion hebdomadaire, issues et GitHub Projects, CI/CD, travail asynchrone.

## 2. Complémentarité

> « Argue your complementarity: what each of you brings that the others don't. »

| Membre | Apport propre | Source |
|---|---|---|
| Tristan | **Données décisionnelles en entreprise** : 11 mois de développement full stack chez Laboratoires Forté Pharma (Monaco), flux ERP / CRM / WMS, API REST, rapports de décision Power BI. **Sécurité** : stage cybersécurité et administration système (Optima Groupe, Monaco), spécialisation cybersécurité (OWASP) à l'Université Laval. **Temps réel** : plateforme anti-triche personnelle (pipeline asynchrone NestJS, Kubernetes, Redis ; détection de signaux frauduleux) | CV, octobre 2026 |
| Ilhan | Vision transverse, algorithmes, infrastructure, application mobile iOS (TestFlight) | Historique du projet. **À compléter** par Ilhan : expériences et compétences propres |
| Raphaël | Interface web, expérience utilisateur, cohérence visuelle du produit | Historique du projet. **À compléter** par Raphaël : expériences et compétences propres |

**Argument d'ensemble** (à valider) : l'équipe couvre toute la chaîne d'un outil de décision en temps réel. Ingestion et qualification de l'information (algorithmes, backend), sécurité et traçabilité que les clients régulés exigent, et interfaces web et mobile pour la lire en quelques secondes. Le produit en production le montre : 317 éditeurs, 677 flux, Trust Factor déterministe, journal de décisions scellé, API Entreprise.

## 3. Légitimité sur le sujet

> « Establish your legitimacy on the subject: why your team in particular is well placed to tackle this problem (lived experience, acquired skill, time spent in the field, network, sheer persistence on the topic). »

Ce qui est déjà démontrable :
- **Persistance** : plus d'un an sur le même problème, la surcharge d'information et la confiance dans la source. Premières études en mars 2025, produit en production, recentré trois fois à partir des retours : confiance en avril, prix en juin, cible professionnelle en octobre.
- **Compétence acquise** : un moteur qui relève 677 flux, classe chaque info et calcule un score de fiabilité explicable, construit et opéré par l'équipe.
- **Ancrage géographique** : Tristan vit près de Monaco et y a travaillé deux fois. Monaco est une place financière (banques privées, sociétés de gestion membres de l'AMAF) qui correspond à la cible P1.

**À compléter par l'équipe** :
- lien personnel de chacun avec le problème : avez-vous déjà eu à décider vite sur une info douteuse ?
- contacts existants dans la finance (famille, stages, alumni) ;
- entretiens déjà menés avec des professionnels de la cible.

## 4. Ce qui manque

> « Say what's missing: the skill or profile you don't have, and how you plan to address it (hiring, partnering, contracting, external support). »

| Manque | Pourquoi c'est critique | Comment y remédier |
|---|---|---|
| **Expertise métier finance** (trading, gestion d'actifs). Aucun membre n'a travaillé sur un desk ; le SWOT du business plan note « expertise métier à recruter » | Crédibilité en démo, choix des sources, vocabulaire, priorités produit | Trouver 1 à 2 conseillers praticiens parmi les bêta-testeurs et les entretiens. Contacts : CFA Society France, A.C.I. Monaco, alumni Epitech, réseau du mentor |
| **Vente B2B** | Cycle de 2 à 4 mois, plusieurs décideurs | Méthode écrite ([prospection.md](./prospection.md)), retours du mentor, entraînement aux démos |
| **Juridique** : droits de presse sur les résumés, frontière avec le conseil en investissement (AMF) | Risques du business plan (slide 16), feu vert juridique visé en janvier 2027 | Avis d'un avocat ou d'une clinique juridique universitaire ; « informer, jamais recommander » appliqué au produit |
| **Disponibilité** : trois étudiants, examens, échanges internationaux en 2025–2026 | Rythme de publication et de prospection | Rôles et calendrier écrits, travail asynchrone déjà rodé |

## 5. L'histoire de l'équipe

> « Your team's story: where the project came from, why this particular group took it on, what you've been through since. »

Trame factuelle (à raconter avec vos mots) :

1. **2025 : le problème.** Une veille éclatée et trop bruyante. Premières études et un premier persona en mars 2025.
2. **2025–2026 : construire à distance.** L'année se passe entre Taïwan, la Chine et le Canada, avec un fort décalage horaire. L'équipe s'organise en asynchrone : issues, réunion fixe, CI/CD. Le produit passe de la collecte à l'aide au jugement : la fiabilité devient le cœur.
3. **Avril–juillet 2026 : le recadrage.** Le mentorat demande de clarifier cible, modèle et prix ; le prix Pro est abaissé à 4,99 €. Greenlight obtenu le 13 juillet, avec une critique nette : acquisition et validation terrain insuffisantes.
4. **Septembre 2026 : le terrain.** Au Warm-up Day, le produit est testé par des étudiants de troisième année et les bugs sont corrigés.
5. **Octobre 2026 : le pivot.** Constat : un produit grand public demande un gros volume de contenu pour séduire, et l'usage gratuit ne prouve pas l'envie de payer. L'équipe vise désormais ceux pour qui une info fausse ou tardive coûte de l'argent : desks, gérants, trésoriers. Le produit est reconstruit autour d'un centre d'opérations temps réel ; le modèle passe à la licence par siège.

**À compléter** : pourquoi ce groupe-là s'est formé, et le moment marquant de chacun.
