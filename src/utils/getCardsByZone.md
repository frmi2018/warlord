# Documentation de getCardsByZone.ts

## Localisation

`src/utils/getCardsByZone.ts`

## Rôle du fichier

Ce fichier utilitaire est le "gestionnaire de vue" du moteur de jeu. Son rôle est d'extraire un sous-ensemble de cartes à partir du tableau global (`cards`) en fonction de leur localisation (`zone`) et de les organiser selon un ordre logique propre à chaque zone.

Il garantit que le reste de l'application (l'interface utilisateur ou l'IA) reçoit des listes de cartes cohérentes, triées et prêtes à être affichées ou traitées.

---

## Logique de Tri par Zone

L'utilitaire ne se contente pas de filtrer ; il applique un algorithme de tri spécifique à la nature de la zone demandée :

### 1. Le Deck (`playerDeck`, `aiDeck`)

- **Critère** : `deckOrder`
- **Logique** : La carte avec l'index le plus bas est considérée comme la "carte du dessus", soit la prochaine à être piochée.

### 2. La Main (`playerHand`, `aiHand`)

- **Critère** : `handOrder`
- **Logique** : Préserve l'ordre visuel des cartes dans la main du joueur pour éviter qu'elles ne sautent de position lors d'une mise à jour d'état.

### 3. La Défausse (`playerDiscard`, `aiDiscard`)

- **Critère** : `discardOrder`
- **Logique** : Trie les cartes selon l'ordre chronologique de leur élimination.

### 4. Les Rangs de Combat (`playerRanks`, `aiRanks`)

C'est le tri le plus complexe, car il gère un plan en 2D :

- **Priorité 1** : `posY` (Le Rang). Les cartes sont groupées du premier rang vers l'arrière.
- **Priorité 2** : `posX` (La Position). Au sein d'un même rang, les cartes sont triées de la gauche vers la droite.

---

## Pourquoi cette architecture ?

| Avantage                   | Description                                                                                                |
| :------------------------- | :--------------------------------------------------------------------------------------------------------- |
| **Performance**            | En filtrant et triant en une seule passe logique, on réduit la charge de calcul pour les composants React. |
| **Affichage Déterministe** | Garantit que le plateau de jeu ne se réorganise pas de manière aléatoire lors du rendu.                    |
| **Sécurité des Types**     | Utilise le type `CardZone` pour empêcher les erreurs de frappe lors de la sélection des zones de jeu.      |

---

## Utilisation type

```typescript
// Récupérer la ligne de front et les soutiens bien organisés
const myArmy = getCardsByZone(allCards, "playerRanks");
```
