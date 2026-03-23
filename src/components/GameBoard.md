# Documentation de GameBoard.tsx

## Localisation

`src/components/GameBoard.tsx`

## Rôle du fichier

Le `GameBoard` est le **composant racine et l'orchestrateur principal** de l'interface de jeu. Sa responsabilité unique est de servir de point de connexion (câblage) entre la multitude de hooks métier qui gèrent la logique et les composants de présentation qui gèrent l'affichage. Il ne contient aucune logique métier ni calcul complexe ; il délègue tout aux hooks spécialisés pour produire le layout final.

## Structure et Flux de données

1.  **Initialisation de l'État** : Il initialise et détient l'état de jeu unifié (`gameState`) via la fonction `createInitialGameState`.
2.  **Câblage des Hooks** : Il instancie tous les hooks métier nécessaires (Combat, Manœuvre, Équipement, Phase Manager, etc.) en leur injectant les dépendances et les fonctions de rappel (callbacks) requises.
3.  **Extraction des Zones** : Il utilise le hook `useGameZones` pour segmenter le plateau en zones logiques comme la main, la défausse, les rangs et le deck pour le joueur et l'IA.
4.  **Délégation UI** : Il répartit les états et les gestionnaires (handlers) entre trois catégories de composants : les modales, la grille de jeu centrale et les panneaux d'information latéraux.

## Composants de rendu principaux

- **`PhaseIndicator`** : Affiche le tour actuel, la phase, et permet de passer à l'action suivante ou de rendre la main.
- **`RanksDisplay`** : Affiche les rangs de combat et gère les interactions visuelles (surbrillance, clics) sur les cartes du terrain.
- **`HandDisplay`** : Affiche les cartes en main et gère la sélection pour le déploiement ou l'utilisation d'actions.
- **`CardPreviewPanel`** : Panneau latéral affichant les détails et les modificateurs de statistiques de la carte actuellement survolée.
- **`MinimapBoard`** : Permet une navigation tactique et un focus rapide entre les différents rangs du champ de bataille.

## Résumé des responsabilités

- Gérer le cycle de vie de l'état de jeu global (`gameState`).
- Distribuer les actions via le dispatcher vers les composants enfants.
- Structurer le Layout visuel (colonnes de phase, plateau central, panneau d'aperçu).
- Afficher les notifications en temps réel comme les actions de l'IA ou les résultats de jets de dés.
