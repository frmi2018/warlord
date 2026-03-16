# Documentation de useActionDispatcher.test.ts

## Localisation

`src/__tests__/useActionDispatcher.test.ts`

## Rôle du fichier

Ce fichier contient les **tests unitaires** pour la logique de résolution des actions en attente (**pending actions**).

Il vérifie que lorsqu'une action a été mise "en pause" (par exemple pour laisser place à une fenêtre de réaction), elle est correctement exécutée une fois la fenêtre fermée. Ce fichier se concentre sur la fonction `resolvePending` extraite du dispatcher pour garantir la stabilité du moteur de jeu.

---

## Concept : Résolution de l'Action Pending

Lorsqu'une action (comme attaquer) est déclenchée, le moteur vérifie s'il existe des réactions possibles.

1. L'action initiale est placée dans `pendingTriggerAction`.
2. Une `reactWindow` s'ouvre.
3. Une fois la fenêtre fermée, `resolvePending` est appelée pour **consommer** l'action en attente et l'appliquer réellement à l'état du jeu.

---

## Fonctions de test

### `resolvePending`

Les tests couvrent les scénarios critiques de l'exécution d'une action après une phase de réaction :

- **Respect de la fenêtre ouverte** : Vérifie que si une `reactWindow` est encore active, l'action en attente ne bouge pas (on ne court-circuite pas les réactions).
- **Gestion du "Pass"** : Vérifie que si l'action en attente est un "Pass", le compteur de passes consécutives du jeu augmente bien.
- **Exécution d'Action (Ex: spendAttacker)** : S'assure qu'une action de combat (dépenser un attaquant) modifie correctement les propriétés de la carte (passage de l'état `ready` à `spent`).
- **Anti-récursion** : C'est un test crucial. Il vérifie que l'exécution d'une action qui vient de sortir de la file d'attente ne ré-ouvre pas une deuxième fenêtre de réaction identique (boucle infinie).

---

## Helpers

### `makeCharacter(overrides)`

Une fonction utilitaire qui génère un objet `CharacterCard` (Personnage) avec des valeurs par défaut. Elle permet de créer rapidement des attaquants ou des cibles pour les besoins des tests sans redéfinir toutes les propriétés à chaque fois.

---

## Variables et États testés

| Élément                | Description                                                   |
| :--------------------- | :------------------------------------------------------------ |
| `pendingTriggerAction` | L'action "en pause" qui doit être résolue.                    |
| `reactWindow`          | Si elle est `null`, la résolution peut commencer.             |
| `consecutivePasses`    | Compteur de passes, utilisé pour tester la fin de tour.       |
| `orientation`          | L'état de la carte (ex: `ready` ou `spent`) après résolution. |

---

## Interaction avec d'autres fichiers

Ce fichier de test interagit avec :

- `pendingResolver.ts` → Le fichier contenant la logique métier testée ici.
- `gameState.ts` & `card.ts` → Pour les définitions de types.
- `makeTestState.ts` → Un helper pour créer des états de jeu factices (mocks).

---

## Résumé

Ce fichier de test garantit que :

- Les actions ne sont exécutées que **lorsque la phase de réaction est terminée**.
- Le passage d'une action de "en attente" à "exécutée" **modifie correctement l'état du jeu**.
- Le système évite les **boucles infinies** de réactions (le "gel" permanent d'une action).
