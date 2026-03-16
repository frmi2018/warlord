# Documentation de selectors.ts

## Localisation

`src/engine/selectors.ts`

## Rôle du fichier

Ce fichier regroupe les **fonctions de lecture** (sélecteurs) du `GameState`. Plutôt que d'accéder manuellement aux profondeurs de l'objet d'état dans les composants React, on utilise ces fonctions pour extraire ou calculer des informations dérivées.

Il agit comme une couche d'abstraction qui garantit que la logique de recherche (ex: "Où est le Warlord ?") est écrite une seule fois et réutilisée partout.

---

## Catégories de Sélecteurs

### 1. Sélecteurs de Cartes

Ces fonctions permettent de récupérer des listes de cartes filtrées sans se soucier de la structure interne du state.

- **`getPlayerCards` / `getAiCards`** : Récupère l'intégralité du deck d'un camp.
- **`getCardsByZoneFromState`** : Extrait les cartes d'une zone précise (ex: Main, Défausse, Plateau).
  - _Optimisation_ : Le sélecteur est intelligent et ne scanne que le tableau du propriétaire de la zone (Joueur ou IA) pour gagner en performance.

### 2. Sélecteurs de Warlords

- **`getWarlord`** : Cherche l'unité possédant le trait "Warlord" dans les rangs de combat.
  - **Note critique** : Si cette fonction retourne `undefined`, cela signifie mathématiquement que le Warlord n'est plus sur le plateau (il est mort ou retiré), ce qui déclenche généralement la fin de la partie.

### 3. Sélecteurs de Flux (Turn/Phase)

- **`isPlayerTurn`** : Détermine si l'interface doit autoriser les interactions du joueur.
  - En phase de **Décret** : Vérifie l'alternance stricte entre Joueur et IA.
  - Hors phase de **Décret** : Retourne `true` par défaut pour permettre au joueur de piloter la progression des phases.

---

## Principes de Conception

| Principe          | Description                                                                                                             |
| :---------------- | :---------------------------------------------------------------------------------------------------------------------- |
| **Pureté**        | Une même entrée (State) produira toujours la même sortie. Aucun calcul aléatoire ici.                                   |
| **Lecture seule** | Ces fonctions ne modifient JAMAIS le `GameState`. Elles ne font que "lire".                                             |
| **Découplage**    | Les composants React ne savent pas comment le state est structuré ; ils demandent juste "Donne-moi les cartes en main". |

---

## Pourquoi utiliser des sélecteurs ?

1.  **Maintenance** : Si vous décidez de renommer `playerRanks` en `battlefield`, vous ne modifiez que le sélecteur, pas les 50 composants qui affichent le plateau.
2.  **Performance** : Permet d'isoler les calculs coûteux (comme le filtrage de gros tableaux) dans des fonctions optimisées.
3.  **Tests** : Il est très facile de tester une fonction de sélection pour vérifier qu'elle renvoie les bonnes cartes dans un scénario donné.

---

## Résumé

Ce fichier garantit que :

- L'accès aux données du jeu est **centralisé et sécurisé**.
- La logique métier (ex: conditions de victoire, tour de jeu) est **cohérente** dans toute l'application.
- Le code React reste **propre et focalisé** sur l'affichage uniquement.
