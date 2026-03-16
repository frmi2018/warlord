# Documentation de closeReactWindow.ts

## Localisation

`src/engine/reacts/closeReactWindow.ts`

## Rôle du fichier

Ce fichier gère **la progression et la fermeture de la fenêtre de
réaction (React Window)** dans le moteur du jeu.

Dans ce système, lorsqu'une action est jouée, les joueurs peuvent avoir
l'opportunité de **réagir** (React).\
La fenêtre reste ouverte tant que les joueurs peuvent répondre.

Elle se ferme lorsque : - les deux joueurs ont **passé
consécutivement** - ou lorsque l'action déclencheuse (**trigger**)
devient **invalide**.

---

## Concept : React Window

Une **React Window** est une phase temporaire où les joueurs peuvent
jouer des réactions à une action.

Exemple de flow :

1.  Un joueur joue une action (trigger).
2.  Une fenêtre React s'ouvre.
3.  Les joueurs peuvent répondre chacun leur tour.
4.  Si les deux joueurs passent → la fenêtre se ferme.
5.  L'action initiale continue normalement.

---

## Fonction principale

### `advanceReactWindow(state: GameState): GameState`

Cette fonction fait avancer la fenêtre React au joueur suivant.

Elle gère trois cas :

1.  **Validation du trigger**
    - Si une action a été annulée (`canceledActionId`)
    - On vérifie si le trigger est toujours valide avec
      `isTriggerStillValid()`.
    - Si ce n'est plus le cas → la fenêtre se ferme immédiatement.
2.  **Passage au joueur suivant**
    - On cherche le prochain joueur qui n'a pas encore passé.
    - Si trouvé → il devient le `currentReactor`.
3.  **Fermeture de la fenêtre**
    - Si aucun joueur ne peut encore réagir → la fenêtre se ferme.

---

## Fonction interne

### `closeReactWindow(state: GameState)`

Ferme la fenêtre React **normalement** lorsque les deux joueurs ont
passé.

Comportement : - `reactWindow` devient `null` - Si le timing est
`"before"` : - l'action déclencheuse est copiée dans
`pendingTriggerAction` - elle sera exécutée juste après par le
dispatcher

Cela permet de **retarder l'exécution d'une action pendant la phase de
réaction**.

---

### `closeReactWindowCanceled(state: GameState)`

Ferme la fenêtre React lorsqu'un **React a invalidé l'action trigger**.

Dans ce cas : - la fenêtre est fermée - `pendingTriggerAction` reste
`null` - l'action déclencheuse **ne sera jamais exécutée**

---

## Variables importantes

---

Élément Description

---

`reactWindow` Contient les informations sur la fenêtre React
active

`currentReactor` Joueur qui peut réagir actuellement

`passedPlayers` Liste des joueurs ayant passé

`triggerAction` Action qui a déclenché la fenêtre React

`pendingTriggerAction` Action en attente d'exécution après la
fermeture de la fenêtre

`canceledActionId` Identifiant d'une action annulée par un React

---

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `GameState` → structure globale de l'état du jeu
- `GameAction` → type représentant une action de jeu
- `triggerValidator.ts` → vérifie si le trigger est toujours valide

---

## Résumé

Ce fichier est responsable de :

- gérer **le tour de réaction des joueurs**
- détecter quand **plus personne ne réagit**
- fermer la **fenêtre React**
- décider si **l'action déclencheuse doit être exécutée ou annulée**

Il fait donc partie du **moteur central de gestion des réactions du
jeu**.
