# Documentation de cards/index.ts

## Localisation

`src/data/cards/index.ts`

## Rôle du fichier

Ce fichier est le **cerveau de l'agrégation**. Depuis le Sprint 1.3, il est la source de vérité unique du projet. Son rôle est de collecter les exportations de chaque dossier de faction, de les fusionner et de distribuer les données sous deux formes distinctes selon les besoins du moteur ou de l'interface.

Il automatise la mise à jour de la base de données : dès qu'une carte est ajoutée dans son dossier faction, elle est instantanément disponible dans tout le projet.

---

## Architecture de Distribution

Le fichier expose deux constantes majeures, chacune ayant une responsabilité précise :

### 1. `allCards` (La Totale)

- **Type** : `CardDefinition[]`
- **Contenu** : Cartes statiques + Logique des effets.
- **Usage** : Utilisé exclusivement par le `effectRegistry` au démarrage pour lier les scripts de capacités aux cartes correspondantes.

### 2. `cardDatabase` (La Vue Légère)

- **Type** : `DeckCard[]`
- **Contenu** : Uniquement les données statiques (Stats, IDs, Images).
- **Traitement** : Le champ `effects` est filtré via un `map` pour respecter le type `DeckCard`.
- **Usage** : Utilisé pour la rétrocompatibilité, le rendu de l'interface utilisateur, et le générateur de deck. Cela évite de charger des fonctions logiques là où on n'a besoin que de texte ou d'images.

---

## Flux d'Agrégation des Données

Le système fonctionne comme un entonnoir inversé pour maximiser l'organisation :

1.  **Niveau Unitaire** : Chaque carte a son fichier (ex: `dwarf-soldier.ts`).
2.  **Niveau Faction** : Le fichier `index.ts` de chaque dossier (ex: `/dwarf/index.ts`) regroupe ses cartes.
3.  **Niveau Global** : Ce fichier (`cards/index.ts`) importe tous les index de factions.

---

## Pourquoi cette architecture ?

| Avantage              | Description                                                                                                                           |
| :-------------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| **Isolation du Type** | En filtrant `effects` pour `cardDatabase`, on garantit que l'UI ne manipule jamais accidentellement la logique interne des capacités. |
| **Scalabilité**       | Ajouter une nouvelle faction (ex: "The Chosen") se résume à ajouter une ligne d'import et une ligne dans le tableau `allCards`.       |
| **Propreté du code**  | Utilise le "destructuring" (`{ effects: _effects, ...card }`) pour transformer les objets proprement sans mutation.                   |

---

## Guide d'Entretien

> **Rappel de sécurité** : Ne jamais importer `allCards` dans un composant React. Utilisez toujours `cardDatabase` pour l'interface utilisateur afin de préserver les performances et la séparation des responsabilités.

---

## Résumé

`src/data/cards/index.ts` est la **gare de triage** de vos données. Il transforme une multitude de petits fichiers de cartes organisés par faction en deux flux de données standardisés, alimentant à la fois l'intelligence du moteur (effets) et la structure visuelle du jeu (stats).
