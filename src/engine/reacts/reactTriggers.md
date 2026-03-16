# Documentation de reactTriggers.ts

## Localisation

`src/engine/reacts/reactTriggers.ts`

## Rôle du fichier

Ce fichier est responsable de la **détection et de l'initialisation des fenêtres de réaction**.

Lorsqu'une action est tentée (par un joueur ou par l'IA), ce module détermine si cette action autorise une pause dans le jeu pour permettre aux joueurs de répondre (React). Si c'est le cas, il prépare la structure de données nécessaire pour gérer cette phase.

---

## Concept : Déclenchement de Fenêtre (Trigger)

Une fenêtre de réaction ne s'ouvre pas systématiquement. Elle suit des règles précises :

1.  **Existence de réponses** : Une fenêtre s'ouvre uniquement si au moins un des deux joueurs possède une carte ou une capacité utilisable en réponse à l'action actuelle. Si personne ne peut réagir, le jeu continue sans interruption.
2.  **Initiative** : Le système utilise l'initiative définie dans l'état du jeu pour décider quel joueur a la première opportunité de réagir.
3.  **Universalité** : Le système traite les actions de l'humain et de l'IA de la même manière ; une attaque de l'IA peut déclencher une réaction humaine, et vice versa.

---

## Fonctions principales

### `opensReactWindow(action, state, timing): boolean`

Détermine si une fenêtre de réaction doit être créée.

- **Vérification d'état** : S'assure qu'une fenêtre n'est pas déjà active.
- **Calcul des possibilités** : Appelle `getAvailableReacts` pour les deux camps (Joueur et IA).
- **Optimisation** : Retourne `true` uniquement si la liste des réactions disponibles n'est pas vide pour l'un des deux joueurs.

### `buildReactWindow(triggerAction, timing, state): ReactWindowState`

Initialise l'objet d'état de la fenêtre React.

- **Détermination du premier reactor** : Attribue le tour de réaction à l'IA si elle a gagné l'initiative, sinon au joueur humain par défaut.
- **Initialisation** : Prépare les listes vides pour les joueurs ayant passé (`passedPlayers`) et les réactions déjà jouées (`reactsPlayed`).

---

## Variables et Types importants

| Élément            | Description                                                                                    |
| :----------------- | :--------------------------------------------------------------------------------------------- |
| `GameAction`       | L'action (attaque, sort, capacité) qui tente de déclencher la fenêtre.                         |
| `ReactTiming`      | Le moment de la réaction (généralement `"before"` pour tenter d'annuler ou modifier l'action). |
| `ReactWindowState` | La structure de données stockée dans le `GameState` pendant toute la durée de la fenêtre.      |
| `initiativeWinner` | Variable issue du `state` déterminant qui parle en premier.                                    |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `getAvailableReacts.ts` → Pour savoir si des réactions sont techniquement possibles à ce moment précis.
- `gameActions.ts` & `gameState.ts` → Pour les structures de données fondamentales du moteur.
- `reactTypes.ts` → Pour les définitions des types liés au système de réaction.

---

## Résumé

Ce fichier est responsable de :

- **Filtrer les interruptions** inutiles pour fluidifier le gameplay.
- **Analyser les mains et capacités** des joueurs pour voir si une réponse est possible.
- **Désigner le premier joueur** à s'exprimer selon les règles d'initiative.
- **Créer le contexte** que le reste du moteur utilisera pour gérer le tour par tour des réactions.
  """
