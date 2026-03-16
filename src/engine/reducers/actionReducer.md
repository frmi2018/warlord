# Documentation de actionReducer.ts

## Localisation

`src/engine/reducers/actionReducer.ts`

## Rôle du fichier

Ce fichier est un **reducer de domaine**. Sa responsabilité est de traiter les actions qui modifient l'état du jeu durant la **Decree Phase**, à l'exception du combat (géré par un reducer dédié).

Il agit comme un chef d'orchestre qui reçoit des actions de haut niveau (joueur ou IA) et les redirige vers les fonctions de manipulation d'état appropriées (handlers).

---

## Concept : Réutilisation et Unification

L'une des forces de ce reducer est l'unification de la logique entre l'humain et l'ordinateur :

1.  **Handlers partagés** : Que ce soit un joueur humain (`equip`) ou l'IA (`aiEquip`) qui décide d'équiper un objet, la logique finale de transformation de la carte est identique.
2.  **Transformation des types** : Le reducer convertit les actions spécifiques à l'IA en types d'actions "joueur" avant de les passer aux handlers, garantissant que le moteur de jeu reste agnostique quant à la source de l'action.

---

## Actions gérées

### Manœuvre (`maneuver`)

Gère le déplacement d'un personnage d'un rang à un autre sur le plateau via `applyManeuver`.

### Équipement (`equip` / `aiEquip`)

Gère l'attachement d'un objet de la main vers un personnage en jeu via `applyEquip`.

- _Note : Pour l'IA, l'action est reformatée pour correspondre à la structure standard._

### Cartes Action (`playAction` / `aiPlayAction`)

Gère l'exécution d'une carte de sort ou d'événement jouée par un personnage (performer) via `applyPlayAction`.

---

## Variables et Types importants

| Élément               | Description                                                                        |
| :-------------------- | :--------------------------------------------------------------------------------- |
| `ActionReducerAction` | Un sous-type de `GameAction` regroupant uniquement les types gérés par ce reducer. |
| `applyManeuver`       | Handler modifiant les coordonnées `posY` et `posX` des cartes.                     |
| `applyEquip`          | Handler déplaçant l'item de la main vers la propriété `equippedItems` de la cible. |
| `applyPlayAction`     | Handler déclenchant l'effet de la carte et gérant son passage en défausse.         |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `gameState.ts` → Pour la structure de l'état à transformer.
- `gameActions.ts` → Pour la définition des types d'actions sources.
- `handlers/` → Contient la logique réelle de modification des tableaux de cartes (Immutabilité).

Il est appelé par le **Reducer Central** (`gameReducer.ts`) lorsque celui-ci détecte une action de type "Decree".

---

## Résumé

Ce fichier est responsable de :

- **Filtrer** les actions de la phase de décret.
- **Garantir** que l'IA et le Joueur suivent exactement la même logique métier.
- **Maintenir la propreté** du reducer principal en isolant la logique de gestion des cartes hors-combat.
  """
