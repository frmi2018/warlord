# applyCharge.ts

## Localisation

`src/engine/actions/charge/applyCharge.ts`

## Rôle du fichier

Ce module gère l'**exécution technique** de l'action "Charge". Contrairement à la validation (`canPlayCharge`), ce fichier est responsable de la mutation (pure) de l'état des cartes. Il orchestre le mouvement physique du combattant sur la grille, la gestion de la défausse et la réorganisation des rangs pour maintenir l'intégrité du plateau.

Sa conception est **agnostique** : il peut traiter aussi bien le camp du joueur que celui de l'IA grâce au paramètre `ranksZone`.

---

## Mécanique de Résolution

L'application de la Charge suit un algorithme précis en cinq étapes :

### 1. Détermination du Mouvement

Le fighter est déplacé vers le rang immédiatement inférieur (`posY - 1`). Par exemple, un personnage au Rang 3 passe au Rang 2.

### 2. Gestion de l'Atterrissage (Destination)

Pour éviter que les cartes ne se chevauchent, le script calcule le nombre de cartes déjà présentes dans le rang de destination. Le fighter est placé à la suite des autres (`posX = cardsInDestRank.length`), garantissant un alignement propre.

### 3. Comblement des Trous (Source)

Lorsqu'un personnage quitte son rang d'origine, il laisse un vide. Le module trie les cartes restantes du rang source par leur position `posX` et les **renumérote de 0 à N** pour "resserrer les rangs" et éviter les index manquants.

### 4. Conservation de l'État

Conformément aux règles de Warlord, l'orientation de la carte (Ready, Spent ou Stunned) est **préservée**. Si un combattant était prêt à agir avant la Charge, il l'est toujours après son mouvement.

### 5. Cycle de vie de l'Action

La carte "Charge" utilisée est identifiée et déplacée vers la zone de défausse appropriée (`playerDiscard` ou `aiDiscard`), libérant ainsi de l'espace dans la main de l'attaquant.

---

## Signature de la Fonction

- **`applyCharge(cards, fighterId, chargeCardId, ranksZone)`** :
  - Prend l'état actuel des cartes et les IDs des protagonistes.
  - Retourne un objet `ApplyChargeResult` contenant le nouveau tableau de cartes mis à jour.
  - **Puretée** : La fonction ne modifie pas le tableau original mais en retourne une copie transformée (`map`), ce qui facilite la gestion du state dans React (Redux/Zustand).

---

## Résumé Technique

- **Logique de grille** : Gère les coordonnées `posX` (horizontal) et `posY` (vertical).
- **Gestion Discard** : Routage intelligent de la défausse selon le camp (`ranksZone`).
- **Maintenance de formation** : Réindexation automatique pour éviter les trous dans les rangs après un départ.
- **Architecture** : Fonction "Pure" sans effets de bord, idéale pour les tests unitaires.

---
