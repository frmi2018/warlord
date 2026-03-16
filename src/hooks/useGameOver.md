# Documentation de useGameOver.ts

## Localisation

`src/hooks/useGameOver.ts`

## Rôle du fichier

Ce hook React est le **moteur de détection de fin de partie**. Il surveille en permanence l'état du plateau pour vérifier si les conditions de victoire ou de défaite sont remplies.

Il applique la règle fondamentale de Warlord : la partie s'arrête instantanément dès qu'un joueur ne contrôle plus de personnage "Warlord" sur le champ de bataille.

---

## Logique de Détection

### La notion de "Warlord Vivant"

Une unité est considérée comme un Warlord actif si elle remplit deux conditions simultanées :

1.  **Trait** : Elle possède le mot-clé `"Warlord"` dans sa liste de traits.
2.  **Zone** : Elle se trouve physiquement dans la zone de combat (`playerRanks` ou `aiRanks`).

Si le Warlord est déplacé dans la défausse (`Discard`) ou retiré du jeu, il n'est plus considéré comme "vivant" par ce hook.

---

## Fonctionnement du Hook

Le hook `useGameOver` prend en entrée les listes de cartes du joueur et de l'IA, puis calcule le résultat via `useMemo` pour optimiser les performances (le calcul ne se déclenche que si une carte change de position ou d'état).

### Résultats possibles (`GameResult`)

| Valeur          | Condition                                             |
| :-------------- | :---------------------------------------------------- |
| `"player_wins"` | Le Warlord de l'IA a disparu du plateau.              |
| `"ai_wins"`     | Le Warlord du joueur a disparu du plateau.            |
| `null`          | Les deux Warlords sont présents : la partie continue. |

---

## Architecture et Performance

- **Optimisation `useMemo`** : Étant donné que ce hook est appelé à chaque rendu du plateau, l'utilisation de `useMemo` garantit que nous ne parcourons pas les tableaux de cartes inutilement si aucune action n'a eu lieu.
- **Priorité de Victoire** : Le code vérifie d'abord si l'IA a perdu. Dans le cas extrêmement rare où les deux Warlords mourraient simultanément (ex: via une capacité de zone), le code actuel donnerait la priorité à la victoire du joueur.

---

## Pourquoi un Hook séparé ?

1.  **Réactivité UI** : Permet à l'interface d'afficher instantanément un écran de victoire ou de défaite ("Game Over Overlay") sans attendre la fin d'une animation.
2.  **Découplage** : La logique de fin de partie est isolée du reste du moteur de combat, facilitant les tests unitaires sur les scénarios de victoire.
3.  **Simplicité** : Les composants de haut niveau (comme `GamePage`) n'ont qu'à lire la valeur de ce hook pour décider s'ils doivent bloquer les interactions.

---

## Résumé

Ce fichier garantit que :

- La **condition de victoire** est universelle et incontestable.
- La fin de partie est détectée **en temps réel**.
- Le moteur sait exactement **qui a gagné** dès qu'un Warlord quitte les rangs.
