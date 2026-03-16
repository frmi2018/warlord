# Documentation de cardGuards.ts

## Localisation

`src/engine/rules/cardGuards.ts`

## Rôle du fichier

Ce fichier contient les **Type Guards** personnalisés de TypeScript pour manipuler l'union de types `Card`. En programmation, un "guard" est une fonction qui permet de confirmer avec certitude le type d'un objet afin de débloquer l'accès à ses propriétés spécifiques.

L'objectif est d'éviter l'utilisation de vérifications manuelles (comme `card.cardType === "..."`) éparpillées dans tout le projet, centralisant ainsi la logique de typage.

---

## Fonctionnement des Guards

Chaque fonction utilise la syntaxe `card is [Type]` pour informer le compilateur TypeScript. Une fois le guard passé, vous n'avez plus besoin d'utiliser l'opérateur optionnel `?.` pour accéder aux champs spécifiques.

### 1. `isCharacter(card)`

- **Cible** : `CharacterCard`
- **Débloque** : `attackValues`, `armorClass`, `hitPoints`, etc.

### 2. `isItem(card)`

- **Cible** : `ItemCard`
- **Débloque** : `level`, `traits` (spécifiques aux objets).

### 3. `isAction(card)`

- **Cible** : `ActionCard`
- **Débloque** : Le tableau `actions` contenant les effets de sort ou de tactique.

### 4. `isWarlord(card)`

- **Cible** : `CharacterCard` (avec le trait "Warlord")
- **Usage** : Permet d'identifier l'unité centrale de l'armée. Comme le Warlord est techniquement un personnage, ce guard réduit le type vers `CharacterCard`.

---

## Pourquoi utiliser ces Guards ?

| Avantage               | Impact sur le code                                                                                 |
| :--------------------- | :------------------------------------------------------------------------------------------------- |
| **Sécurité de typage** | Empêche d'accéder à `attackValues` sur une carte "Action" par erreur.                              |
| **Lisibilité**         | `if (isCharacter(card))` est plus expressif que `if (card.cardType === 'character')`.              |
| **Maintenance**        | Si la structure des types change, il suffit de modifier le guard ici pour corriger tout le moteur. |

---

## Exemple d'utilisation

```typescript
import { isCharacter } from "./cardGuards";

function printStats(card: Card) {
  if (isCharacter(card)) {
    // Ici, TypeScript sait que card possède 'attackValues'
    console.log(`Attaque : ${card.attackValues}`);
  } else {
    console.log("Cette carte n'a pas de statistiques de combat.");
  }
}
```
