# Documentation de gameState.test.ts

## Localisation

`src/__tests__/gameState.test.ts`

## Rôle du fichier

Ce fichier contient les tests de validation du **GameState canonique**. Il s'assure que lorsque le jeu démarre, l'état initial est correctement structuré, complet et prêt à être utilisé par le moteur.

Il teste deux aspects majeurs :

1.  **L'initialisation** : La création d'une nouvelle partie via `createInitialGameState`.
2.  **Les Sélecteurs** : Les fonctions utilitaires (selectors) qui permettent d'extraire proprement des données de l'état (ex: "Où est le Warlord ?").

---

## Concept : État Initial et Sérialisabilité

Dans ce projet, le `GameState` doit être **100% sérialisable**. Cela signifie qu'il doit pouvoir être transformé en texte (JSON) sans perte d'information (pas de fonctions ou de références circulaires dans l'objet). C'est crucial pour pouvoir sauvegarder la partie ou la synchroniser.

---

## Fonctions de test

### `createInitialGameState`

Vérifie que la "photo" du jeu au tour 1 est correcte :

- **Identifiants** : Chaque partie possède un ID unique (UUID).
- **Phase de départ** : Le jeu commence toujours en `Draw Phase`, au tour 1, avec le joueur comme `activePlayer`.
- **Configuration des decks** : Les informations des decks (factions, images) sont bien conservées.
- **Placement des cartes** : Vérifie que le Warlord et les troupes de départ sont bien dans les zones de jeu (`playerRanks` / `aiRanks`) et non dans la pioche ou la défausse.

### `selectors` (Sélecteurs)

Les sélecteurs sont des fonctions "lecture seule" qui simplifient l'accès aux données :

- **`getPlayerCards` / `getAiCards`** : Récupère la liste complète des cartes d'un camp.
- **`getCardsByZoneFromState`** : Filtre les cartes pour ne retourner que celles d'une zone précise (ex: la main, le deck).
- **`getWarlord`** : Trouve spécifiquement la carte ayant le trait "Warlord" dans les rangs. Teste aussi le cas où le Warlord est mort (doit retourner `undefined`).
- **`isPlayerTurn`** : Détermine si c'est au tour de l'humain de jouer, en fonction de la phase actuelle et de l'avancement des décrets.

---

## Fixtures

Le fichier utilise des **Fixtures** (`playerDeckInfo`, `aiDeckInfo`). Ce sont des jeux de données fixes et simplifiés (Nains vs Devereniens) utilisés pour simuler des decks réels sans charger toute la base de données.

---

## Variables et États testés

| Élément      | Description                                             |
| :----------- | :------------------------------------------------------ |
| `phase`      | L'étape actuelle du tour (Draw, Ready, Decree...).      |
| `turnNumber` | Le numéro du tour actuel.                               |
| `zone`       | L'emplacement d'une carte (Hand, Ranks, Deck, Discard). |
| `decreeTurn` | Désigne qui a la main pendant la phase de Décret.       |
| `isGameOver` | Indicateur de fin de partie.                            |

---

## Interaction avec d'autres fichiers

- `createInitialGameState.ts` : La logique de création testée ici.
- `selectors.ts` : Les fonctions d'accès aux données testées ici.
- `gameState.ts` (Type) : Définit la structure que ces tests valident.

---

## Résumé

Ce fichier garantit que :

1.  Le jeu démarre avec des **fondations solides** (données propres et complètes).
2.  Les outils utilisés par l'interface (UI) pour lire l'état sont **fiables**.
3.  La logique de début de partie (placement des troupes, choix de la phase) est respectée.
