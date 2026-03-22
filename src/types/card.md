# Documentation de card.ts (Types)

## Localisation

`src/types/card.ts`

## Rôle du fichier

Ce fichier définit les **interfaces fondamentales des cartes** telles qu'elles existent en mémoire durant une partie. Il distingue la donnée statique (issue de la base de données) de la donnée dynamique (état sur le plateau).

---

## Structure des Types

- **`DeckCard`** : La structure de base "morte" (nom, faction, stats de base).
- **`RuntimeFields`** : Champs ajoutés lorsque la carte est en jeu (ID unique, zone actuelle, orientation, blessures).
- **`CharacterCard` / `ItemCard` / `ActionCard`** : Spécialisations via une union discriminée (`cardType`).

---

## Logique d'Orientation et d'État

Le fichier définit les états physiques d'une carte sur le plateau :

- **`ready`** : Verticale, prête à agir.
- **`spent`** : Inclinée à 90°, a déjà agi.
- **`stunned`** : Retournée à 180°, ne peut pas agir au prochain tour.

---

## Capacités Actives des Objets

L'interface `ItemCard` inclut optionnellement une fonction `execute()`. C'est cette fonction qui permet aux objets de déclencher des actions spécifiques (comme un arc tirant à distance) plutôt que de simples bonus passifs de statistiques.

---

## Résumé

Le `card.ts` est responsable de :

- **Définir l'anatomie** d'une carte en jeu.
- **Gérer les énumérations** critiques (factions, zones, orientations).
- **Permettre le "Type Narrowing"** (distinction automatique entre un personnage et un objet via TypeScript).
