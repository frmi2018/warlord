# Documentation de rollInitiative.ts

## Localisation

`src/engine/initiative/rollInitiative.ts`

## Rôle du fichier

Ce fichier gère la mécanique de **jet d'initiative**, qui survient généralement au début de la phase de combat ou de la phase de décret. L'initiative détermine quel joueur agira en premier, un avantage stratégique crucial dans Warlord.

Il simule un duel de dés entre le joueur et l'IA et traite les cas particuliers comme les réussites critiques (20 naturels).

---

## Logique de l'Initiative

Le calcul repose sur la comparaison de deux jets de $d20$ :

### 1. Le Duel de Dés

Chaque camp génère un nombre aléatoire entre 1 et 20.

- **Réussite Critique** : Un score de **20** est considéré comme une réussite critique.
- **Priorité Critique** : Un 20 naturel bat n'importe quel autre score inférieur, même si l'adversaire possède des bonus élevés (implémentation future).

### 2. Détermination du Vainqueur

Le moteur suit cet ordre de priorité pour décider du résultat :

1.  **Critique Unique** : Si un seul camp fait 20, il gagne immédiatement.
2.  **Double Critique** : Si les deux font 20, le résultat est une égalité (`tie`).
3.  **Comparaison Standard** : Le score le plus élevé l'emporte.
4.  **Égalité Standard** : Si les scores sont identiques (et < 20), le résultat est `tie`.

---

## Structure du Résultat (`InitiativeRollResult`)

La fonction retourne un objet complet permettant à l'interface d'animer les dés et d'afficher les messages appropriés :

| Propriété                       | Type               | Description                                                     |
| :------------------------------ | :----------------- | :-------------------------------------------------------------- |
| `playerRoll` / `aiRoll`         | `number`           | La valeur brute du dé affichée à l'écran.                       |
| `winner`                        | `InitiativeWinner` | `"player"`, `"ai"`, ou `"tie"`.                                 |
| `playerCritical` / `aiCritical` | `boolean`          | Permet de déclencher des effets visuels spéciaux (ex: dé doré). |

---

## Évolutions Prévues (TODO)

Le code prévoit déjà des paramètres pour les **bonus d'initiative** (`_playerBonus`, `_aiBonus`).
À terme, ces bonus seront extraits du `GameState` en fonction :

- Des traits du **Warlord** actif.
- Des effets de certaines cartes d'action jouées au tour précédent.
- Des bonus de faction.

---

## Utilitaire de Réinitialisation

### `resetInitiative()`

Une fonction de confort qui retourne un objet d'état "vierge". Elle est utilisée par le moteur pour nettoyer les valeurs de dés de l'écran une fois que la phase d'initiative est terminée, afin de ne pas encombrer l'interface pour le tour suivant.

---

## Résumé

Ce fichier garantit que :

- Le tirage de l'ordre de jeu est **impartial**.
- Les **réussites critiques** sont valorisées conformément à l'esprit du jeu.
- L'interface dispose de toutes les données pour **animer le duel de dés**.
