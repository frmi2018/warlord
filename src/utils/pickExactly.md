# Documentation de pickExactly.ts

## Localisation

`src/utils/pickExactly.ts`

## Rôle du fichier

Ce fichier est un utilitaire de sélection stricte. Il permet de piocher ou de sélectionner un nombre précis ($N$) de cartes au sein d'un ensemble (un "pool").

Il est principalement utilisé lors de la **génération de decks** ou lors de la mise en place d'une partie (Setup), où le moteur a besoin de garanties mathématiques sur le nombre de cartes extraites pour respecter l'équilibrage du jeu.

---

## Logique de Sélection

La fonction `pickExactly` suit un processus en deux étapes garantissant l'équité et la robustesse :

1.  **Mélange Aléatoire** : Elle utilise la fonction `shuffle()` pour mélanger le pool de cartes source. Cela garantit que chaque carte a une probabilité égale d'être sélectionnée.
2.  **Extraction** : Elle effectue une découpe (`slice`) pour ne conserver que les $N$ premiers éléments du tableau mélangé.

---

## Sécurité et Gestion d'Erreur

Contrairement à une fonction de pioche classique qui pourrait retourner "ce qu'il reste", `pickExactly` est **intransigeante**.

- **Validation du Pool** : Si le nombre de cartes demandées est supérieur au nombre de cartes disponibles, la fonction **lève une exception** (`throw Error`).
- **Traceur de Debug** : Le paramètre `label` permet d'identifier précisément quel module a échoué (ex: _"Draft du Warlord"_ ou _"Main de départ"_), facilitant la correction des bugs de données.

---

## Pourquoi cette architecture ?

| Avantage                 | Description                                                                                                                                                      |
| :----------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Garantie de Quantité** | Assure que les fonctions appelantes reçoivent toujours exactement le nombre de cartes attendu, évitant les tests de type `if (cards.length === n)` à répétition. |
| **Impartialité**         | L'intégration du `shuffle` en interne évite d'oublier de mélanger les cartes avant une sélection aléatoire.                                                      |
| **Fail-Fast**            | En levant une erreur immédiatement si le pool est insuffisant, on évite que le jeu ne continue dans un état instable ou corrompu.                                |

---

## Exemple d'utilisation

```typescript
// Sélectionner 3 Warlords parmi les cartes disponibles pour le choix du joueur
const selectedWarlords = pickExactly(allWarlords, 3, "Choix du Warlord");
```
