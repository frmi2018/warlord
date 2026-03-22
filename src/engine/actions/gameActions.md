# Documentation de gameActions.ts

## Localisation

`src/engine/actions/gameActions.ts`

## Rôle du fichier

Ce fichier définit l'**Union Type `GameAction`**, qui est le dictionnaire de toutes les commandes possibles au sein du moteur de jeu. C'est le contrat qui lie l'interface utilisateur (UI) au moteur (Reducer).

---

## Architecture des Actions

Le fichier sépare les intentions en plusieurs catégories :

- **PlayerAction** : Actions directes de l'utilisateur (attaquer, passer, etc.).
- **AiAction** : Actions générées par les algorithmes de l'IA.
- **SystemAction** : Actions de maintenance (ex: `fallForward` pour réajuster les rangs).
- **ItemExecuteResult** : Définit les types d'intentions qu'un objet peut retourner (ex: une frappe à distance).

---

## Concepts Clés : Intentions Pures

Une règle d'or du moteur est que l'exécution d'une capacité d'item (`execute()`) ne modifie jamais l'état directement. Elle retourne un `ItemExecuteResult` (une intention), qui est ensuite dispatché sous forme de `GameAction`.

### Exemple de flux :

1. L'item retourne `{ actionType: "ITEM_RANGED_STRIKE", bonus: 1 }`.
2. Le dispatcher transforme cela en une action de type `ITEM_RANGED_STRIKE`.
3. Le `gameReducer` applique enfin la modification au `GameState`.

---

## Résumé

Le `gameActions` est responsable de :

- **Définir le langage commun** entre l'UI et le moteur.
- **Catégoriser les sources d'actions** (Joueur vs IA vs Système).
- **Typage strict** pour éviter les erreurs lors de l'envoi de données au reducer.
