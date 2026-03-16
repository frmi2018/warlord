# Documentation de GameModals.tsx

## Localisation

`src/components/GameModals.tsx`

## Rôle du fichier

Le `GameModals` est le **gestionnaire de couches d'interface**. Son rôle unique est d'afficher les fenêtres contextuelles (modales) et les bannières d'information en fonction de l'état actuel du jeu.

C'est un composant de présentation qui permet de désencombrer le fichier principal `GameBoard.tsx` en regroupant toute la logique d'affichage conditionnel des dialogues.

---

## Fonctionnement : Affichage Conditionnel

Le composant reçoit en props les états de tous les hooks métier et utilise des expressions logiques pour décider quel élément doit être rendu à l'écran :

- **Gestion des Priorités** : Les éléments sont listés de manière à ce que plusieurs couches puissent éventuellement se superposer (ex: une bannière de rang illégal sous une fenêtre d'action).
- **Centralisation des Interactions** : Toutes les fonctions de confirmation (`onConfirm`) ou d'annulation (`onCancel`) des hooks sont liées ici aux boutons des modales correspondantes.

---

## Les Différentes Fenêtres Gérées

### Systèmes de Jeu

- **`GameOverScreen`** : S'affiche dès qu'un résultat de partie (`gameResult`) est détecté.
- **`InitiativeRollModal`** : Apparaît spécifiquement durant la phase d'initiative.
- **`IllegalRanksBanner`** : Alerte le joueur si ses unités ne respectent plus les règles de placement.

### Actions de Combat et Mouvement

- **`CombatRollModal`** : Gère l'affichage des jets de dés lors d'une attaque.
- **`CombatTargetBanner`** : Guide le joueur lorsqu'il doit sélectionner une cible ennemie.
- **`ManeuverModal`** : Permet de valider le déplacement d'une unité vers un rang adjacent.

### Gestion des Cartes

- **`DeployModal` / `EquipItemModal`** : Gèrent respectivement la mise en jeu de personnages et l'attachement d'objets.
- **`PlayActionCardModal`** : Utilisée pour choisir quel combattant exécute une carte d'action.
- **`CardActionsModal`** : Le menu contextuel qui s'ouvre au clic sur une carte pour proposer les options (Attaquer, Déployer, etc.).

### Fenêtre de Réaction (Sprint R2)

- **`ReactWindowModal`** : Une pièce critique qui s'active lorsqu'une capacité spéciale (comme une riposte ou un bonus) peut être jouée en réponse à une action. Elle communique directement avec le `dispatch` pour envoyer les actions de type `react` ou `reactPass`.

---

## Concepts Clés pour le Développement

### Types Dérivés

Le fichier utilise `ReturnType<typeof useHook>` pour garantir que les props reçues correspondent exactement à ce que les hooks de logique retournent, évitant ainsi les erreurs de typage lors des évolutions du moteur.

### Pas de Logique Interne

`GameModals` ne doit pas contenir de `useState` ou de calculs complexes. Si une modale doit apparaître, la décision doit venir d'une prop calculée en amont dans le `GameBoard`.

---

## Résumé

Le `GameModals` est responsable de :

- **Décharger le layout principal** de la gestion des fenêtres.
- **Faire le pont** entre les hooks de logique (`useCombat`, `useManeuver`) et les composants UI de dialogue.
- **Capturer les entrées utilisateur** critiques (confirmations, choix de cibles) pour les renvoyer vers le dispatcher.
