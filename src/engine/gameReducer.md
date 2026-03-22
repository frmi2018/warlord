# Documentation de gameReducer.ts

## Localisation

`src/engine/gameReducer.ts`

## Rôle du fichier

Le `gameReducer` est le **point d'entrée unique** pour toutes les modifications de l'état global du jeu (`GameState`). Son rôle est purement architectural : il reçoit une action et la route vers le sous-reducer spécialisé (combat, déploiement, actions, etc.).

---

## Fonctionnement : Pipeline de Dispatch

Ce fichier implémente le pattern "Root Reducer" :

- **Routage par Domaine** : Au lieu d'avoir une logique massive, il délègue à des fichiers comme `combatReducer` ou `reactReducer`.
- **Testabilité** : Le reducer est une fonction pure, ce qui signifie qu'il est 100% testable de manière isolée, sans dépendance à l'interface React.
- **Support Multi-Source** : Il traite de la même manière les actions venant du joueur humain et celles venant de l'IA (ex: `aiDeploy` vs `deploy`).

---

## Responsabilités

- **Combat** : Redirige vers `combatReducer` pour les attaques et résolutions.
- **Capacités d'Items** : Gère les effets actifs comme `ITEM_RANGED_STRIKE`.
- **Phases et Système** : Gère les transitions de phase et les corrections automatiques du plateau (`fallForward`).
- **Fenêtres de Réaction** : Centralise le flux des "Reacts" via le `reactReducer`.

---

## Résumé

Le `gameReducer` est responsable de :

- **Garantir l'intégrité de l'état** en centralisant les mutations.
- **Distribuer la logique métier** vers les modules appropriés.
- **Assurer la cohérence** entre les actions joueurs et IA.
