# Documentation de rangedStrikeRules.ts

## Localisation

`src/engine/rules/rangedStrikeRules.ts`

## Rôle du fichier

Ce fichier implémente la logique spatiale des **attaques à distance** (Ranged Strikes) telle que définie dans la 4e édition de Warlord.

Contrairement à la mêlée qui cible le rang immédiatement adjacent, le tir à distance possède une contrainte de portée spécifique : il doit "enjamber" un rang pour atteindre sa cible. Ce fichier fournit les fonctions pures pour calculer ces distances et valider les cibles légales.

---

## Concept : La Distance Symétrique

Dans ce moteur, les deux armées se font face sur une grille où les rangs sont indexés de **1** (front) à **3** (arrière). La distance entre deux unités est calculée par la somme de leurs `posY` :

- **Distance 2** (1+1) : Premier rang contre Premier rang. C'est la **Mêlée**. Le tir à distance y est interdit.
- **Distance 3** (1+2 ou 2+1) : Premier rang contre Second rang (ou inversement). C'est la portée **Ranged** standard.
- **Distance 4+** : Unités trop éloignées pour un tir standard (ex: Second rang contre Second rang).

---

## Fonctions principales

### `isInRangedStrikeRange(attacker, target): boolean`

Vérifie si la cible se trouve à la distance exacte de **3**.

> "You may not target a character within one rank with a ranged strike." (4e-rules)

### `canTargetWithRangedStrike(attacker, target): boolean`

La fonction de validation complète. Elle vérifie :

1.  **Capacité** : L'attaquant possède-t-il des valeurs dans `rangedStrikes` ?
2.  **Localisation** : Les deux cartes sont-elles bien déployées sur le plateau ?
3.  **Alignement** : S'assure qu'on ne tire pas sur ses propres troupes.
4.  **Portée** : Applique le calcul de distance symétrique.

### `getRangedStrikeTargets(attacker, enemyCards): Card[]`

Utilitaire pour l'IA et l'UI qui filtre toutes les cartes ennemies pour ne retourner que celles qui sont "à portée de fusil".

---

## Règles Spéciales (Conformité 4e)

- **Indépendance des stats** : Le bonus d'ATK de mêlée et le nombre de frappes de mêlée n'influencent jamais les tirs à distance.
- **Targeting** : On ne peut jamais tirer sur le rang de mêlée (distance 2). Un archer au premier rang doit tirer sur le second rang ennemi.

---

## Variables et Types importants

| Élément                  | Description                                                                   |
| :----------------------- | :---------------------------------------------------------------------------- |
| `RANGED_STRIKE_DISTANCE` | Constante fixée à **3**.                                                      |
| `rangedStrikes`          | Tableau de nombres (ex: `[4]`) définissant la puissance et le nombre de tirs. |
| `posY`                   | La coordonnée verticale (1, 2 ou 3) utilisée pour le calcul de distance.      |

---

## Interaction avec d'autres fichiers

- **`combatRules.ts`** : Complète les règles de mêlée pour offrir une validation de combat totale.
- **`rollCombat.ts`** : Utilise les valeurs de `rangedStrikes` pour calculer le succès du tir.
- **`cardClickRouter.ts`** : Permet de mettre en surbrillance les cibles valides lorsqu'un archer est sélectionné.

---

## Résumé

Ce fichier est responsable de :

- **Calculer la portée** spécifique du combat à distance.
- **Interdire le tir** sur le rang de mêlée (distance 2).
- **Identifier les cibles valides** pour les archers et les mages.
  """
