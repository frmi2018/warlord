# Documentation de useGameBoardLocalState.ts

## Localisation

`src/hooks/useGameBoardLocalState.ts`

## Rôle du fichier

Ce hook gère exclusivement l'**état local et éphémère** de l'interface utilisateur. Il stocke les données qui ne concernent pas les règles du jeu "moteur" mais qui sont indispensables pour une navigation fluide sur le plateau.

## États gérés

Le hook expose des primitives simples (états et setters) pour :

- **Interactions de menus** : `actionCard` suit la carte dont le menu d'actions est actuellement ouvert.
- **Aperçu visuel** : `displayedCard` garde en mémoire la dernière carte survolée pour l'afficher dans le panneau de détails.
- **Déploiement** : `deployingCard` et `deployInfo` stockent temporairement les données d'une carte en train d'être glissée sur le plateau.
- **Navigation (Minimap)** : Gère quel rang (1, 2 ou 3) est actuellement mis en avant pour le joueur et pour l'IA.
- **Focus** : Permet de cibler précisément une carte spécifique pour des effets de zoom ou de mise en évidence.

## Intérêt architectural

En isolant ces états dans ce fichier, on évite de polluer les hooks métier avec des variables de "confort" visuel. Cela permet également de réinitialiser l'interface (fermer les modales, reset le hover) très facilement sans risquer de désynchroniser la partie en cours.
