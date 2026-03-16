# Documentation de reactTypes.ts

## Localisation

`src/engine/reacts/reactTypes.ts`

## Rôle du fichier

Ce fichier définit les **structures de données et les types fondamentaux** du système de réactions (Reacts).

Il s'assure que toutes les informations circulant dans le système de réaction sont **sérialisables** (compatibles avec une sauvegarde ou un transfert réseau), ce qui signifie qu'elles ne contiennent aucune fonction, uniquement des données brutes.

---

## Concept : Le Cycle de Vie React

Le système suit un flux précis défini par les règles du jeu :

1.  **Détection** : Une action est jouée, on vérifie si elle ouvre une fenêtre.
2.  **Initialisation** : Création d'un `ReactWindowState` dans l'état global.
3.  **Tour de rôle** : Les joueurs (Humain ou IA) choisissent de réagir ou de passer.
4.  **Résolution** : Une fois que tout le monde a passé, la fenêtre se ferme et l'action initiale (trigger) reprend ou est annulée.

---

## Types Principaux

### `ReactTiming`

Définit **quand** la réaction intervient par rapport à l'action déclencheuse :

- `"before"` : Intervient avant la résolution (ex: pour annuler ou booster une attaque).
- `"after"` : Intervient une fois l'action terminée (ex: une contre-attaque).

### `ReactSource`

Identifie l'**origine** du pouvoir utilisé pour réagir :

- `"action_card"` : Une carte jouée depuis la main.
- `"feat"` : Une capacité intrinsèque d'un personnage (ex: _Defend_).
- `"item"` : Un objet équipé possédant une capacité réactive.

### `AvailableReact`

Représente une **option de réaction** concrète affichable dans l'interface ou analysable par l'IA.

- Contient le `performerId` (qui agit), le `label` (nom affiché), et si l'action nécessite de dépenser (`requiresSpend`) le personnage.

---

## Structure de l'État : ReactWindowState

C'est l'objet qui "vit" dans le `GameState` tant qu'une fenêtre de réaction est ouverte.

| Propriété        | Description                                                            |
| :--------------- | :--------------------------------------------------------------------- |
| `triggerAction`  | L'action d'origine (IA ou Joueur) qui a déclenché la phase.            |
| `timing`         | Le timing actuel de la fenêtre (`before` ou `after`).                  |
| `currentReactor` | Désigne qui doit prendre une décision actuellement (`player` ou `ai`). |
| `passedPlayers`  | Liste des joueurs ayant choisi de ne pas réagir.                       |
| `reactsPlayed`   | Historique des IDs des réactions déjà utilisées dans cette fenêtre.    |

---

## Interaction avec d'autres fichiers

Ce fichier est une **dépendance centrale** pour :

- `gameActions.ts` → Pour le type `GameAction` lié au trigger.
- `gameState.ts` → Pour intégrer `ReactWindowState` dans l'état global.
- Tous les fichiers du dossier `reacts/` qui consomment ces types.

---

## Résumé

Ce fichier est le **socle technique** des réactions. Il :

- Standardise la communication entre le moteur et l'UI.
- Garantit la compatibilité avec la sauvegarde via des types "plats".
- Définit les règles de priorité et de timing (avant/après).
  """
