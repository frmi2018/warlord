# Documentation de useReactSettled.ts

## Localisation

`src/hooks/useReactSettled.ts`

## Rôle du fichier

Ce hook est le **médiateur de synchronisation** du moteur. Sa mission est de détecter le moment précis où une "Fenêtre de Réaction" (React Window) se referme pour libérer la suite du flux de jeu.

Il résout un problème critique de concurrence : empêcher que le tour de l'IA ou la phase suivante ne s'activent alors que le joueur est encore en train de lire un effet ou de faire un choix dans une modal de réaction.

---

## Problématique : Le conflit des flux

Dans un moteur basé sur des événements, une action simple (ex: "Attaquer") peut être interrompue par une réaction (ex: "Piège").

- **Sans ce hook** : L'interface appelle `notifyActionDone()` juste après le clic. Le jeu croit que l'action est finie et passe au tour suivant, alors que la modal du "Piège" vient à peine de s'ouvrir.
- **Avec ce hook** : L'interface pose un "verrou" (`pendingRef`). Le jeu attend que la modal se ferme avant de signaler que l'action est véritablement terminée.

---

## Fonctionnement technique : Détection de Transition

Le hook n'utilise pas de tableau de dépendances classique dans son `useEffect` afin d'observer chaque cycle de rendu avec une précision maximale.

1.  **Mémorisation** : Il garde une trace de l'état précédent de la fenêtre (`prevWindowRef`).
2.  **Détection de Front Descendant** : Il cherche le moment exact où :
    - `prev !== null` (Une fenêtre était ouverte)
    - `curr === null` (Elle vient de se fermer)
3.  **Vérification du Verrou** : Si `pendingRef.current` est à `true`, cela signifie qu'une action parente attendait cette fermeture.
4.  **Libération** : Le verrou est remis à `false` et le callback `onSettled()` (souvent `notifyActionDone`) est exécuté.

---

## Pourquoi une Ref pour `pendingRef` ?

Le hook utilise une `MutableRefObject<boolean>` passée par le parent (ex: `useCombat`).

- Contrairement à un `useState`, modifier une `Ref` ne provoque pas de nouveau rendu.
- Cela permet de marquer une action comme "en attente de fermeture" de manière synchrone, juste après un `dispatch`, sans entrer dans une boucle de rendus React infinis.

---

## Cas d'usage

### Chemin Long (Réaction déclenchée)

1. `dispatch(attaque)` → ouvre une fenêtre de réaction.
2. Le parent pose `pendingRef.current = true`.
3. L'utilisateur interagit avec la fenêtre.
4. La fenêtre se ferme → `useReactSettled` détecte la fin et appelle `notifyActionDone()`.

### Chemin Court (Pas de réaction)

1. `dispatch(attaque)` → aucune fenêtre ne s'ouvre.
2. Le parent voit que `openedReactWindow` est `false`.
3. Le parent appelle `notifyActionDone()` immédiatement.
4. `useReactSettled` ne fait rien car il n'y a pas eu de transition `non-null` vers `null`.

---

## Interaction avec d'autres fichiers

- **`useCombat.ts`** : L'utilise pour attendre la fin des ripostes ou des bonus de combat.
- **`usePlayActionCard.ts`** : L'utilise pour attendre la résolution des effets de sorts.
- **`useManeuver.ts`** : L'utilise pour synchroniser les déplacements tactiques.

---

## Résumé

Ce fichier est responsable de :

- **Détecter la fermeture** des modals de réaction.
- **Synchroniser l'état local UI** avec l'état global du moteur.
- **Éviter les chevauchements de tours** entre le joueur et l'IA.
- **Garantir un flux de jeu séquentiel** et propre.
  """
