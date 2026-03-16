# Documentation de moveCard.ts

## Localisation

`src/utils/moveCard.ts`

## Rôle du fichier

Ce fichier est le **moteur logistique central** du jeu. Il orchestre tous les transferts de cartes entre les différentes zones (Deck, Main, Rangs, Défausse). Contrairement à un simple changement de propriété, `moveCard` gère les effets de bord complexes : la réindexation des positions, la réinitialisation des états et le maintien de l'intégrité spatiale des rangs.

---

## Fonctionnalités Clés

### 1. Gestion Intelligente des Zones

Le script adapte le comportement de la carte en fonction de sa destination :

- **Vers le Deck/la Défausse** : Gère les piles (LIFO/FIFO) en décalant les index (`deckOrder`, `discardOrder`). On peut choisir de placer une carte au-dessus, au-dessous ou à un index précis.
- **Vers la Défausse (Nettoyage)** : Réinitialise automatiquement les blessures (`wounds`), l'orientation (`ready`) et retire les sélections pour que la carte soit "neuve" si elle est réanimée plus tard.

### 2. Renormalisation des Rangs (`renormalizeRankPositions`)

C'est la fonction la plus critique pour la cohérence tactique. Lorsqu'une carte quitte un rang (destruction ou défausse) :

- Le moteur détecte le "trou" laissé dans la ligne.
- Il recalcule les positions horizontales (`posX`) pour qu'elles soient toujours consécutives (0, 1, 2...).
- **Pourquoi ?** Les effets de proximité (ex: _"Les cartes adjacentes gagnent +1 AC"_) reposent sur une suite mathématique parfaite. Sans cela, une carte détruite briserait les bonus de ses anciens voisins.

### 3. Réorganisation (`reorderCard`)

Permet de déplacer une carte à l'intérieur de sa propre zone (ex: réorganiser sa main ou replacer une carte dans le deck suite à un effet de jeu).

---

## Logique de Mouvement : Un exemple

Lorsqu'une unité au front (Rang 1, Position 1) est détruite et envoyée en défausse :

1.  **Changement de Zone** : `zone` devient `playerDiscard`.
2.  **Reset** : `wounds` passe à 0.
3.  **Indexation** : Elle reçoit le `discardOrder: 0` (sommet de pile).
4.  **Compression** : Toutes les cartes du Rang 1 ayant un `posX` supérieur à 1 voient leur position décrémentée de 1 pour fermer la brèche.

---

## API du module

| Fonction        | Usage                                                                         |
| :-------------- | :---------------------------------------------------------------------------- |
| `moveCard()`    | Déplace une seule carte et déclenche la renormalisation.                      |
| `moveCards()`   | Version "bulk" pour déplacer plusieurs IDs d'un coup (ex: défausser sa main). |
| `reorderCard()` | Modifie l'ordre interne (index) sans changer de zone.                         |

---

## Pourquoi cette architecture ?

- **Immuabilité Totale** : Chaque mouvement retourne un nouveau tableau de cartes, ce qui est crucial pour que React puisse animer le déplacement des cartes à l'écran.
- **Fiabilité Tactique** : En garantissant des `posX` sans trous, on élimine les bugs liés aux calculs d'adjacence lors des combats.
- **Centralisation** : Toutes les règles de "nettoyage" d'une carte (quand elle meurt) sont ici, évitant d'oublier de soigner les blessures ou de redresser la carte.

---

## Résumé

Ce fichier garantit que :

- Les **transferts de zones** sont fluides et sans bugs.
- La **formation des rangs** reste compacte et mathématiquement correcte.
- Le **nettoyage des états** est automatique lors de la défausse.
