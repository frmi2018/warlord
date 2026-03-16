# Documentation de shuffle.ts

## Localisation

`src/utils/shuffle.ts`

## Rôle du fichier

Ce fichier fournit une fonction utilitaire fondamentale pour tout jeu de cartes : le **mélange aléatoire**. Il implémente l'algorithme de **Fisher-Yates** (également connu sous le nom de mélange de Knuth), qui est la méthode de référence en informatique pour garantir une permutation aléatoire uniforme et impartiale.

Il est utilisé partout où le hasard intervient : mélange du deck lors du setup, pioche de cartes aléatoires, ou redistribution de la défausse.

---

## L'Algorithme : Fisher-Yates

L'algorithme parcourt le tableau de la fin vers le début et échange l'élément actuel avec un autre élément choisi au hasard parmi ceux qui le précèdent (incluant lui-même).

### Pourquoi cet algorithme ?

Contrairement à des méthodes simplistes comme `.sort(() => Math.random() - 0.5)`, Fisher-Yates :

- **Est impartial** : Chaque permutation possible du tableau a exactement la même probabilité d'apparaître.
- **Est performant** : Sa complexité est de $O(n)$, ce qui signifie qu'il est extrêmement rapide même pour des decks de plusieurs centaines de cartes.

---

## Fonctionnement technique : `shuffle<T>(array)`

La fonction est conçue pour être à la fois flexible et sécurisée :

1.  **Généricité (`<T>`)** : Elle accepte des tableaux de n'importe quel type (objets `Card`, chaînes de caractères, nombres, etc.).
2.  **Immuabilité** : Elle crée systématiquement une **copie superficielle** (`[...array]`) avant de procéder au mélange. Cela évite de modifier par inadvertance le tableau d'origine, ce qui est crucial pour la gestion d'état avec React.
3.  **Destructuration** : Utilise la syntaxe ES6 `[a, b] = [b, a]` pour effectuer l'échange d'éléments de manière concise et lisible.

---

## Pourquoi cette architecture ?

| Caractéristique     | Bénéfice                                                                                                     |
| :------------------ | :----------------------------------------------------------------------------------------------------------- |
| **Copie locale**    | Empêche les bugs où le deck original du joueur serait modifié avant que l'action ne soit validée.            |
| **Pureté**          | La fonction est prévisible : elle prend une entrée, retourne une sortie, et n'affecte rien à l'extérieur.    |
| **Standardisation** | Centraliser le mélange ici garantit que le "hasard" est géré de la même manière pour le joueur et pour l'IA. |

---

## Exemple d'utilisation

```typescript
import { shuffle } from "./utils/shuffle";

const deckMelange = shuffle(deckInitial);
console.log("Deck prêt pour la partie !");
```
