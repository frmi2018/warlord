# Documentation de globalBonuses.ts

## Localisation

`src/engine/effects/globalBonuses.ts`

## Rôle du fichier

Ce fichier sert de **stockage temporaire** pour les bonus passifs qui affectent l'ensemble de la partie. Il permet à certaines fonctions de validation (qui ne reçoivent pas l'objet `GameState` complet en paramètre) de consulter des modificateurs de règles en temps réel.

> [!WARNING]
> **Statut : Obsolète (Legacy)** > Ce fichier est une solution de transition. À terme, ces bonus seront directement intégrés et consultés via l'objet `bonuses` présent dans le `GameState` pour respecter une architecture 100% immuable.

---

## Concept : Les Bonus de Faction

Certaines capacités de cartes (souvent les Warlords ou les bannières) modifient les règles de base pour toutes les unités d'une même faction.

_Exemple traité ici :_ Un bonus permettant aux nains d'équiper des objets d'un niveau supérieur à la normale.

---

## Structure de l'objet

### `globalBonuses`

| Propriété              | Type     | Description                                                                                  |
| :--------------------- | :------- | :------------------------------------------------------------------------------------------- |
| `dwarfEquipLevelBonus` | `number` | Valeur ajoutée à la limite de niveau d'équipement pour les personnages de faction **Dwarf**. |

---

## Utilisation et Cycle de vie

1.  **Mise à jour** : Les handlers d'événements (dans `eventBus`) modifient cette valeur lorsqu'une carte spécifique entre ou quitte le jeu.
2.  **Consultation** : Des fonctions comme `canEquipItem` lisent cette valeur pour autoriser ou non l'attachement d'un objet.

---

## Pourquoi migrer vers le GameState ?

L'utilisation d'un export d'objet global (`export const`) pose deux problèmes majeurs :

1.  **Testabilité** : Il est difficile de réinitialiser proprement cette valeur entre deux tests unitaires.
2.  **Prédictibilité** : Le moteur de jeu doit être une fonction pure. Un bonus "caché" dans un fichier externe rend le débogage complexe car l'état du jeu ne suffit plus à expliquer un comportement.

---

## Résumé

Ce fichier garantit :

- Le support des **capacités passives de faction** (spécifiquement les Nains).
- Une **liaison rapide** entre les règles d'équipement et les effets globaux actifs.
