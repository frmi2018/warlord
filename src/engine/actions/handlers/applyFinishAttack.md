# Documentation de applyFinishAttack.ts

## Localisation

`src/engine/actions/handlers/applyFinishAttack.ts`

## Rôle du fichier

Ce fichier contient le **handler de clôture du combat**. Son unique responsabilité est de marquer un personnage comme ayant terminé son action de combat en changeant son état d'orientation vers "épuisé" (`spent`).

C'est une étape de nettoyage essentielle qui garantit qu'une unité ne peut pas attaquer plusieurs fois durant le même tour (sauf règles spéciales) et que l'interface utilisateur reflète visuellement que l'unité a agi.

---

## Concept : L'épuisement des unités

Dans le système de jeu, une unité peut être dans plusieurs états (Ready, Spent, Stunned).

- **Transition** : L'attaque est une action qui consomme la disponibilité du personnage.
- **Universalité** : Le handler traite les cartes du joueur et de l'IA de la même manière. Il parcourt les deux listes pour trouver l'ID correspondant, ce qui simplifie l'appel depuis le `combatReducer`.

---

## Fonction principale

### `applyFinishAttack(state, action): GameState`

Cette fonction effectue une transformation simple et pure de l'état :

1.  **Recherche** : Elle parcourt `playerCards` et `aiCards`.
2.  **Mise à jour** : Si l'ID de la carte correspond à l' `attackerId`, elle crée une copie de la carte avec la propriété `orientation: "spent"`.
3.  **Immuabilité** : Elle retourne un nouvel objet `GameState` sans modifier l'original, en utilisant `.map()` pour garantir que seules les références des cartes modifiées changent.

---

## Variables et Types importants

| Élément            | Description                                                                                                 |
| :----------------- | :---------------------------------------------------------------------------------------------------------- |
| `attackerId`       | L'identifiant unique de la carte qui vient de terminer son attaque.                                         |
| `orientation`      | Propriété de la carte déterminant si elle peut encore agir (`ready`) ou non (`spent`).                      |
| `"spent" as const` | Assertion TypeScript pour garantir que la valeur correspond exactement au type d'union attendu dans `Card`. |

---

## Interaction avec d'autres fichiers

Ce handler est la conclusion logique du flux de combat :

- `combatReducer.ts` : Appelle ce handler lors de la réception de l'action `finishAttack`.
- `GameState.ts` : Définit la structure des listes de cartes impactées.
- `Card.ts` : Définit les états possibles de l'orientation d'un personnage.

---

## Résumé

Ce fichier est responsable de :

- **Finaliser le coût** d'une attaque en épuisant l'attaquant.
- **Garantir la cohérence visuelle** sur le plateau de jeu.
- **Empêcher les actions multiples** illégales pour une même unité.
  """
