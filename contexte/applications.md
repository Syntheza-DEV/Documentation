# Applications web et mobile

**Lire les frontières de session avant de modifier l’authentification ou les caches.**

Consolidation : **2026-10-09**. « Observé localement » décrit les sources lues, pas une version actuellement déployée. « Rapporté » désigne un résultat historique daté. Aucune application ni suite de tests n’a été exécutée pour cette consolidation.

## Socle réel

| | Web - observé localement | Mobile - observé localement |
|---|---|---|
| Interface | React 19.2, TypeScript, Vite 6, React Router 7 | React Native 0.81.5, React 19.1, Expo SDK 54, Expo Router 6 |
| Composants | Radix UI, Tailwind 4, Framer Motion | Tamagui 2 RC, Reanimated 4, composants natifs |
| Données | Apisauce, TanStack Query 5, Zustand 5 | Apisauce, TanStack Query 5, Zustand 5 |
| Outillage | Node ≥ 20 ; pnpm 10.11.0 ; Vitest | Node ≥ 20 ; pnpm 10.33.0 ; Jest ; Maestro |

Le README web historique JavaScript/Ant Design et les anciennes instructions mobile Yarn ne décrivent plus ce socle. Le mobile utilise un **dev client Expo**, pas Expo Go comme environnement de référence. Le back-office reste web-only : parité produit ne signifie pas recopier l’administration sur téléphone.

Les services sont organisés par domaine et partagent les contrats de l’API, pas une bibliothèque cliente unique. Les noms actuels sont notamment `publisher`, `publisherSubscription`, `feed`, `trust`, `comment` et `notification`. Ne pas réintroduire les anciennes routes `Source` en copiant un ancien service.

Le modèle partagé et les contraintes de feed, digest, confiance et budget sont résumés dans [Backend et moteur](./backend-et-moteur.md). La distinction utile côté client : un Publisher est un éditeur global, un Channel son canal d’ingestion et un Item le contenu affiché ; l’abonnement porte sur l’éditeur, pas sur une ancienne source privée à l’utilisateur.

## Authentification : deux stockages, un contrat API

- **Observé localement - web :** `AuthContext` pilote l’identité ; `tokenService` conserve utilisateur, access token et refresh token dans le stockage navigateur. Les appels utilisent `Authorization: Bearer`. Le client relance le refresh avec le refresh token dans `X-Refresh-Token` et le corps ; ne pas le décrire comme un client exclusivement fondé sur les cookies.
- **Observé localement - mobile :** access/refresh tokens dans `expo-secure-store`, profil utilisateur via le stockage applicatif. L’intercepteur rafraîchit une seule fois à la fois, met les requêtes concurrentes en attente et avertit `AuthContext` en cas d’échec. Effacer seulement SecureStore ne suffit pas : l’interface doit également quitter la session.
- **Rapporté le 16 mai 2026 :** le mobile se bloquait après expiration du token parce que le backend attendait seulement un cookie de refresh et que l’état React restait connecté. Le correctif a introduit le refresh token dans les réponses et le transport header/corps. Les sessions créées avant ce correctif demandaient une reconnexion unique ; ce n’est pas une procédure de migration récurrente.
- **Contrats observés :** Google transmet `idToken`, Apple `identityToken`. Les services doivent respecter l’enveloppe API `{ success, data }` et les enums backend, pas fabriquer leurs propres valeurs. Les anciennes divergences `medium`/`normal` et `monthly`/`never` ont déjà causé des incompatibilités.

### Changement de compte : ne pas confondre identité et cache

**Rapporté le 8 juillet 2026, correctif présent localement :** après déconnexion de A puis inscription de B, Profil et Digest pouvaient encore montrer A. Les clés TanStack partagées et un `staleTime` de cinq minutes rendaient ces données encore fraîches. Le web appelle maintenant `queryClient.clear()` au logout **et** à l’échec d’authentification. Invalider les requêtes seulement aurait laissé un affichage transitoire de l’ancien compte.

**Réserves locales, non reproduites ici :**

