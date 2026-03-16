# Documentation de deckRules.test.ts

## Localisation

`src/__tests__/deckRules.test.ts`

## Rôle du fichier

Ce fichier contient les **tests unitaires** définissant les contraintes de construction d'un deck et la configuration de la formation de départ. Il s'assure que les règles de limitation des exemplaires par carte et les constantes du format de jeu sont respectées.

---

## Concept : Deck et Unicité

Pour garantir l'équilibre du jeu, la construction d'un deck suit des règles strictes :

- **Taille du deck** : Le nombre total de cartes autorisé.
- **Limites par carte** : En général, on peut avoir plusieurs exemplaires d'une même carte, sauf si elle est marquée comme "Unique" ou s'il s'agit d'un "Warlord".
- **Formation** : Le nombre de personnages de niveau 1 et 2 déployés automatiquement au début de la partie.

---

## Fonctions de test

### `constants`

Vérifie que les valeurs fondamentales du jeu ne sont pas modifiées par erreur :

- **MAX_DECK_SIZE (20)** : Le deck doit faire exactement 20 cartes.
- **MAX_COPIES_PER_CARD (4)** : On ne peut pas mettre plus de 4 exemplaires d'une carte commune.
- **RANK1_FORMATION_COUNT (3)** : Nombre de troupes de niveau 1 dans la formation de départ.
- **RANK2_FORMATION_COUNT (2)** : Nombre de troupes de niveau 2 dans la formation de départ.

### `isCardUnique`

Vérifie la logique de détection des cartes à exemplaire unique :

- Retourne `true` si la propriété `unique` est à vrai.
- Retourne `true` si la carte possède le trait `Warlord` (un seigneur de guerre est toujours unique).
- Retourne `false` pour les troupes régulières.

### `getMaxCopies`

Vérifie le nombre d'exemplaires autorisés en fonction du type de carte :

- **1 exemplaire** pour les cartes uniques ou les Warlords.
- **4 exemplaires** (valeur de `MAX_COPIES_PER_CARD`) pour les autres cartes.

---

## Variables et États testés

| Élément         | Description                                                |
| :-------------- | :--------------------------------------------------------- |
| `unique`        | Booléen indiquant si la carte est limitée à un exemplaire. |
| `traits`        | Liste des mots-clés (ex: `Warlord`) impactant les règles.  |
| `MAX_DECK_SIZE` | La limite globale de cartes dans un deck.                  |

---

## Interaction avec d'autres fichiers

Ce fichier teste la logique présente dans :

- `src/engine/rules/deckRules.ts` : Contient les constantes et les fonctions de calcul des limites.
- `src/data/cardDatabase.ts` : Définit le type `DeckCard` utilisé pour simuler les cartes en test.

---

## Résumé

Ce fichier garantit que :

1. Les **limites de construction** de deck sont immuables.
2. Le système identifie correctement les **cartes restreintes** (Warlords et uniques).
3. Les **paramètres de départ** de la partie (formation) sont correctement configurés.
