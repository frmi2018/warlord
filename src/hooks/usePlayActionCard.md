# Documentation de usePlayActionCard.ts

## Localisation

`src/hooks/usePlayActionCard.ts`

## Rôle du fichier

Ce hook orchestre le processus complexe de **jouer une carte Action** (type d'action ponctuelle) depuis la main du joueur. Dans Warlord, jouer une carte action nécessite souvent de désigner un "exécutant" (performer) sur le plateau.

Ce fichier fait le pont entre la main du joueur, les cibles valides sur le plateau et le moteur de jeu, tout en gérant les notifications de phase (Decree Phase) et les éventuelles fenêtres de réaction.

---

## Flux UX : Jouer une Action

Le processus est découpé en plusieurs étapes pour garantir une expérience utilisateur fluide :

1.  **Déclenchement** (`startPlayAction`) : Le joueur sélectionne une carte action en main. Le hook vérifie s'il existe au moins un personnage éligible pour l'utiliser.
2.  **Ciblage de l'Exécutant** : Une modal s'affiche, présentant les `eligibleFighters`.
3.  **Confirmation** (`confirmPlayAction`) : Le joueur choisit son personnage. L'action est alors dispatchée au moteur avec les deux IDs : la carte jouée et le personnage qui l'exécute.
4.  **Finalisation** : La carte est défaussée et la phase de jeu continue.

---

## Concept : Les Eligibility Resolvers

Toutes les cartes actions ne fonctionnent pas de la même manière (certaines demandent un Mage, d'autres un guerrier adjacent, etc.).

- Pour rester extensible, le hook utilise un **Resolver**.
- `getEligibilityResolver` : Identifie la logique spécifique à la carte (ex: pour "Charge" `sots-171`, il appelle `getChargeEligibleFighters`).
- Cela permet d'ajouter de nouvelles cartes actions simplement en créant un nouveau fichier de règles et en le branchant dans le switch central.

---

## Gestion des Fenêtres de Réaction (Fix Sprint R2)

L'une des difficultés majeures était la synchronisation avec la `Decree Phase`.

- **Problème** : Si une action déclenche une réaction (ex: "Charge" déclenche un piège adverse), la phase de décret ne doit pas se terminer avant que la réaction soit résolue.
- **Solution** : Comme pour le combat, le hook utilise `pendingRef` et `useReactSettled`.
  - Si `dispatch` retourne `openedReactWindow = true`, on attend.
  - `notifyActionDone()` n'est appelé que lorsque le calme est revenu sur le plateau.

---

## Fonctions principales

### `startPlayAction(actionCard)`

Initialise le processus. Si aucun personnage ne peut exécuter l'action (ex: pas de guerrier prêt pour une Charge), l'action ne se lance pas.

### `confirmPlayAction(fighterId)`

L'étape de validation finale. Elle consomme l'action et libère l'état `playingCard`.

### `eligibleFighters` (Variable calculée)

Filtre dynamiquement les unités sur le plateau qui remplissent les conditions de la carte action sélectionnée.

---

## Interaction avec d'autres fichiers

- **`useReactSettled.ts`** : Garantit que l'IA ou le joueur suivant n'agit pas pendant qu'une réaction est en cours.
- **`canPlayCharge.ts`** : Exemple de règle métier externe injectée dans le hook.
- **`applyPlayAction.ts`** : Le reducer côté moteur qui traite l'action une fois dispatchée.

---

## Résumé

Ce fichier est responsable de :

- **Vérifier l'éligibilité** des personnages pour une action donnée.
- **Guider l'UI** dans le choix de l'exécutant de la carte.
- **Synchroniser la fin de l'action** avec le système de phases du jeu.
- **Gérer les interruptions** par des fenêtres de réaction.
  """