- Le refresh web peut encore sauvegarder un token après le nettoyage d’une déconnexion déjà engagée : aucune garde de fin de session n’est présente autour de cette sauvegarde. Le risque signalé en juillet ne doit pas être déclaré résolu par le seul `clear()` de TanStack.
- Le mobile possède un QueryClient au niveau racine, mais son `AuthContext` n’effectue pas le même nettoyage au logout/échec d’authentification. L’isolation entre comptes ne bénéficie donc pas du correctif web à cet endroit ; le symptôme sur appareil n’est pas établi ici.
- Les stores de préférences web/mobile restent en mémoire sans action de remise à zéro dans ces sorties de session. Un rechargement n’est pas une garantie d’isolation utilisable comme conception.
- L’intercepteur web exclut les routes d’authentification publiques et le refus d’un mot de passe actuel incorrect du refresh automatique. Le mobile n’a pas cette même liste d’exclusion ; ne pas annoncer une parité complète de gestion des erreurs.

## État client : préserver les mises à jour concurrentes

**Observé localement :** les deux stores Zustand de préférences appliquent une modification optimiste puis la réponse serveur. En cas d’échec, ils restaurent **la clé modifiée uniquement**, et non l’ancien objet entier. C’est la leçon d’un bug historique où l’échec d’une préférence annulait une autre modification faite entre-temps. Cela ne prouve pas, à lui seul, que toute concurrence entre réponses réussies est réglée.

La préférence de thème est un état réactif : `useColors()` et le provider Tamagui lisent `preferences.theme` et respectent `light`, `dark` ou le système. Le défaut corrigé en avril 2026 était un thème système lu indépendamment, qui ignorait la préférence utilisateur.

La note générique sur les fonctions Zustand à référence constante venait **d’un autre projet**. Elle n’est pas un incident Syntheza et n’est pas reprise comme dette. Ici, les sélecteurs de thème observés lisent directement la donnée concernée.

## Parité de charte : intention et limites

**Rapporté le 24 juin 2026 :** le chantier mobile/web a été fusionné pour aligner couleurs, Space Grotesk, rayons, composants et écrans, hors back-office. Ce résultat de chantier n’est pas une preuve que les deux interfaces restent identiques après leurs évolutions respectives.

- **Sources de style locales :** `src/globals.css` côté web ; `src/design/colors.ts` et `useColors()` pour les couleurs mobiles. Les palettes secondaires diffèrent désormais : ne pas promettre une égalité pixel à pixel.
- **Piège Tamagui :** les thèmes clair et sombre mappent encore `primary` vers le même token, alors que `useColors()` inverse correctement premier plan/fond. Une retouche fondée uniquement sur `$primary` peut contredire le thème clair.
- **Typographie :** le mobile charge Space Grotesk jusqu’à 700. Ne pas supposer une graisse 800 native identique au rendu web. Les valeurs numériques d’espacement utilisées dans les composants sont des pixels, pas automatiquement des références de tokens.
- **En-têtes :** `ScreenHeader` porte réellement ses marges horizontales et son espacement supérieur. Son commentaire disant le contraire est périmé ; l’imbriquer dans un deuxième conteneur margé crée un décalage.
- **Avatar web, incident rapporté le 24 juin :** une URL absolue transformée en chemin relatif causait des 404 ; la politique CORP du backend bloquait ensuite l’image cross-origin malgré un HTTP 200. Le correctif rapporté limitait l’autorisation cross-origin à `/uploads`. Une image visible en natif ne valide donc pas son affichage navigateur.

## Livré dans les sources, rapporté, ou différé

### Ce qui existe localement

L’écran Compte mobile contient **un vrai sélecteur/upload d’avatar et un vrai changement de mot de passe**. Les notes de juin les disant absents ou simulés sont dépassées. Cela ne prouve pas que le profil déjà chargé se rafraîchit partout sans délai, ni que le parcours a été rejoué sur appareil.

L’article mobile affiche résumé, contenu, accès à la source, TrustBadge et actions de commentaire avec signalement/blocage. Le détail lu ne rend pas d’image de couverture dédiée. La liste mobile sépare les notifications personnelles et `SYSTEM`, sans garde de rôle dans cet écran : conserver cette divergence dans les arbitrages de parité, sans en déduire une exposition serveur non vérifiée.

Discover propose Tendances, Récents et Éditeurs avec abonnement/désabonnement. Ce n’est pas encore l’expérience éditoriale approfondie imaginée en mai.

### Résultats historiques à ne pas perdre

