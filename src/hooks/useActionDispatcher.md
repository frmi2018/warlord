# Documentation de useActionDispatcher.ts

## Localisation

`src/hooks/useActionDispatcher.ts`

## Rôle du fichier

Le `useActionDispatcher` est la **tour de contrôle** de l'application. C'est l'unique point d'entrée par lequel passent toutes les intentions de jeu (Joueur, IA ou Système) pour être transformées en changements d'état.

Ce hook orchestre le cycle "Validation -> Application -> Résolution des triggers", garantissant que le moteur de jeu reste synchronisé avec l'interface React tout en gérant les fenêtres de réaction complexes (React Windows).

---

## Concept : Le Dispatcher "Intelligent"

Contrairement à un dispatcher Redux classique, celui-ci possède une logique de "post-traitement" récursive :

1.  **Validation** : Si l'action vient du joueur, il vérifie sa légalité via `validateAction`.
2.  **Application** : Il calcule l'état résultant via `applyAction`.
3.  **Résolution des Triggers** : Il vérifie si une action est "en attente" (`pendingTriggerAction`). Si c'est le cas et qu'aucune fenêtre de réaction n'est ouverte, il l'exécute immédiatement **en mémoire** avant de rendre la main à React.

---

## Gestion du "Stale Closure" (latestStateRef)

L'une des parties les plus critiques du fichier est l'utilisation de `useRef` pour `latestStateRef`.

- **Problème** : Si deux `dispatch` sont appelés dans le même bloc de code synchrone (ex: `resolveAttack` suivi de `finishAttack`), le second dispatch lirait un état périmé car React n'a pas encore fini son rendu.
- **Solution** : Le dispatcher met à jour `latestStateRef.current` immédiatement après chaque calcul. Le dispatch suivant utilise alors cet état "frais" calculé en mémoire, même si le `setGameState` n'a pas encore provoqué de nouveau rendu.

---

## Le Flux de la Fenêtre de Réaction (R2-fix.2)

Le hook gère élégamment l'interruption du flux de jeu par des capacités spéciales (comme le _Powerattack_) :

- **Interception** : Si une action (ex: `spendAttacker`) déclenche un effet qui demande un choix au joueur, le `state` est "gelé".
- **Mise en attente** : L'action originale est stockée dans `pendingTriggerAction`.
- **Libération** : Ce n'est qu'une fois la fenêtre de réaction fermée (`reactPass`) que le dispatcher détecte qu'il peut enfin exécuter l'action originale mise en attente.

---

## Fonctions et Types

### `dispatch(action): { openedReactWindow: boolean }`

- **Entrée** : Une `GameAction` (Joueur, IA, ou Système).
- **Sortie** : Un indicateur précisant si une fenêtre de dialogue vient de s'ouvrir.
- **Comportement** : Court-circuite la validation pour les actions non-joueurs (IA/Système).

### `PLAYER_ACTION_TYPES`

Une liste blanche des types d'actions qui doivent impérativement être vérifiées par les règles du jeu (est-ce le tour du joueur ? l'unité est-elle prête ? etc.).

---

## Interaction avec d'autres fichiers

- **`validateAction.ts`** : Utilisé pour refuser les coups illégaux du joueur.
- **`applyAction.ts`** : Le moteur qui calcule la transformation de l'état.
- **`pendingResolver.ts`** : La logique qui décide si on doit consommer une action en attente.

---

## Résumé

Ce fichier est responsable de :

- **Unifier le traitement** des actions (IA et Humain).
- **Gérer la récursivité** des actions déclenchées (triggers).
- **Éviter les bugs de désynchronisation** React grâce aux références (`useRef`).
- **Suspendre le flux de jeu** pour les fenêtres de réactions.
  """
