# Documentation de validateAction.ts

## Localisation

`src/engine/actions/validateAction.ts`

## Rôle du fichier

Ce fichier est le **garde-fou central** pour toutes les actions initiées par le joueur.

Avant qu'une action ne soit traitée par le moteur (reducer), elle passe par `validateAction` pour vérifier si elle respecte les règles du jeu (est-ce le bon tour ? la phase est-elle correcte ? la cible est-elle valide ?). Cela permet de renvoyer un message d'erreur clair à l'utilisateur si l'action est impossible.

---

## Concept : Validation des Actions

Le moteur de jeu suit un cycle de validation strict :

1.  **Phase & Tour** : La plupart des actions (attaque, équipement) ne sont autorisées que durant la **Decree Phase** du joueur actif.
2.  **État de la carte** : Vérifie si la carte est dans la bonne zone (main ou rang) et si son orientation permet l'action (non "spent" ou non "stunned").
3.  **Fenêtres spécifiques** : Les actions de réaction (`react`, `reactPass`) ne sont validées que si une fenêtre de réaction est active et que c'est au joueur de s'exprimer.

---

## Fonction principale

### `validateAction(action, state): ValidationResult`

C'est le dispatcher de validation. Elle reçoit une action et l'état actuel, puis délègue la vérification à une fonction spécialisée selon le type d'action.

**Résultat retourné :** Un objet `{ valid: boolean, reason?: string }`.

---

## Fonctions de validation spécifiques (extraits)

- **validateSpendAttacker** : Vérifie la portée, l'état de l'attaquant et de la cible. Gère le cas spécial de la **Riposte** (qui ne nécessite pas d'être "ready").
- **validateDeploy** : Utilise `canDeploy` pour vérifier si un personnage peut entrer en jeu sur le rang demandé.
- **validateManeuver** : Vérifie si la destination du mouvement est adjacente et libre via `getManeuverTargets`.
- **validateEquip** : Délègue à `canEquipItem` pour vérifier les restrictions de niveau et de classe.
- **validateReact / validateReactPass** : S'assure qu'un joueur ne tente pas de réagir en dehors d'une fenêtre de réaction légitime.

---

## Variables et Types importants

| Élément            | Description                                                                       |
| :----------------- | :-------------------------------------------------------------------------------- |
| `ValidationResult` | Interface standard pour le retour (succès ou échec avec message).                 |
| `isRiposte`        | Drapeau permettant de bypasser la vérification "ready" lors d'une contre-attaque. |
| `activePlayer`     | Vérifie si c'est bien le joueur humain qui tente l'action.                        |

---

## Interaction avec d'autres fichiers

Ce fichier est le **carrefour des règles**, il importe :

- `combatRules.ts` & `canEquipItem.ts` : Pour les règles métier complexes.
- `deployCharacter.ts` & `getManeuverTargets.ts` : Pour la logique de placement sur le plateau.
- `playerActions.ts` : Pour la définition des types d'actions reçues.

---

## Résumé

Ce fichier est responsable de :

- **Interdire** les coups illégaux.
- **Expliquer** pourquoi une action est refusée (messages d'erreur pour l'UI).
- **Centraliser** tous les pré-requis avant l'exécution d'un changement d'état.
  """