**Rapporté au 9 septembre 2026 :** le cycle de recette web a notamment corrigé l’envoi de réinitialisation via Brevo, les erreurs lisibles en français, les abonnements de la sidebar, les graphiques par rôle et l’interface sombre. Le feed conserve abonnements et pertinence, sans les anciens filtres de 48 heures/quota ; pagination progressive et défilement ont été vérifiés dans ce cycle. L’explication de confiance est conservée puis relue sans relancer l’IA ; son absence pour un ancien score est explicitée.

Le bilan historique indique **18 ATP web PASS**, dont le digest validé trois fois par le responsable du projet. Cela ne signifie ni que les 18 ont tous été rejoués après la dernière modification, ni que le mobile a été validé en même temps. [État et recette](./etat-et-recette.md) précise ce périmètre.

### Exploration, sans engagement de livraison

- **18 mai 2026 - Discover enrichi :** détail Publisher, regroupement de contenus, score/statut éditeur, logos et liens par slug. Motivation : exploiter un éditeur global plutôt qu’une simple liste plate avec bouton Suivre. Le Discover local ci-dessus ne réalise pas toute cette proposition.
- **18 mai 2026 - suggestion/vote :** idée communautaire, mais objet du vote et effet sur la confiance non définis. Ne pas la présenter comme une fonctionnalité promise ou un modèle métier déjà choisi.

## Pièges de reprise

1. **Vérifier chaque route réellement servie avant un port « iso ».** En mai, une méthode de comptage d’abonnés copiée d’un ancien service appelait une route inexistante. La ressemblance avec l’ancien client n’est pas la conformité backend.
2. **Isoler l’environnement avant de lancer un écran.** Les exemples et les valeurs par défaut ciblent l’API publique ; sur mobile, changer `EXPO_PUBLIC_API_URL` seul ne modifie pas actuellement `Config.API_URL`. Les étapes sûres figurent dans [Exploitation](./exploitation.md).
3. **Séparer web et natif dans la recette.** Maestro doit gérer launcher/dev menu du dev client, dialogues iOS et sélecteurs d’accessibilité. Sur un seul simulateur, les flows sont séquentiels. Les anciens résultats de tests ne certifient pas les builds locaux actuels.

## Sources

Les décisions et réserves utiles sont intégralement résumées ici ; les références suivantes sont des preuves détaillées facultatives. Les liens GitHub ne constituent pas un relevé de production ; voir les révisions et limites dans [Sources](./sources.md).

- **Socle et sessions :** [package web](https://github.com/Syntheza-DEV/frontend-web/blob/main/package.json), [package mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/package.json), [AuthContext web](https://github.com/Syntheza-DEV/frontend-web/blob/main/src/contexts/AuthContext.tsx), [intercepteur web](https://github.com/Syntheza-DEV/frontend-web/blob/main/src/services/api/index.ts), [AuthContext mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/contexts/AuthContext.tsx).
- **État et charte :** [store web](https://github.com/Syntheza-DEV/frontend-web/blob/main/src/stores/preferencesStore.ts), [store mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/stores/preferencesStore.ts), [CSS web](https://github.com/Syntheza-DEV/frontend-web/blob/main/src/globals.css), [couleurs mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/design/colors.ts), [ScreenHeader](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/components/ScreenHeader.tsx).
- **Parcours observés :** [Compte mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/app/%28app%29/settings/account.tsx), [Discover mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/app/%28app%29/%28tabs%29/discover.tsx), [article mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/app/%28app%29/article/%5Bid%5D.tsx), [notifications mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/app/%28app%29/%28tabs%29/notifications.tsx), [layout racine mobile](https://github.com/Syntheza-DEV/frontend-mobile/blob/main/src/app/_layout.tsx).
- **Historique extrait :** `frontend-architecture.md` - 20 juillet 2026 ; `mobile_charter_iso.md` - 24 juin 2026 ; `web-auth-session-cache.md` - 8 juillet 2026 ; `feedback_port_iso.md` et `future_features_mobile.md` - 18 mai 2026 ; `resolved-issues.md` - mars–mai 2026.
- **Outillage et périmètre :** `incident_2026-05-16.md` - 16 mai 2026 ; `feedback_mobile-expo-cli.md` et `feedback_mobile-testing.md` - revus le 20 juillet 2026 ; `feedback_zustand_reactive_pattern.md` - source d’un autre projet, écartée comme incident Syntheza lors de cette consolidation ; recette du 9 septembre 2026 détaillée dans [État et recette](./etat-et-recette.md).
