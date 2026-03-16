# Documentation de canAddCard.ts

## Localisation

`src/utils/canAddCard.ts`

## Rôle du fichier

Ce fichier contient la **logique de validation unitaire** pour la construction d'un deck. Il agit comme un filtre de conformité qui vérifie, avant chaque ajout, si une carte spécifique respecte les limitations quantitatives imposées par les règles de _Warlord_ (4e Édition).

Il sert de garde-fou aussi bien pour le générateur automatique de deck que pour un éventuel futur éditeur de deck manuel.

---

## Règles de Validation

La fonction `canAddCard` s'appuie sur deux piliers de deck-building :

### 1. La Règle d'Unicité

Si la carte possède le trait `unique` (vérifié via `isCardUnique`) :

- **Condition** : La carte ne doit pas déjà exister dans le deck.
- **Limite** : Strictement **1 exemplaire** par armée.
- _Note : Les Warlords sont, par définition, toujours uniques._

### 2. La Limite par Exemplaire (`MAX_COPIES_PER_CARD`)

Pour toutes les autres cartes (troupes de base, objets communs, actions) :

- **Condition** : Le nombre d'exemplaires déjà présents doit être inférieur au plafond global.
- **Limite** : Généralement fixée à **3 exemplaires** (selon la constante `MAX_COPIES_PER_CARD` définie dans les règles du moteur).

---

## Fonctionnement technique : `canAddCard()`

La fonction utilise une structure de données performante (`Map`) pour effectuer ses vérifications en temps constant.

| Paramètre | Type                  | Description                                                                                            |
| :-------- | :-------------------- | :----------------------------------------------------------------------------------------------------- |
| `card`    | `BaseCard`            | L'objet carte que l'on souhaite ajouter.                                                               |
| `counts`  | `Map<string, number>` | Un dictionnaire recensant le nombre d'occurrences actuelles de chaque `card.id` dans le deck en cours. |

**Logique de retour :**

- `true` : L'ajout est autorisé.
- `false` : Le quota est atteint, l'ajout est rejeté.

---

## Pourquoi cette architecture ?

- **Performance** : L'utilisation d'une `Map` de compteurs évite de devoir reparcourir tout le tableau de cartes à chaque nouvel ajout (optimisation $O(1)$).
- **Centralisation** : En isolant cette règle dans un utilitaire, on garantit que le joueur et l'IA sont soumis aux mêmes restrictions strictes.
- **Réutilisation** : Ce même utilitaire pourra être utilisé plus tard pour empêcher un joueur de mettre trop de copies d'une carte puissante dans son deck personnalisé.

---

## Résumé

Ce fichier est le **vigile du deck-building**. Il garantit que chaque armée respecte les limites d'unicité et de quantité, préservant ainsi l'équilibre du jeu et la rareté des personnages légendaires.
