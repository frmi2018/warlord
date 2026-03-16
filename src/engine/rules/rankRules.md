# Documentation de rankRules.ts

## Localisation

`src/engine/rules/rankRules.ts`

## Rôle du fichier

Ce fichier est le **gardien de la structure des armées**. Dans Warlord, la formation des rangs obéit à des règles géométriques et hiérarchiques strictes. Ce module fournit les fonctions nécessaires pour valider le déploiement des unités et s'assurer que la formation reste légale au fil de la partie.

Il centralise les deux piliers du déploiement : les **contraintes de niveau** et la **géométrie pyramidale** des rangs.

---

## Concept : La Formation Légale

Le système de rangs de Warlord (4e édition) repose sur trois règles majeures gérées ici :

1.  **Règle du Niveau** : Un personnage ne peut pas être déployé dans un rang inférieur à son propre niveau.
    - _Exemple :_ Un héros de niveau 3 doit être déployé au rang 3 ou plus.
2.  **Règle de l'Adjacence** : On ne peut pas créer un "trou" entre les rangs. Pour occuper le rang 2, il doit y avoir au moins une carte au rang 1.
3.  **Règle de la Pyramide (Nombre d'unités)** : Un rang ne peut jamais contenir plus d'unités que le rang situé immédiatement devant lui.
    - _Exemple :_ Si vous avez 2 soldats au rang 1, vous ne pouvez pas avoir 3 soldats au rang 2.

---

## Fonctions principales

### `isRankLegal(card, rank, existingCards): boolean`

Vérifie la légitimité individuelle d'un placement :

- La carte est-elle un personnage ?
- Le rang est-il compatible avec son niveau (`minRank`) ?
- Le rang précédent est-il occupé ?

### `canOccupyRank(card, rank, existingCards): boolean`

Cette fonction est plus avancée : elle **simule** l'arrivée de la carte pour vérifier si cela ne rend pas la formation entière illégale (notamment par rapport à la règle de la pyramide).

- Elle groupe les cartes par rang.
- Elle compare les effectifs de chaque rang consécutif.
- Elle interdit le placement si le rang `N` devient plus peuplé que le rang `N-1`.

### `getMinDeployRank(card): number`

Détermine le point d'entrée minimal d'un personnage. C'est ici que le lien est fait entre la statistique `level` de la carte et la coordonnée `posY` du plateau.

---

## Variables et Types importants

| Élément       | Description                                                                              |
| :------------ | :--------------------------------------------------------------------------------------- |
| `FRONT_RANK`  | Constante fixée à **1**. Le point le plus exposé.                                        |
| `cardsByRank` | Une `Map` temporaire utilisée pour compter les effectifs par ligne lors des validations. |
| `posY`        | Dans le code, `posY` correspond directement au numéro du rang (1, 2, 3...).              |

---

## Interaction avec d'autres fichiers

- **`deployReducer.ts`** : Utilise ces règles pour autoriser ou rejeter le placement d'une carte depuis la main.
- **`aiDeploy.ts`** : L'IA consulte ces fonctions pour trouver des emplacements valides pour ses propres troupes.
- **`aiResolveIllegalRanks.ts`** : Se base sur ces définitions pour réorganiser le plateau après une mort qui aurait brisé la pyramide.

---

## Résumé

Ce fichier est responsable de :

- **Appliquer la hiérarchie** des niveaux sur le champ de bataille.
- **Maintenir la forme pyramidale** de l'armée.
- **Empêcher les déploiements incohérents** (unités flottantes, rangs vides).
  """
