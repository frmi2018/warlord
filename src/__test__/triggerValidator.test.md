# Documentation de triggerValidator.test.ts

## Localisation

`src/__tests__/triggerValidator.test.ts`

## Rôle du fichier

Ce fichier contient les **tests unitaires** vérifiant la logique de validation des actions déclencheuses (**triggers**) lors d'une fenêtre de réaction.

Il garantit que le moteur de jeu détecte correctement si une action (comme une attaque ou un sort) doit être annulée suite à l'utilisation d'une capacité de réaction (ex: _Stealth_ ou _Magic Resistance_).

---

## Concept : Validation de Trigger

Lorsqu'une **React Window** est ouverte, les joueurs peuvent jouer des cartes ou des capacités. Certaines de ces réactions ont pour but d'invalider l'action initiale.

Le système de test vérifie deux aspects critiques :

1. **Intégrité des entités** : L'attaquant ou la cible sont-ils toujours en jeu ?
2. **Annulation explicite** : L'ID de l'action figure-t-il dans `canceledActionId` ?

---

## Fonctions testées

### `isTriggerStillValid(state, window)`

Vérifie si l'action qui a ouvert la fenêtre est toujours exécutable.

- **Actions de combat (`spendAttacker`)** : Invalide si l'attaquant est éliminé ou si l'action est marquée comme annulée.
- **Actions de sorts (`playAction`)** : Invalide si la carte jouée a été contrée (ex: par une résistance magique).
- **Gestion du Timing** : Vérifie que le timing `"before"` est plus permissif que le timing `"after"` (la cible n'a pas besoin d'être valide en "before" car elle n'est pas encore impactée).

### `advanceReactWindow(state)`

Teste l'intégration du validateur dans le flux de progression de la fenêtre.

- **Fermeture forcée** : Si le trigger devient invalide, la fenêtre doit se fermer immédiatement.
- **Nettoyage** : Vérifie que `canceledActionId` est bien remis à `null` après l'annulation pour ne pas polluer les actions futures.
- **Flux normal** : Vérifie que si le trigger est valide, le tour passe bien au joueur suivant.

---

## Cas de tests principaux (Scénarios)

| Catégorie         | Scénario de test                                             | Résultat attendu                          |
| :---------------- | :----------------------------------------------------------- | :---------------------------------------- |
| **Stealth**       | Un attaquant voit son action annulée via `canceledActionId`. | Trigger Invalide                          |
| **Élimination**   | L'attaquant est envoyé en défausse par une réaction.         | Trigger Invalide                          |
| **Résistance**    | Un sort est joué mais l'ID de la carte est annulé.           | Trigger Invalide                          |
| **Double Pass**   | Les deux joueurs passent alors que le trigger est valide.    | Fermeture normale (exécution du trigger)  |
| **Timing Before** | La cible meurt mais l'action est en "before".                | Trigger Valide (l'attaque peut continuer) |

---

## Interaction avec d'autres fichiers

Ce fichier de test manipule et valide les interactions entre :

- `triggerValidator.ts` → Le code source de la logique de validation.
- `closeReactWindow.ts` → Le moteur de gestion de la fenêtre.
- `makeTestState` → Helper permettant de simuler des états de jeu complexes.

---

## Résumé

Ce fichier de test est le garde-fou du **système d'annulation du jeu**. Il assure que :

- Les réactions d'annulation (contres, esquives) fonctionnent techniquement.
- Le moteur ne tente pas d'exécuter une action dont les conditions ne sont plus remplies.
- L'état global du jeu reste propre après une annulation.
