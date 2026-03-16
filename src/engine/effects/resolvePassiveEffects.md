# Documentation de resolvePassiveEffects.ts

## Localisation

`src/engine/effects/resolvePassiveEffects.ts`

## Rôle du fichier

Ce fichier est le **moteur de calcul des statistiques dynamiques**. Sa mission est de compiler tous les bonus et malus "invisibles" qui s'appliquent à une carte à un instant T, en fonction de son équipement et de son environnement (alliés proches, position, etc.).

Contrairement aux effets actifs, les effets passifs sont calculés "à la volée" à chaque fois que le moteur a besoin de connaître la valeur réelle d'une caractéristique (Attaque, Armure, Compétence).

---

## Concept : La Fusion des Bonus

Le moteur additionne deux sources distinctes pour déterminer les statistiques finales :

1.  **Effets de Registre (Synergies)** : Proviennent des capacités intrinsèques de la carte ou des auras des alliés (ex: "Gagne +1 ATK si un Nain est adjacent").
2.  **Objets Équipés (Items)** : Proviennent du matériel porté par le personnage (ex: "Chain Mail" donne +2 Armor).

---

## Fonctions principales

### `resolvePassiveEffects(card, alliedCards, enemyCards): StatModifiers`

Le chef d'orchestre du calcul.

- **Récupération** : Interroge le `effectRegistry` pour obtenir les passifs.
- **Réduction (Reduce)** : Parcourt tous les passifs valides et accumule les scores dans un objet unique `StatModifiers`.
- **Fusion** : Ajoute les bonus d'objets via `getEquippedItemStatMods`.

### `getEffectAttackBonus(...)` & `getEffectArmorBonus(...)`

Des "shorthands" (raccourcis) pratiques.

- Ils permettent au code de combat de demander simplement "Quel est le bonus d'attaque ?" sans avoir à manipuler l'objet complet des modificateurs.

---

## Les Statistiques Impactées

| Statistique     | Source Type          | Usage Type                                                  |
| :-------------- | :------------------- | :---------------------------------------------------------- |
| **attackBonus** | Armes, Compétences   | Ajouté au jet de touche en combat.                          |
| **armorBonus**  | Armures, Boucliers   | Augmente la difficulté pour être touché (AC).               |
| **levelBonus**  | Talents, Rareté      | Modifie le niveau effectif pour certains calculs.           |
| **skillBonus**  | Outils, Entraînement | Augmente les chances de réussite des Reacts (Skill Checks). |

---

## Interaction avec d'autres fichiers

Ce fichier est une pierre angulaire consultée par :

- `rollCombat.ts` : Pour calculer la précision d'une frappe.
- `GameBoard / CardPreviewPanel` : Pour afficher les statistiques "vertes" (boostées) ou "rouges" (réduites) dans l'interface.
- `getEquippedItemStatMods.ts` : Utilitaire extrayant les stats des objets stockés dans `card.equippedItems`.

---

## Résumé

Ce fichier est responsable de :

- **Maintenir la vérité mathématique** du plateau.
- **Gérer le cumul** de multiples sources de bonus sans erreurs.
- **Fournir une interface simple** pour interroger les statistiques complexes.
  """
