# types.ts (AI Priorities)

## Localisation

`src/ai/priorities/types.ts`

## Rôle du fichier

Ce fichier définit le **contrat technique** et les structures de données utilisés par l'ensemble du système décisionnel de l'IA. Il assure que toutes les règles de priorité (de P0 à P5) parlent le même langage et retournent des formats d'actions cohérents.

Il sert de fondation de typage pour garantir que le moteur de jeu peut interpréter sans erreur les intentions de l'IA.

---

## Les Piliers du Typage IA

### 1. `AiGameState`

Une version simplifiée et focalisée de l'état du jeu. L'IA n'a besoin de connaître que la position et l'état des cartes (IA et Joueur) pour prendre ses décisions. Cela permet d'isoler la logique de décision du reste de l'application (comme l'UI ou les paramètres sonores).

### 2. `AiAction` (L'Union des Possibilités)

C'est le type le plus important du fichier. Il liste de manière exhaustive tout ce que l'IA a le droit de faire pendant son tour :

- **`attack`** : Désigne un attaquant et une cible.
- **`playCard`** : Pose une nouvelle unité sur le plateau.
- **`maneuver`** : Déplace une unité déjà présente (via les règles de manœuvre).
- **`equipItem`** : Attache un objet à un personnage.
- **`playActionCard`** : Utilise une carte d'événement (ex: _Charge_) sur une cible spécifique.
- **`pass`** : Met fin au tour de l'IA.

### 3. `AiPriorityMeta`

Ce type est dédié à la **transparence** et au **debug**. Chaque règle de priorité possède :

- `priorityNumber` : Son rang dans la chaîne (0 à 5).
- `priorityReason` : Une phrase en langage naturel expliquant le "pourquoi" du coup (ex: _"J'attaque le Warlord car il est à découvert"_).

---

## Architecture de Flux

Le système fonctionne comme un entonnoir :

1. Une règle (`AiPriorityRule`) reçoit le `AiGameState`.
2. Elle renvoie soit une `AiAction`, soit `null` si la règle n'est pas applicable.
3. Si une action est choisie, elle est transformée en `AiActionWithMeta` pour être envoyée au moteur de jeu avec ses explications.

---

## Avantages de cette Structure

- **Extensibilité** : Pour ajouter une nouvelle capacité à l'IA (ex: lancer un sort), il suffit d'ajouter un nouveau type d'action dans l'union `AiAction`.
- **Sûreté (Type-Safety)** : Grâce à TypeScript, il est impossible pour une règle d'IA de retourner une action mal formée (ex: une attaque sans cible).
- **Facilité de Test** : Comme les types sont purs et déconnectés de React, on peut tester chaque règle d'IA avec des données factices très facilement.

---

## Résumé

Ce fichier est responsable de :

- **Définir le dictionnaire des actions** possibles pour l'IA.
- **Standardiser les entrées/sorties** de chaque règle de priorité.
- **Permettre le traçage** des décisions via les métadonnées de debug.
- **Assurer la cohérence** des données entre le "cerveau" de l'IA et le "corps" (le moteur de jeu).
