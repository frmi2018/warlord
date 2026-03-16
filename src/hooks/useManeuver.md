# Documentation de useManeuver.ts

## Localisation

`src/hooks/useManeuver.ts`

## Rôle du fichier

Le hook `useManeuver` gère la logique d'interface pour le déplacement tactique des unités sur le plateau (les "manœuvres"). Dans Warlord, déplacer un personnage est une action stratégique qui peut changer les lignes de front ou optimiser les auras de voisinage.

Ce hook orchestre le processus en trois temps : sélection du personnage, visualisation des destinations possibles, et exécution du mouvement via le moteur de jeu.

---

## Flux de Manœuvre

Le hook transforme une intention de mouvement en une action concrète pour le moteur :

1.  **Initialisation** (`startManeuver`) : Calcule les emplacements disponibles (slots) pour la carte sélectionnée en appelant `getManeuverTargets`.
2.  **Visualisation** : L'UI utilise `maneuverTargets` pour mettre en surbrillance les zones où la carte peut "atterrir".
3.  **Exécution** (`confirmManeuver`) : Envoie les coordonnées (`fromRank`, `toRank`, `slotIndex`) au dispatcher.
4.  **Finalisation** : Réinitialise l'état et signale la fin de l'action à la phase de jeu.

---

## Synchronisation et Réactions (Fix Timing)

Comme pour les attaques ou les cartes actions, les manœuvres peuvent déclencher des réactions (ex: un piège qui s'active lors d'un déplacement).

- **Le Verrou `pendingRef`** : Si le mouvement déclenche une fenêtre React (piège, capacité "Enter" d'un allié), le hook ne ferme pas immédiatement le tour du joueur.
- **Intégration `useReactSettled`** : Le hook attend patiemment que toutes les fenêtres de réaction soient fermées avant d'appeler `notifyActionDone()`. Cela garantit que l'IA ne commence pas son tour alors qu'une animation de réaction est encore à l'écran.

---

## Fonctions principales

### `startManeuver(card)`

Initialise le mode "déplacement". Elle récupère les cibles valides en fonction de la position actuelle de la carte et de l'occupation du plateau allié.

### `confirmManeuver(target)`

Déclenche le son de mouvement et dispatche l'action `maneuver`. C'est ici que la décision est prise d'attendre ou non une réaction via `openedReactWindow`.

### `maneuverTargets`

Un tableau d'objets `ManeuverTarget` contenant les coordonnées précises (rang et index de slot) pour l'affichage des "dropzones" dans l'UI.

---

## Interaction avec d'autres fichiers

- **`getManeuverTargets.ts`** : Contient la logique algorithmique qui définit où une carte a le droit d'aller (règles de contiguïté).
- **`useReactSettled.ts`** : Assure la fluidité du passage de tour en cas d'interruption par une réaction.
- **`soundManager.ts`** : Joue le retour sonore de mouvement pour renforcer le feedback utilisateur.

---

## Résumé

Ce fichier est responsable de :

- **Calculer les destinations légales** pour un personnage.
- **Gérer l'état local** du déplacement (quelle carte bouge et vers où).
- **Synchroniser le mouvement** avec le système de réactions du moteur.
- **Notifier les gestionnaires de phase** du succès ou de l'annulation de la manœuvre.
  """
