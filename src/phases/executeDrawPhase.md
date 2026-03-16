# Documentation de executeDrawPhase.ts

## Localisation

`src/engine/phases/executeDrawPhase.ts`

## Rôle du fichier

Ce fichier contient la logique métier de la **Draw Phase** (Phase de Pioche). C'est la première étape de chaque tour de jeu. Sa mission est double : gérer le renouvellement de la main (mulligan partiel) et assurer que chaque joueur dispose des ressources nécessaires pour le tour à venir.

Le fichier expose une version générique pour les joueurs et une version spécifique pour l'intelligence artificielle.

---

## Logique de la Phase de Pioche

Le processus suit deux étapes strictes pour garantir l'équité :

### 1. La Défausse Volontaire

Avant de piocher, le joueur peut choisir de se débarrasser de cartes inutiles de sa main.

- **Action** : Les cartes sélectionnées sont déplacées vers la zone `playerDiscard` ou `aiDiscard`.
- **Note** : En format Warlord, cette étape permet de "cycler" son deck pour chercher des solutions spécifiques.

### 2. Le Remplissage de la Main

Le moteur vérifie le nombre de cartes restantes en main après la défausse.

- **Limite** : La taille de main standard (`STANDARD_HAND_SIZE`) est fixée à **5 cartes**.
- **Mécanique** : Le joueur pioche les $X$ premières cartes de son deck pour compléter sa main jusqu'à 5.
- **Sécurité** : Si le deck est vide, le joueur ne pioche simplement rien (pas de défaite automatique par "deck-out" à cette étape).

---

## Fonctions Exportées

### `executeDrawPhase()`

Fonction principale utilisée par le moteur de jeu.

- **Paramètres** :
  - `cards` : Le tableau complet des cartes du joueur concerné.
  - `owner` : Identifie s'il s'agit du `"player"` ou de l' `"ia"`.
  - `cardsToDiscard` : Liste optionnelle d'IDs à défausser.
- **Retour** : Un nouveau tableau de cartes (`Card[]`) avec les zones mises à jour (approche immuable).

### `executeDrawPhaseAI()`

Version simplifiée pour l'adversaire informatique.

- **Stratégie actuelle** : L'IA ne défausse aucune carte et se contente de compléter sa main.
- **Évolutivité** : C'est ici que sera injectée la logique décisionnelle future (ex: défausser les cartes de niveau trop élevé).

---

## Pourquoi cette approche ?

| Avantage           | Description                                                                                                                                             |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Immuabilité**    | La fonction ne modifie pas le tableau original mais en retourne un nouveau, ce qui facilite le "Undo" et le suivi de l'état par React.                  |
| **Centralisation** | Les noms des zones (`playerHand`, `aiDeck`, etc.) sont résolus dynamiquement, évitant de dupliquer la logique pour chaque camp.                         |
| **Robustesse**     | L'utilisation de `Math.min` et de vérifications de longueur empêche le moteur de tenter de piocher des cartes inexistantes, évitant les crashs système. |

---

## Résumé

Ce fichier garantit que :

- Les joueurs commencent chaque tour avec une **main complète de 5 cartes**.
- Le **mélange du deck** est respecté (pioche dans l'ordre du tableau).
- L'**IA** dispose d'une base de pioche automatique simple mais extensible.
