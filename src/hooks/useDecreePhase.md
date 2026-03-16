# Documentation de useDecreePhase.ts

## Localisation

`src/hooks/useDecreePhase.ts`

## Rôle du fichier

`useDecreePhase` est le **moteur de tour-par-tour** principal du jeu. Il gère l'alternance entre le joueur et l'IA durant la phase de décret (la phase d'action). Contrairement aux autres phases qui sont souvent instantanées, celle-ci est asynchrone, ponctuée de délais visuels, d'animations et d'interruptions (Fenêtres de Réaction).

Il ne prend aucune décision de jeu lui-même ; il agit comme un **exécuteur** qui demande à l'IA quoi faire, attend la fin des animations, puis passe la main au joueur.

---

## Architecture : L'exécution de l'IA

Le hook suit un cycle strict pour chaque action de l'ordinateur :

1.  **Réflexion** (`setIsAiThinking`) : Un délai de 800ms est appliqué pour simuler le temps de calcul.
2.  **Décision** (`aiDecide`) : Le hook appelle le cerveau de l'IA qui renvoie une action (Attaque, Déploiement, Manœuvre, Equipement ou Passe).
3.  **Résolution** (`resolveXxx`) : Le hook exécute l'action spécifique. Chaque type d'action possède son propre `setTimeout` pour laisser le temps aux sons et aux transitions CSS de s'afficher.
4.  **Fin de tour** (`endAiTurn`) : Les compteurs sont mis à jour et le tour bascule vers le joueur humain.

---

## Gestion des "Stale Closures" et Performance

Parce que le tour de l'IA repose lourdement sur des `setTimeout`, il existe un risque que l'IA utilise des données périmées si le joueur a réagi entre-temps.

- **Usage massif de `useRef`** : `aiCards`, `playerCards`, et même les fonctions de dispatch sont synchronisées dans des Refs. Cela garantit que `executeAiTurn` accède toujours à la situation actuelle du plateau, même au milieu d'une chaîne de délais.
- **Inlining des résolveurs** : Les fonctions `resolveAttack`, `resolvePlayCard`, etc., sont déclarées _à l'intérieur_ de `executeAiTurn`. Cela permet de garder un tableau de dépendances vide pour `useCallback`, évitant ainsi de recréer la logique du tour à chaque modification mineure de l'UI.

---

## Synchronisation avec les Réactions (Fix Riposte)

C'est ici que se joue la synchronisation critique avec les fenêtres de réaction :

- **Le Problème** : Si l'IA attaque, le moteur peut ouvrir une fenêtre de "Riposte" pour le joueur. L'IA ne doit pas finir son tour tant que le joueur n'a pas choisi de riposter ou de passer.
- **La Solution** :
  - `pendingEndAiTurnRef` : Si l'attaque IA ouvre une fenêtre (`openedReactWindow`), on lève un drapeau.
  - Un `useEffect` surveille la fermeture de la fenêtre (`gameState.reactWindow === null`).
  - Dès que la fenêtre se ferme, si le drapeau est levé, le tour de l'IA se termine enfin proprement.

---

## Événements et Bus de Données

Le hook communique avec le reste du système via deux canaux :

1.  **`globalEventBus`** : Émet des événements comme `turn_start` ou `card_played`. Cela permet à d'autres systèmes (effets passifs, quêtes, logs) de réagir sans être directement liés à ce hook.
2.  **`aiActionEvent`** : Un état local qui contient les détails de la dernière action IA (nom de la carte, raison de la priorité). Ce state est consommé par le composant `AiActionToast` pour afficher des messages du type _"L'IA utilise Charge car son unité est en retrait"_.

---

## Condition de Fin de Phase

La phase de décret se termine selon la règle officielle : **"Deux passes consécutives"**.

- Si le joueur passe, `consecutivePasses` passe à 1.
- Si l'IA passe immédiatement après, `consecutivePasses` passe à 2, et `onPhaseEnd()` est appelé pour changer de phase.
- Toute action (Attaque, Pose de carte) remet ce compteur à 0.

---

## Résumé

Ce fichier est responsable de :

- **Orchestrer l'alternance** entre le joueur et l'IA.
- **Simuler le comportement de l'IA** (délais de réflexion et d'animation).
- **Gérer la continuité des données** via des Refs pour éviter les bugs de synchronisation.
- **Attendre la résolution des réactions** humaines avant de rendre la main.
- **Notifier le système** des débuts et fins de tours via l'EventBus.
  """
