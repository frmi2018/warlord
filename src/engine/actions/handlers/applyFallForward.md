# Documentation de applyFallForward.ts

## Localisation

`src/engine/actions/handlers/applyFallForward.ts`

## Rôle du fichier

Ce fichier contient la logique du **"Fall Forward"** (Avancée automatique). Il gère le déplacement d'une unité vers le rang situé immédiatement devant elle lorsqu'un vide a été créé (par exemple, après la mort d'un allié au premier rang).

Comme pour les autres handlers, son rôle est d'extraire cette logique des composants React pour l'intégrer proprement dans le pipeline **`applyAction`**.

---

## Concept : Le Fall Forward

Dans les règles du jeu, une armée ne peut pas laisser de "trous" entre ses rangs. Si le rang 1 devient vide alors que des unités sont au rang 2, ces dernières doivent avancer.

---

## Fonctionnement de l'Action

### `applyFallForward(state, action)`

La fonction est capable de traiter aussi bien les unités du joueur que celles de l'IA :

1.  **Vérification du propriétaire (`owner`)** :
    - Si `owner === "player"`, elle travaille sur le tableau `playerCards`.
    - Sinon, elle travaille sur `aiCards`.
2.  **Délégation** : Elle appelle la fonction utilitaire `fallForward` (située dans `utils/`).
3.  **Mise à jour** : Elle retourne une copie de l'état avec la position de la carte mise à jour dans le tableau correspondant.

---

## Variables et Paramètres de l'Action

| Paramètre | Type               | Description                                        |
| :-------- | :----------------- | :------------------------------------------------- |
| `cardId`  | `string`           | L'identifiant de la carte qui doit avancer.        |
| `owner`   | `"player" \| "ai"` | Détermine quel camp est concerné par le mouvement. |

---

## Interaction avec d'autres fichiers

- **`fallForward.ts` (Utils)** : Contient la logique de calcul de la nouvelle position `posY`. Elle s'assure que la carte diminue son index de rang (ex: passe du rang 2 au rang 1).
- **`useIllegalRanks` (Hook)** : C'est généralement ce hook qui détecte un rang illégal et déclenche cette action pour corriger la formation.
- **`applyAction.ts`** : Le point d'entrée central qui route l'action vers ce handler.

---

## Résumé

Ce fichier garantit que :

- La **cohésion des rangs** est maintenue de manière automatique.
- Le mouvement d'avancée est traité comme une **action officielle** du moteur, permettant un suivi précis de l'état du plateau.
- Le code est **symétrique**, gérant de la même manière le joueur et l'adversaire IA.
