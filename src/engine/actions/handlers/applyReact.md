# Documentation de applyReact.ts

## Localisation

`src/engine/actions/handlers/applyReact.ts`

## Rôle du fichier

Ce fichier contient la **logique métier d'exécution** des réactions (Reacts). C'est ici que le moteur de jeu calcule si une tentative de réaction réussit ou échoue, et quelles en sont les conséquences sur l'état du jeu.

Il gère deux cas de figure :

1.  **L'exécution d'un Feat** : Le personnage tente d'activer une capacité spéciale (ex: Riposte).
2.  **Le passage de tour** (`reactPass`) : Le joueur décide de ne pas réagir.

---

## Concept : Le Skill Check (DC 20)

Toute réaction basée sur un Feat nécessite un jet de compétence.

- **Calcul** : `d20 + Skill du personnage`.
- **Difficulté (DC)** : Fixée à **20** selon les règles.
- **Résultat** : Même en cas d'échec du jet, le tour de réaction est consommé et les informations sont stockées pour l'affichage UI (`lastReactResult`).

---

## Fonctions principales

### `applyReact(state, action): GameState`

Le handler principal pour déclencher un effet de réaction.

1.  **Validation** : Vérifie la présence d'une fenêtre de réaction ouverte.
2.  **Résolution du Dé** : Utilise le dé fourni par l'action (cas de l'IA/Multijoueur) ou génère un nouveau `rollD20()`.
3.  **Application du Feat** : Appelle la méthode `.apply()` du Feat concerné (ex: `riposteFeat.ts`).
4.  **Tracking** : Enregistre l'utilisation dans `reactUsedThisTurn` pour respecter les limitations (souvent une fois par tour).
5.  **Progression** : Appelle `advanceReactWindow` pour passer au joueur suivant ou fermer la fenêtre.

### `applyReactPass(state): GameState`

Gère l'abandon de l'opportunité de réaction.

- Ajoute le joueur actuel à `passedPlayers`.
- Si tous les joueurs passent consécutivement, la fenêtre se fermera via `advanceReactWindow`.

---

## Flux de données d'une Réaction

1.  **Déclenchement** : Une action (ex: Attaque) ouvre une fenêtre.
2.  **Choix** : Le joueur sélectionne un Feat (ex: _Defend_).
3.  **Exécution** : `applyReact` calcule le total (ex: 15 au dé + 6 de Skill = 21).
4.  **Succès** : Le total 21 >= 20. L'effet du Feat est appliqué au `newState`.
5.  **Feedback** : L'interface affiche le résultat via `lastReactResult`.

---

## Variables et Types importants

| Élément           | Description                                                                                                |
| :---------------- | :--------------------------------------------------------------------------------------------------------- |
| `REACT_DC`        | La difficulté fixe de **20**.                                                                              |
| `lastReactResult` | Objet stocké dans l'état pour permettre à l'UI d'afficher "Réussite" ou "Échec" avec le détail des scores. |
| `reactsPlayed`    | Liste des identifiants `ID:Feat` utilisés pour empêcher les répétitions infinies dans la même fenêtre.     |

---

## Interaction avec d'autres fichiers

Ce handler est le point de convergence de :

- `featRegistry.ts` : Pour récupérer la logique spécifique de chaque Feat.
- `dice.ts` : Pour générer le hasard nécessaire au jeu.
- `advanceReactWindow.ts` : Pour gérer le cycle de vie de la fenêtre de réaction.
- `cardGuards.ts` : Pour s'assurer que l'entité qui réagit est bien un personnage.

---

## Résumé

Ce fichier est responsable de :

- **Transformer le hasard** (dé) en **résultat de jeu** (succès/échec).
- **Appliquer les effets** des capacités spéciales sur le plateau.
- **Informer l'utilisateur** du détail technique de sa tentative.
- **Gérer le flux** entre les différents intervenants lors d'une fenêtre de réaction.
  """
