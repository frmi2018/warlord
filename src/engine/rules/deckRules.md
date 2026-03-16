# Documentation de deckRules.ts

## Localisation

`src/engine/rules/deckRules.ts`

## Rôle du fichier

Ce fichier définit les **règles de construction** et les **limites structurelles** du deck et de la formation de départ. Il centralise les constantes numériques et les fonctions de validation qui dictent comment une armée peut être composée avant le début d'une partie.

C'est ici que l'on définit ce qui rend une carte "Unique" et combien d'exemplaires d'une même carte un joueur a le droit d'inclure.

---

## Constantes de Configuration

Le fichier exporte plusieurs constantes qui servent de référence pour l'ensemble du moteur :

| Constante               | Valeur | Description                                          |
| :---------------------- | :----- | :--------------------------------------------------- |
| `MAX_DECK_SIZE`         | 20     | Nombre total de cartes autorisé dans un deck.        |
| `MAX_COPIES_PER_CARD`   | 4      | Limite standard de copies pour une carte non-unique. |
| `RANK1_FORMATION_COUNT` | 3      | Nombre d'unités requises au premier rang au départ.  |
| `RANK2_FORMATION_COUNT` | 2      | Nombre d'unités requises au second rang au départ.   |

---

## Logique de Validation

### `isCardUnique(card)`

Détermine si une carte est soumise à la règle de l'exemplaire unique.

- **Critères** : La carte est unique si elle possède la propriété `unique: true` dans la base de données OU si elle possède le trait **"Warlord"**.

### `getMaxCopies(card)`

Calcule le nombre maximum autorisé pour une carte spécifique.

- **Retour** : `1` si la carte est unique, sinon renvoie la constante globale `MAX_COPIES_PER_CARD` (4).
- **Extensibilité** : Cette fonction est prévue pour accueillir des règles de restriction intermédiaires (ex: cartes limitées à 2 exemplaires) sans modifier le reste du code.

---

## Pourquoi centraliser ces règles ?

1.  **Équilibrage facilité** : Si vous décidez de passer la taille du deck à 30 cartes, vous ne modifiez qu'une seule variable ici au lieu de chercher dans toute l'interface.
2.  **Cohérence UI/Engine** : Les composants de création de deck et le moteur de validation de partie utilisent la même source de vérité.
3.  **Respect du Lore** : Garantit qu'un joueur ne puisse jamais déployer deux fois le même Warlord ou un personnage nommé unique.

---

## Résumé

Ce fichier garantit que :

- La **structure des armées** est conforme aux règles de Warlord.
- Les **limites de copies** sont respectées lors de la composition du deck.
- Les **Warlords** sont traités nativement comme des entités uniques.
