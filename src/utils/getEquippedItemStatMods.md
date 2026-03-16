# Documentation de getEquippedItemStatMods.ts

## Localisation

`src/utils/getEquippedItemStatMods.ts`

## Rôle du fichier

Ce fichier utilitaire calcule l'impact statistique des **objets équipés** (Items) sur un personnage. Dans _Warlord_, les équipements sont des cartes attachées à un personnage qui lui confèrent des bonus permanents d'Attaque (ATK) et de Classe d'Armure (AC).

Sa fonction est d'agréger tous ces bonus pour fournir au moteur de combat les statistiques réelles (modifiées) du personnage au moment du calcul des jets de dés.

---

## Logique de Calcul et Règles (4e Édition)

Le script applique strictement les règles de la 4ème édition concernant l'état physique du porteur :

### 1. La Règle de l'Étourdissement (`Stunned`)

C'est la condition critique de ce fichier :

- **État `ready` ou `spent`** : Les bonus des objets sont **actifs**. Un personnage fatigué bénéficie toujours de son armure et de son épée.
- **État `stunned`** : Les bonus d'objets sont **désactivés**. Un personnage étourdi est considéré comme incapable d'utiliser son équipement efficacement pour se défendre ou attaquer.

### 2. Agrégation des Bonus

La fonction parcourt la liste `equippedItems` du personnage et cumule deux types de statistiques :

- **`attackBonus`** : Augmente les chances de toucher lors d'une attaque.
- **`armorBonus`** : Augmente la Classe d'Armure (CA) pour rendre le personnage plus difficile à blesser.

---

## Fonctionnement technique : `getEquippedItemStatMods()`

La fonction utilise une approche fonctionnelle via `.reduce()` pour garantir une sortie propre et prévisible.

| Étape            | Action                                                                                                  |
| :--------------- | :------------------------------------------------------------------------------------------------------ |
| **Validation**   | Vérifie si la carte est bien de type `character` (via `isCharacter`).                                   |
| **Check État**   | Si la carte est `stunned`, retourne immédiatement un objet vide `{}`.                                   |
| **Accumulation** | Somme les valeurs `attackBonus` et `armorBonus` de chaque item présent dans le tableau `equippedItems`. |

---

## Pourquoi cette architecture ?

- **Découplage** : Le moteur d'attaque n'a pas besoin de savoir quels objets sont portés, il reçoit simplement un modificateur final (ex: `+3 ATK`, `+2 AC`).
- **Performance** : En centralisant ce calcul, on évite de recalculer les bonus d'objets à plusieurs endroits du code (UI, IA, Moteur de résolution).
- **Maintenance** : Si une future règle ajoute un nouveau type de bonus d'item (ex: `skillBonus`), il suffira de modifier ce `reduce` pour qu'il soit pris en compte partout.

---

## Résumé

Ce fichier garantit que :

- Les **statistiques des personnages** sont fidèles à leur équipement actuel.
- La règle spécifique du **Stun** (désactivation des items) est appliquée sans exception.
- Le calcul des combats reste **fluide et précis** grâce à des modificateurs pré-agrégés.
