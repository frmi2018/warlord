# Documentation de rankRules.test.ts

## Localisation

`src/__tests__/rankRules.test.ts`

## Rôle du fichier

Ce fichier contient les **tests unitaires** validant les règles de positionnement des personnages sur le plateau de jeu. Il s'assure que les cartes sont déployées aux bons endroits (rangs) en respectant les contraintes de niveau et de structure de l'armée.

---

## Concept : Les Rangs (Ranks)

Le plateau de jeu est organisé en rangs horizontaux (représentés par l'axe `posY`).

- Le **Rang 1** est le "Front Rank" (premier rang).
- Un personnage a un niveau qui détermine son rang de déploiement minimal.
- Un nouveau rang ne peut être créé que si le rang précédent est déjà occupé.

---

## Fonctions de test

### `FRONT_RANK` & `isFrontRank`

- Vérifie que la constante du premier rang est bien fixée à `1`.
- Valide que la fonction de détection identifie correctement le rang 1 par rapport aux autres.

### `getMinDeployRank`

- Vérifie qu'un personnage ne peut pas être déployé sur un rang inférieur à son niveau (ex: un niveau 2 commence au minimum au rang 2).
- S'assure que les objets (Items) n'ont pas de contrainte de rang (retourne `undefined`).

### `isRankLegal`

C'est le cœur de la validation. Les tests vérifient :

- **Type de carte** : Seuls les personnages peuvent occuper un rang.
- **Cohésion de l'armée** : On ne peut pas déployer au rang 2 si le rang 1 est totalement vide (pas de "trou" dans les lignes).
- **Niveau** : Le rang choisi doit être supérieur ou égal au niveau de la carte.

### `canOccupyRank`

Vérifie s'il est possible d'ajouter une carte spécifique à un rang donné :

- Autorise le déploiement si la structure est respectée.
- Empêche la création de rangs illégaux (plus de 2 personnages par rang selon la logique de ce test).

---

## Helpers

- **`makeCharacter`** : Génère un personnage de test avec un niveau et des stats par défaut.
- **`makeItem`** : Génère une carte objet pour tester les cas d'exclusion.

---

## Variables et États testés

| Élément         | Description                                                              |
| :-------------- | :----------------------------------------------------------------------- |
| `level`         | Le niveau du personnage, utilisé pour calculer son rang min.             |
| `posY`          | Représente le numéro du rang sur le plateau.                             |
| `posX`          | Représente la position horizontale dans le rang.                         |
| `existingCards` | La liste des cartes déjà présentes pour vérifier la cohésion des lignes. |

---

## Interaction avec d'autres fichiers

Ce fichier de test valide la logique définie dans :

- `src/engine/rules/rankRules.ts` : Le code métier des règles de rangs.
- `src/types/card.ts` : Pour la définition des structures de cartes.

---

## Résumé

Ce fichier de test garantit que :

1.  Les personnages respectent leur **rang de déploiement minimal** lié à leur niveau.
2.  L'armée reste **compacte** (pas de rangs avancés sans soutien derrière).
3.  Le système de coordonnées du plateau (`posY`) est correctement interprété par le moteur.
