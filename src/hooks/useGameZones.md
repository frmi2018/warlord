# Documentation de useGameZones.ts

## Localisation

`src/hooks/useGameZones.ts`

## Rôle du fichier

Le hook `useGameZones` est un **utilitaire de segmentation**. Son rôle est de prendre la masse informe de cartes présentes dans le `GameState` (stockées dans deux grands tableaux : `playerCards` et `aiCards`) et de les ventiler dans les 8 zones logiques du plateau de jeu.

Il sert à simplifier le composant `GameBoard` en lui fournissant des listes de cartes déjà filtrées et prêtes à être affichées dans leurs conteneurs respectifs (Main, Pioche, Défausse, Rangs).

---

## Les 8 Zones du Jeu

Le hook sépare les cartes en deux groupes (Joueur et IA), chacun possédant quatre zones distinctes :

| Zone                                        | Usage                                                              |
| :------------------------------------------ | :----------------------------------------------------------------- |
| **Ranks** (`playerRanks` / `aiRanks`)       | Les cartes de personnages déployées sur le champ de bataille.      |
| **Deck** (`playerDeck` / `aiDeck`)          | La pioche. Les cartes sont généralement face cachée ici.           |
| **Hand** (`playerHand` / `aiHand`)          | Les cartes disponibles pour être jouées ou défaussées.             |
| **Discard** (`playerDiscard` / `aiDiscard`) | La défausse (cimetière) où vont les cartes détruites ou utilisées. |

---

## Optimisation : Mémoïsation stratégique

Le filtrage d'un grand nombre de cartes à chaque rendu pourrait impacter les performances. `useGameZones` utilise `useMemo` pour chaque zone :

- **Dépendances précises** : Les zones du joueur ne sont recalculées que si `playerCards` change. Les zones de l'IA ne bougent que si `aiCards` change.
- **Stabilité référentielle** : Cela garantit que si une action ne modifie que les cartes de l'IA, les tableaux `playerHand` ou `playerRanks` gardent la même référence mémoire, évitant ainsi des rendus inutiles des composants du joueur.

---

## Pourquoi centraliser ce filtrage ?

Avant ce hook, chaque partie de l'interface devait appeler `getCardsByZone` manuellement. Centraliser cette logique permet :

1.  **Code plus propre** : `GameBoard` devient déclaratif (`const { playerHand } = useGameZones(...)`).
2.  **Source de vérité unique** : Si le nom d'une zone change dans les types, il n'y a qu'un seul fichier à mettre à jour.
3.  **Facilité de Debug** : Il est très facile de placer un log ici pour voir la répartition des cartes sur le plateau après une action complexe.

---

## Interaction avec d'autres fichiers

- **`getCardsByZone.ts`** : La fonction utilitaire pure qui effectue le filtrage réel basé sur la propriété `zone` de l'objet `Card`.
- **`GameState.ts`** : La source de données brute contenant les tableaux complets.
- **`GameBoard.tsx`** : Le consommateur principal qui distribue ces listes aux composants `Hand`, `Deck`, et `RankSlot`.

---

## Résumé

Ce fichier est responsable de :

- **Organiser spatialement** les cartes du jeu.
- **Optimiser les performances** d'affichage via la mémoïsation.
- **Réduire le boilerplate** dans les composants de vue.
  """
