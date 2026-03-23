# Documentation de useGameBoardComputedValues.ts

## Localisation

`src/hooks/useGameBoardComputedValues.ts`

## Rôle du fichier

Ce hook est le **cerveau analytique** de l'interface du plateau. Son rôle est de transformer les données brutes du `gameState` en valeurs directement exploitables par les composants React sans jamais modifier l'état original. Il utilise intensivement `useMemo` pour garantir que les calculs ne sont relancés que si nécessaire, optimisant ainsi les performances du rendu.

## Logique de calcul

Le hook orchestre plusieurs transformations critiques :

1.  **Enrichissement des données** : Il ajoute des propriétés dynamiques aux cartes (comme `canMove`) pour indiquer à l'UI si une unité est active dans le contexte actuel (manœuvre ou attaque).
2.  **Gestion du ciblage** : Il calcule les listes d'IDs pour les cibles valides en mêlée ou à distance (via des items).
3.  **Résolution des effets passifs** : Il appelle `resolvePassiveEffects` pour calculer les bonus/malus de statistiques de la carte survolée en fonction de sa position et de ses voisins.
4.  **Traduction des phases** : Il transforme l'état technique de la partie en messages d'instruction clairs pour le joueur via `getPhaseMessages`.

## Propriétés clés retournées

- **`isAiBusy`** : Booléen indiquant si l'IA est en train d'attaquer ou de jouer, bloquant ainsi les interactions joueur.
- **`highlightedCardIds`** : Un `Set` d'identifiants permettant d'allumer visuellement les cibles valides.
- **`highlightColor`** : Définit dynamiquement la couleur du surbrillance (ex: vert pour la distance, rouge pour la mêlée).
