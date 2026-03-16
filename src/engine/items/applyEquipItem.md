# Documentation de applyEquipItem.ts

## Localisation

`src/engine/items/applyEquipItem.ts`

## Rôle du fichier

Ce fichier contient la logique pure permettant d'**équiper un objet (item) sur un personnage**.

Il s'agit d'une fonction dite "pure" car elle ne modifie pas directement l'état global du jeu, mais retourne un **nouveau tableau de cartes** mis à jour (principe d'immutabilité). Elle gère les déplacements de cartes entre la main, le personnage et la défausse.

---

## Concept : Équipement d'Item

Lorsqu'un joueur équipe un item :

1.  **L'item quitte la main** du joueur.
2.  **Le personnage dépense son action** : son état (orientation) devient `"spent"`.
3.  **Gestion des emplacements** : Si le personnage porte déjà un objet au même endroit (ex: deux armes à deux mains), l'ancien objet est remplacé et envoyé à la défausse.
4.  **L'item est attaché** : Il est ajouté à la liste `equippedItems` du personnage.

---

## Fonction principale

### `applyEquipItem(allCards, itemId, characterId, discardZone): Card[]`

Cette fonction transforme le tableau de cartes pour refléter l'action d'équipement.

**Déroulement technique :**

- **Identification** : Trouve l'item et le personnage dans la liste fournie via leurs IDs.
- **Nettoyage des slots** : Utilise `getItemsToDestroy` pour identifier les objets déjà équipés qui doivent être retirés (conflit d'emplacement).
- **Transformation du personnage** :
  - Passe son orientation en `"spent"`.
  - Met à jour sa liste `equippedItems` en retirant les anciens objets et en ajoutant le nouveau.
- **Gestion de la défausse** : Les objets remplacés sont ajoutés au tableau final avec la zone positionnée sur `discardZone`.

---

## Variables et Types importants

| Élément         | Description                                                    |
| :-------------- | :------------------------------------------------------------- |
| `allCards`      | Le tableau actuel de toutes les cartes (Joueur ou IA).         |
| `itemId`        | L'identifiant unique de la carte objet à équiper.              |
| `characterId`   | L'identifiant unique du personnage qui reçoit l'objet.         |
| `equippedItems` | Propriété de la carte personnage contenant les items attachés. |
| `orientation`   | État de la carte (`ready` pour prêt, `spent` pour utilisé).    |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `Card` & `CardZone` → Les types de base représentant les cartes et leurs emplacements.
- `canEquipItem.ts` → Utilise `getItemsToDestroy` pour savoir quels objets supprimer par collision de slot.
- `cardGuards.ts` → Utilise `isCharacter` pour s'assurer que la cible est bien un personnage avant de modifier ses équipements.

---

## Résumé

Ce fichier est responsable de :

- **Retirer l'item** de la main du joueur.
- **Marquer le personnage** comme ayant agi (`spent`).
- **Remplacer les anciens items** si les emplacements (slots) sont identiques.
- **Maintenir l'immutabilité** des données en retournant une nouvelle liste de cartes.
  """
