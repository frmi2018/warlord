# Documentation de usePhaseManager.ts

## Localisation

`src/hooks/usePhaseManager.ts`

## Rôle du fichier

Le `usePhaseManager` est le **métronome du jeu**. Il orchestre la transition entre les différentes étapes d'un tour (Ready, Draw, Initiative, Decree, End of Turn). Son rôle est de s'assurer que les règles spécifiques à chaque phase sont appliquées et que le passage à la suivante s'effectue de manière cohérente.

Il sert de pont entre l'état global (`GameState`) et les sous-logiques de phases, notamment la complexe **Decree Phase** qu'il délègue au hook `useDecreePhase`.

---

## Structure d'un Tour (Le Cycle de Vie)

Le manager gère la boucle de jeu standard de Warlord (4e édition) :

1.  **Ready Phase** : Redresse toutes les cartes inclinées (Spent -> Ready).
2.  **Draw Phase** : Permet aux joueurs de défausser des cartes et de piocher pour compléter leur main.
3.  **Initiative Phase** : Détermine quel joueur agira en premier durant le tour.
4.  **Decree Phase** : La phase principale d'action où les joueurs alternent leurs coups.
5.  **End of Turn Phase** : Nettoie les effets temporaires et prépare le tour suivant.

---

## Fonctions et Logiques Clés

### `advancePhase()`

Calcule la phase suivante en utilisant l'utilitaire `getNextPhase`. Si le jeu entre en _Ready Phase_, il incrémente automatiquement le `turnNumber`.

### `handleNextPhase()`

C'est le point d'entrée du bouton "Phase Suivante" de l'UI. Selon la phase actuelle, il exécute la logique métier associée :

- **Draw** : Appelle `executeDrawPhase` pour traiter les défausses choisies par le joueur.
- **Ready** : Appelle `executeReadyPhase` pour réinitialiser les unités.
- **End of Turn** : Appelle `executeEndOfTurnPhase` pour les maintenances de fin.

### `useEffect` (Gestion de l'Initiative et du Décret)

Le hook utilise des effets pour initialiser les données dès qu'on entre dans une phase critique :

- Remise à zéro de l'initiative à l'entrée en _Initiative Phase_.
- Réinitialisation des compteurs d'actions à l'entrée en _Decree Phase_.

---

## États Transitoires vs État de Jeu

Le manager fait une distinction subtile entre deux types de données :

1.  **Champs du GameState** (`currentPhase`, `turnNumber`) : Ces données sont persistantes et synchronisées via `setGameState`.
2.  **États Transitoires** (`cardsToDiscard`, `selectedCardsIds`) : Ces données ne servent qu'à l'interface pendant la _Draw Phase_ (ex: quelles cartes sont cochées pour la défausse). Elles restent locales au hook pour éviter de polluer l'état global permanent.

---

## Interaction avec useDecreePhase

La **Decree Phase** est la seule phase qui n'est pas "instantanée". Elle demande une alternance d'actions entre l'IA et le joueur. `usePhaseManager` instancie `useDecreePhase` et expose ses propriétés (comme `isAiThinking` ou `handlePlayerPass`) pour que l'interface puisse afficher les bons indicateurs de progression.

---

## Résumé

Ce fichier est responsable de :

- **Maintenir la séquence logique** du jeu.
- **Appliquer les effets de maintenance** (Ready, End of Turn).
- **Gérer le passage du temps** (incrémentation des tours).
- **Synchroniser les transitions** entre les actions du joueur et les réactions de l'IA.
  """
