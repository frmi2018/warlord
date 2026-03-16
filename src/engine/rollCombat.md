# Documentation de rollCombat.ts

## Localisation

`src/engine/rollCombat.ts`

## Rôle du fichier

Ce fichier contient la **logique de résolution des combats**. Il simule le jet de dé de l'attaquant et calcule si une attaque réussit ou échoue en comparant le score total à la défense de la cible.

Il respecte les règles de la **4ème édition de Warlord**, où seul l'attaquant lance un dé (D20), tandis que le défenseur oppose une valeur de défense fixe améliorée par ses bonus passifs.

---

## Formule de Calcul

Le succès d'une attaque est déterminé par la formule suivante :

$$\text{Résultat du Dé (1-20)} + \text{ATK du Strike} + \text{Bonus Passifs} \geq \text{Classe d'Armure (AC)} + \text{Bonus de Défense}$$

### Cas Spéciaux

- **Critique (20 naturel)** : L'attaque touche toujours, peu importe la défense adverse.
- **Échec Critique / Fumble (1 naturel)** : L'attaque échoue toujours, peu importe les bonus de l'attaquant.

---

## Fonctionnement : `rollCombat()`

Cette fonction est **pure** : elle reçoit les données, effectue le calcul et renvoie un rapport détaillé sans modifier les cartes en mémoire.

1.  **Génération du Dé** : Un nombre aléatoire entre 1 et 20 est généré.
2.  **Calcul de l'Attaque** :
    - Récupère la valeur d'attaque correspondant au strike actuel (ex: 1ère ou 2ème attaque de l'unité).
    - Ajoute les bonus provenant des effets passifs (compétences, adjacence, objets).
3.  **Calcul de la Défense** :
    - Récupère la `armorClass` de base de la cible.
    - Ajoute les bonus passifs de défense de la cible.
4.  **Verdict** : Compare les deux totaux et détermine le nombre de blessures infligées (généralement 1 si l'attaque touche).

---

## Le type `CombatRollResult`

La fonction retourne un objet riche en informations, utilisé par l'interface pour afficher le détail du jet de dé au joueur :

| Propriété                 | Type      | Description                              |
| :------------------------ | :-------- | :--------------------------------------- |
| `attackerRoll`            | `number`  | La valeur brute du dé (1-20).            |
| `attackerTotal`           | `number`  | Le dé + tous les bonus d'attaque.        |
| `defenderAC`              | `number`  | Le seuil final à atteindre pour toucher. |
| `hits`                    | `boolean` | `true` si l'attaque est réussie.         |
| `isCritical` / `isFumble` | `boolean` | Indique si un 20 ou un 1 a été obtenu.   |

---

## Pourquoi cette architecture ?

- **Transparence** : En renvoyant le détail (`attackerBonus`, `defenderAC`), l'interface peut expliquer au joueur _pourquoi_ il a touché ou raté (ex: "Il vous manquait 2 points pour toucher").
- **Découplage** : Le calcul du combat est séparé de l'application des dégâts. Cela permet de simuler des combats ou de prévoir des probabilités sans modifier l'état réel de la partie.
- **Support des Effets Passifs** : La fonction prend en compte tout l'environnement du plateau (`alliedCards`, `enemyCards`) pour intégrer les synergies de groupe dans le calcul.

---

## Résumé

Ce fichier garantit que :

- Les **règles de combat** sont appliquées avec précision.
- Le hasard est géré de manière **équitable** via un D20 standard.
- L'interface utilisateur dispose de toutes les données pour créer un **log de combat** détaillé.
