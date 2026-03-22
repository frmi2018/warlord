# Documentation de itemAbilityReducer.ts

## Localisation

`src/engine/reducers/itemAbilityReducer.ts`

## Rôle du fichier

Le `itemAbilityReducer` est un sous-reducer spécialisé qui gère les **effets mécaniques déclenchés par les objets équipés**. Actuellement, sa responsabilité principale est de traiter la logique de "Frappe à Distance" (Ranged Strike).

Contrairement au combat de mêlée standard, ce reducer intervient pour marquer l'état du porteur de l'objet avant que la résolution du jet de dés ne soit effectuée par le système de combat global.

---

## Fonctionnement : Application de la Frappe à Distance

La fonction `applyItemRangedStrike` suit une logique stricte pour garantir l'intégrité du jeu :

- **Validation de la Cible** : Le `targetId` est obligatoire. Le reducer part du principe que la sélection a déjà été validée par le hook `useRangedStrikeFromItem` (UI).
- **Vérification de l'État de l'Attaquant** : Il vérifie que le porteur de l'objet existe toujours sur le plateau et qu'il n'est pas déjà dans l'état `spent` (épuisé).
- **Mutation de l'État** : Sa seule action directe sur le `GameState` est de basculer l'orientation du porteur de `ready` vers `spent`.

---

## Architecture et Flux de Résolution

Une particularité importante de ce fichier est ce qu'il **ne fait pas** :

1.  **Pas de Jet de Dés** : Le calcul des dégâts et le jet de dés ne sont pas gérés ici. Ils sont délégués au `CombatRollModal` côté interface, puis résolus par le `combatReducer` standard via l'action `resolveAttack`.
2.  **Séparation des Préoccupations** : En isolant le passage à l'état "épuisé" dans ce reducer, le moteur s'assure qu'un joueur ne peut pas déclencher plusieurs fois la capacité d'un objet durant le même tour, même si le processus de jet de dés est en cours.

---

## Historique des Corrections (Bugs résolus)

Ce fichier a été optimisé pour éliminer deux problèmes critiques :

- **Persistance des Bonus** : Suppression des champs temporaires qui "fuitaient" et appliquaient par erreur des bonus de distance aux attaques de mêlée suivantes.
- **Sélection Silencieuse** : Suppression des branches de code qui tentaient de gérer la sélection de cible à l'intérieur du reducer, une tâche désormais intégralement gérée par les hooks React pour une meilleure réactivité de l'UI.

---

## Résumé

Le `itemAbilityReducer` est responsable de :

- **Consommer l'action de l'unité** (passage en `spent`) lors de l'utilisation d'une capacité d'objet.
- **Garantir que les conditions d'utilisation** de l'objet sont remplies avant de modifier l'état.
- **Servir de pont** entre une intention d'action d'objet et le système de résolution de combat universel.
