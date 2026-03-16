# Documentation de compileDSL.ts

## Localisation

`src/engine/effects/dsl/compileDSL.ts`

## Rôle du fichier

Ce fichier est le **cerveau logique** du système DSL. Son rôle est de transformer un objet de configuration simple (`EffectDSL`) en une fonction complexe et exécutable (`CardEffect`).

C'est ici que l'on définit mathématiquement ce que signifie "être adjacent" ou "être au rang 1". En centralisant cette logique, on garantit que toutes les cartes se comportent de la même manière.

---

## Logique des Patterns

### 🛡️ Bonus Passifs

Le compilateur génère des fonctions `apply` qui scannent le plateau :

- **Adjacence** : Utilise le helper `areAdjacent` pour compter combien de cartes cibles entourent l'unité et multiplie le bonus en conséquence.
- **Positionnement** : Compare le `posY` de la carte (0-indexé en code) avec le rang visé (1-indexé dans les règles).

### ⚡ Effets Actifs (Orders)

Le compilateur génère des structures `ActiveEffect` complètes :

- **Validation (`canUse`)** : Vérifie que la carte est prête (`ready`) et sur le plateau.
- **Exécution (`execute`)** : Incline la carte (`spent`) et renvoie un **message de commande** (ex: `SEARCH_DECK_FOR_ITEM`). Ce message est ensuite intercepté par l'interface pour ouvrir les menus de pioche.
- **Intelligence Artificielle (`aiScore`)** : Fournit une estimation de l'utilité de l'action pour que l'IA puisse décider de l'utiliser ou non.

---

## Architecture : Le Switch Exhaustif

Le fichier utilise une structure `switch (dsl.pattern)` avec une sécurité TypeScript :

```typescript
default: {
  const _exhaustive: never = dsl;
  throw new Error(...);
}
```
