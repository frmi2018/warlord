# Documentation de combatReducer.ts

## Localisation

`src/engine/reducers/combatReducer.ts`

## Rôle du fichier

Ce fichier est le **moteur de résolution des combats**. Il s'agit d'un reducer de domaine spécialisé qui traite toutes les étapes d'une attaque, de sa déclaration à sa finalisation, pour le joueur humain comme pour l'IA.

Il assure la transition entre l'intention d'attaquer (dépense de l'unité) et la résolution effective des dégâts, tout en gérant les interruptions possibles (comme les annulations d'attaques).

---

## Concept : Le Cycle de Combat

Le combat dans ce moteur suit un flux précis géré par ce reducer :

1.  **Déclaration** (`spendAttacker` / `aiAttack`) : L'attaquant est identifié, ses conditions de ressources sont vérifiées et il est potentiellement marqué comme "épuisé" (spent).
2.  **Résolution** (`resolveAttack`) : Calcul des dégâts et application des blessures sur la cible.
3.  **Annulation** (`cancelAttack`) : Si un effet (comme _Stealth_) annule l'attaque, ce cas remet l'attaquant dans son état initial ("ready").
4.  **Clôture** (`finishAttack`) : Marque définitivement la fin de la séquence de combat pour une unité.

---

## Fonctions principales

### `combatReducer(state, action): GameState`

Le dispatcher principal du domaine de combat. Il oriente l'action vers le handler approprié.

### `applySpendAiAttacker(state, action)`

Gère spécifiquement les attaques de l'Intelligence Artificielle.

- **Déterminisme** : Si l'action contient un `result` pré-calculé (généré par l'IA en amont), le reducer l'applique directement. Cela garantit que le lancer de dés de l'IA ne change pas si on recharge l'état.
- **Fallback** : Si aucun résultat n'est embarqué, il se contente de gérer l'orientation (épuisement) de l'unité IA.

### `applyFinishAttack(state, action)`

Handler simple qui s'assure qu'à la fin de sa séquence de combat, l'attaquant (joueur ou IA) passe en état `"spent"`.

---

## Gestion de l'IA vs Joueur

| Action          | Source | Particularité                                                                  |
| :-------------- | :----- | :----------------------------------------------------------------------------- |
| `spendAttacker` | Joueur | Appelle `applySpendAttacker` (logique standard).                               |
| `aiAttack`      | IA     | Utilise des données **pré-calculées** pour éviter l'aléatoire dans le reducer. |
| `resolveAttack` | Joueur | Déclenche le calcul des "hits" et des blessures.                               |

---

## Variables et Types importants

| Élément                                 | Description                                                                         |
| :-------------------------------------- | :---------------------------------------------------------------------------------- |
| `CombatAction`                          | Union des types d'actions strictement liés au combat.                               |
| `isCharacter`                           | Type Guard pour s'assurer qu'on ne tente pas de faire attaquer un objet ou un sort. |
| `updatedAiCards` / `updatedPlayerCards` | Tableaux de cartes modifiés fournis par l'IA lors d'une attaque.                    |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `gameActions.ts` → Pour la structure des actions de combat.
- `handlers/applySpendAttacker.ts` & `applyResolveAttack.ts` → Pour la logique métier détaillée.
- `card.ts` → Pour manipuler les propriétés `orientation` et `wounds`.

---

## Résumé

Ce fichier est responsable de :

- **Transformer l'état** du plateau suite à une agression.
- **Gérer l'épuisement** des unités après leur combat.
- **Assurer la synchronisation** entre les calculs de l'IA et l'état global du jeu.
- **Permettre l'annulation** propre d'une attaque en cours de route.
  """
