# Documentation de effects/types.ts

## Localisation

`src/engine/effects/types.ts`

## Rôle du fichier

Ce fichier définit les **interfaces et les contrats** qui régissent le comportement des capacités spéciales des cartes. Il sépare clairement ce qu'une carte _est_ (ses stats de base) de ce qu'elle _fait_ (ses effets).

Il est conçu pour être **pur** : il ne dépend pas de React et ne manipule pas d'état global, facilitant ainsi les tests et l'utilisation par l'IA.

---

## Les Deux Catégories d'Effets

Le système distingue deux types d'interactions :

### 1. Effets Passifs (`PassiveEffect`)

Ce sont des capacités toujours actives qui modifient les statistiques en temps réel.

- **Exemple** : "+2 ATK si un nain est adjacent."
- **Fonctionnement** : Le moteur appelle `apply()` à chaque fois qu'il a besoin de calculer les stats d'une unité.

### 2. Effets Actifs (`ActiveEffect`)

Ce sont des capacités déclenchées par une action (Joueur ou IA).

- **Exemple** : "Order : Piochez une carte."
- **Timing** : Définit quand l'action est jouable (`order`, `spend` ou `react`).
- **IA Ready** : Inclut une fonction `aiScore` permettant à l'IA d'évaluer si l'effet est rentable à jouer (score de 0 à 100).

---

## Interfaces Clés

### `EffectContext`

C'est la "photo" du plateau envoyée à l'effet pour qu'il puisse prendre une décision.

- `self` : La carte qui possède l'effet.
- `alliedCards` : La liste des alliés présents.
- `enemyCards` : La liste des ennemis présents.

### `StatModifiers`

Le résultat d'un effet passif. Il contient des bonus optionnels :

- `attackBonus`, `armorBonus`, `levelBonus`, `skillBonus`.

### `ActiveEffectResult`

Le résultat d'un effet actif. Comme le moteur est immuable, l'effet retourne les **nouvelles listes de cartes** modifiées au lieu de changer les cartes existantes.

---

## Structure d'une définition d'effet (`EffectDefinition`)

Chaque carte du jeu peut être liée à une définition d'effet via son `cardBaseId`. Cela permet de séparer la base de données des cartes (JSON) de la logique de programmation (Code).

| Propriété    | Rôle                                                                   |
| :----------- | :--------------------------------------------------------------------- |
| `cardBaseId` | L'ID technique de la carte (ex: "sots-35").                            |
| `effects`    | Un tableau contenant un ou plusieurs `CardEffect` (Passifs ou Actifs). |

---

## Pourquoi cette architecture ?

1.  **Immuabilité** : Les fonctions `apply`, `canUse` et `execute` ne modifient jamais les données reçues. Elles retournent de nouvelles données.
2.  **Prédictibilité** : L'IA peut simuler l'utilisation d'un effet via `canUse` et `aiScore` sans modifier la partie en cours.
3.  **Extensibilité** : On peut ajouter de nouveaux types de bonus dans `StatModifiers` sans casser les effets existants.

---

## Résumé

Ce fichier garantit que :

- Toutes les capacités de cartes suivent la **même structure**.
- Le moteur sait **quand et comment** calculer les bonus.
- L'IA possède les **outils nécessaires** pour évaluer ses options tactiques.
