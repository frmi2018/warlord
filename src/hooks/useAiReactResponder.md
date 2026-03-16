# Documentation de useAiReactResponder.ts

## Localisation

`src/hooks/useAiReactResponder.ts`

## Rôle du fichier

Ce hook React agit comme un **automètre de réponse pour l'IA**. Sa seule mission est de surveiller l'état du jeu et, dès qu'une "Fenêtre de Réaction" (React Window) s'ouvre et désigne l'IA comme le joueur actif (`currentReactor === "ai"`), de déclencher une décision après un court délai.

Il permet de simuler la présence d'un adversaire réactif sans bloquer le thread principal de React et en offrant un retour visuel compréhensible pour le joueur humain.

---

## Concept : L'Observation Réactive

Le hook utilise le mécanisme `useEffect` pour "écouter" les changements de l'état global.

1.  **Condition de déclenchement** : La fonction `shouldAiRespond(gameState)` renvoie `true`.
2.  **Délai de réflexion** : Un `setTimeout` de 600ms est lancé. Cela évite que l'IA ne réponde instantanément (ce qui rendrait la fenêtre invisible pour l'œil humain).
3.  **Validation finale** : Juste avant de répondre, l'IA vérifie une dernière fois que c'est toujours son tour (sécurité anti-concurrence).

---

## Gestion de la Concurrence (Refs)

Pour éviter les problèmes de "Stale Closures" (clôtures périmées) classiques dans React :

- **`gameStateRef`** : Contient toujours la version la plus récente du jeu. Si un événement se produit pendant les 600ms de réflexion, l'IA prendra sa décision basée sur la situation _réelle_ au moment de l'expiration du délai, pas sur celle au moment du lancement.
- **`dispatchRef`** : Garantit que l'IA utilise toujours la fonction de dispatch la plus à jour fournie par le parent.

---

## Fonctions et Logique Interne

### `useEffect([isAiTurn])`

Le moteur du hook. Il ne se ré-exécute que si le statut "C'est au tour de l'IA" change. Cela garantit une performance optimale en évitant des calculs inutiles à chaque modification mineure du state (comme le survol d'une carte).

### `AI_REACT_DELAY_MS`

Constante fixée à **600ms**. C'est le "temps de cerveau" simulé de l'IA.

---

## Pourquoi GameAction et non PlayerAction ?

Contrairement aux actions humaines, les réponses de l'IA (`aiReact`, `aiReactPass`) sont injectées directement dans le moteur.

- Elles **court-circuitent** les validateurs de sécurité destinés aux joueurs (qui vérifient par exemple si l'ID de l'utilisateur correspond au joueur actif).
- Utiliser `GameAction` permet d'utiliser le canal de communication rapide du moteur, évitant des erreurs de typage ou des rejets d'action injustifiés.

---

## Interaction avec d'autres fichiers

Ce hook est le pont entre l'interface et le cerveau de l'IA :

- **`aiReactDecider.ts`** : Fournit la logique "grise" (quelle carte choisir pour réagir ?).
- **`useActionDispatcher.ts`** : Reçoit l'action finale envoyée par le hook.
- **`GameBoard.tsx`** : Instancie ce hook pour qu'il soit actif durant toute la partie.

---

## Résumé

Ce fichier est responsable de :

- **Détecter automatiquement** quand l'IA doit parler dans une fenêtre de réaction.
- **Humaniser l'IA** en ajoutant un délai de réponse artificiel.
- **Garantir la robustesse** du dispatch en utilisant des références fraîches du State.
- **Nettoyer les timers** (`clearTimeout`) pour éviter des fuites de mémoire ou des actions fantômes.
  """
