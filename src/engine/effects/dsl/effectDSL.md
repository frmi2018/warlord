# Documentation de effectDSL.ts

## Localisation

`src/engine/effects/dsl/effectDSL.ts`

## Rôle du fichier

Ce fichier définit un **langage simplifié (DSL)** pour décrire les capacités les plus courantes des cartes. Plutôt que d'écrire des fonctions TypeScript complexes pour chaque carte, on utilise des objets "patrons" (patterns) faciles à lire et à maintenir.

L'objectif est de réduire le code répétitif (boilerplate) pour les effets standards comme les bonus de proximité ou de position.

---

## Les Patterns Disponibles

### 1. `BONUS_IF_ADJACENT`

Applique un bonus de statistique si une carte spécifique se trouve sur une case adjacente (gauche ou droite).

- **Usage typique** : Les synergies d'armes (ex: la Hache gagne +2 ATK si le Marteau est à côté).
- **Paramètres** : Nom de la cible, statistique ciblée (ATK/AC), valeur du bonus.

### 2. `BONUS_IF_RANK` & `BONUS_IF_FRONT_RANK`

Applique un bonus si la carte est située à un rang précis du plateau.

- **Usage typique** : Un lancier qui gagne de la défense (AC) lorsqu'il est au premier rang (Front Rank).
- **Paramètres** : Numéro du rang, statistique, valeur du bonus.

### 3. `DRAW_CARD_OF_TYPE`

Crée une action de type **Order** permettant de chercher ou piocher une carte spécifique dans le deck.

- **Usage typique** : "Order : Cherchez un objet de niveau 1".
- **Paramètres** : Type de carte (item/character), niveau maximum.

### 4. `MOVE_FORWARD`

Crée une action de type **Order** permettant à une unité d'avancer vers le front.

- **Usage typique** : Une capacité de "Charge" simplifiée.
- **Paramètres** : Nombre de rangs à franchir.

---

## Pourquoi utiliser le DSL ?

| Avantage        | Description                                                                                                                   |
| :-------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **Lisibilité**  | Un objet `{ pattern: "BONUS_IF_RANK", rank: 1, ... }` est plus simple à comprendre qu'une fonction de filtrage de tableau.    |
| **Maintenance** | Si la logique de calcul de l'adjacence change, on ne modifie qu'un seul endroit (le compilateur du DSL) au lieu de 50 cartes. |
| **Sécurité**    | TypeScript valide que vous n'avez pas oublié de paramètre obligatoire pour un pattern donné.                                  |

---

## La Règle d'Or

> [!CAUTION]
> **Précision avant tout** : Si la règle d'une carte physique est trop complexe pour entrer dans un pattern (ex: "bonus si adjacent à un nain de niveau 2"), **n'utilisez pas le DSL**.
> Un DSL qui "ressemble" à la règle mais ne la respecte pas à 100% crée des bugs difficiles à trouver. Dans ce cas, écrivez l'effet en TypeScript pur.

---

## Exemple d'intégration

Pour ajouter un effet à une carte via le DSL, on utilise généralement une fonction de compilation (souvent nommée `compileDSL`) :

```typescript
// Dans la définition d'une carte
effects: [
  {
    pattern: "BONUS_IF_ADJACENT",
    targetName: "Hammer",
    stat: "ATK",
    bonus: 2,
  },
];
```
