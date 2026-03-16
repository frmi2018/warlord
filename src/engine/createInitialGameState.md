# Documentation de createInitialGameState.ts

## Localisation

`src/engine/createInitialGameState.ts`

## Rôle du fichier

Ce fichier est la **fabrique (factory)** de l'état du jeu. Son rôle est de transformer les informations des decks choisis par le joueur et l'IA en un objet `GameState` complet, prêt à être utilisé par le moteur de jeu.

Il gère la mise en place initiale : distribution des cartes dans les bonnes zones (deck, main, rangs), initialisation du tour 1 et calcul des bonus de départ.

---

## Logique de Mise en Place

### 1. Construction des Armées

La fonction utilise `buildCardsForOwner` pour chaque camp :

- Si un deck a été sauvegardé, elle reconstruit la formation exacte.
- Sinon, elle génère un deck valide et une formation par défaut.
- Elle attribue des **IDs uniques** à chaque instance de carte pour éviter les conflits.

### 2. Le Cas Particulier de "King Xod"

Dans Warlord, le Warlord commence la partie **déjà en jeu** (dans les rangs).

- **Problème** : Les bonus normaux se déclenchent lors de l'événement "Unité déployée". King Xod n'étant jamais "déployé" (il est là dès la seconde zéro), son bonus de faction risquait d'être ignoré.
- **Solution** : La fonction vérifie manuellement sa présence via `isKingXodInRanks` et applique immédiatement le bonus `dwarfEquipLevelBonus` dans l'état initial.

---

## Structure de l'État Initial

Le `GameState` retourné contient tous les compteurs à zéro et les drapeaux de contrôle :

| Propriété      | Valeur Initiale | Description                                            |
| :------------- | :-------------- | :----------------------------------------------------- |
| `phase`        | `"Draw Phase"`  | Le jeu commence toujours par la phase de pioche.       |
| `turnNumber`   | `1`             | Initialisation au premier tour.                        |
| `activePlayer` | `"player"`      | Le joueur humain a l'initiative par défaut au tour 1.  |
| `reactWindow`  | `null`          | Aucune fenêtre de réaction n'est ouverte au démarrage. |
| `bonuses`      | Calculé         | Contient les bonus passifs détectés dès le départ.     |

---

## Contraintes Techniques

- **Pureté** : À l'exception d'une mise à jour nécessaire de `globalBonuses` (legacy), la fonction est déterministe.
- **Sérialisation** : L'objet retourné est 100% compatible avec `JSON.stringify`, ce qui permet de sauvegarder ou de restaurer une partie facilement.
- **Indépendance** : Ce fichier ne contient aucune référence à React, il peut donc être testé dans un environnement Node.js pur.

---

## Pourquoi synchroniser globalBonuses ici ?

Actuellement, certaines parties du code (comme la validation d'équipement) lisent encore les bonus dans un objet global plutôt que dans le `GameState`. En synchronisant `globalBonuses` au moment de la création de la partie, on s'assure que King Xod "active" les capacités de ses nains dès le premier clic du joueur.

---

## Résumé

Ce fichier garantit que :

- La partie démarre dans un **état stable et prédictible**.
- Les **formations de départ** sont correctement positionnées sur la grille.
- Les **capacités de Warlord** sont actives dès le début du Tour 1.
- Le système de **réactions (Reacts)** est initialisé et prêt à intercepter les actions.
