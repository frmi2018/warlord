# Documentation de card.ts

## Localisation

`src/types/card.ts`

## Rôle du fichier

Ce fichier est la **constitution technique** du jeu. Il définit l'anatomie d'une carte sous toutes ses formes : de sa définition statique dans la base de données (`DeckCard`) à son état dynamique en plein combat (`Card`).

Il utilise le typage avancé de TypeScript (interfaces, unions discriminées, types littéraux) pour garantir qu'un personnage possède toujours des points de vie, tandis qu'une action possède toujours des effets.

---

## Hiérarchie des Types

L'architecture sépare strictement les données **statiques** (qui ne changent jamais) des données **runtime** (qui évoluent pendant la partie).

### 1. DeckCard (Le Plan)

C'est la structure stockée dans la base de données. Elle contient l'identité de la carte :

- **Faction & Couleurs** : Définit l'appartenance à un groupe (Dwarf, Elf, etc.) et la charte graphique associée (`factionColors`).
- **Stats de base** : Attaque, CA, HP, Traits.
- **Actions** : Définit si la capacité est un `order` (action principale), un `spend` (épuise la carte) ou un `react` (réponse).

### 2. RuntimeFields (L'État en jeu)

Ces propriétés n'existent que lorsqu'une partie est lancée. Elles traquent la vie de la carte sur le plateau :

- **`zone`** : Où se trouve la carte (Main, Deck, Rangs, Défausse).
- **`orientation`** : La carte est-elle prête (`ready`) ou inclinée (`spent`) ?
- **`posX` / `posY`** : Coordonnées précises dans la formation tactique.
- **`wounds`** : Compteur de blessures accumulées.

### 3. L'Union Discriminée `Card`

C'est le type le plus utilisé dans le code. TypeScript utilise le champ `cardType` pour "deviner" quelles propriétés sont disponibles :

| Type              | Spécificités                                                         |
| :---------------- | :------------------------------------------------------------------- |
| **CharacterCard** | Garantit la présence de `attackValues`, `armorClass` et `hitPoints`. |
| **ItemCard**      | Focus sur les bonus d'équipement (`attackBonus`, `armorBonus`).      |
| **ActionCard**    | Doit posséder au moins une capacité dans son tableau `actions`.      |

---

## Système de Factions et Visuels

Le fichier définit les 7 factions majeures de _Warlord_ 4e Édition, chacune liée à une couleur hexadécimale précise utilisée pour les bordures et les headers dans l'interface utilisateur.

---

## Pourquoi cette architecture ?

- **Sécurité au Runtime** : En rendant `hitPoints` obligatoire dans `CharacterCard` mais optionnel dans `DeckCard`, le compilateur vous empêche d'essayer d'infliger des dégâts à une carte "Action" par erreur.
- **Lisibilité** : Les développeurs savent immédiatement quels champs sont disponibles en vérifiant simplement le `cardType`.
- **Évolutivité** : Ajouter une nouvelle faction ou un nouvel état d'orientation se fait en une seule ligne, et TypeScript signalera immédiatement tous les endroits du code à mettre à jour.

---

## Exemple de Narrowing (Affinement de type)

```typescript
function getCombatPower(card: Card) {
  if (card.cardType === "character") {
    // Ici, TypeScript autorise l'accès à attackValues
    return Math.max(...card.attackValues);
  }
  return 0; // Les objets et actions n'ont pas de puissance d'attaque propre
}
```
