# Documentation de savedDeck.ts

## Localisation

`src/types/savedDeck.ts`

## Rôle du fichier

Ce fichier définit les **contrats de données** pour la persistance des decks. Il sépare la logique de stockage (base de données Supabase ou localStorage) de la logique de jeu en direct.

L'objectif est de garantir que n'importe quel deck sauvegardé possède toutes les informations nécessaires pour être reconstruit par le moteur de jeu, sans dépendre d'objets complexes ou circulaires.

---

## Structures de Données

### 1. StartingFormation

Représente la disposition tactique initiale des troupes sur le plateau de jeu.

- **`warlordId`** : Le pivot de l'armée.
- **`rank1`** : Un tuple strict de 3 IDs (Niveau 1).
- **`rank2`** : Un tuple strict de 2 IDs (Niveau 2).
- _Note : L'utilisation de tuples `[string, string...]` au lieu de simples tableaux assure que la formation est toujours complète selon les règles du jeu._

### 2. SavedDeck

C'est l'objet complet tel qu'il existe dans votre stockage permanent.

- **Identifiants** : Gère à la fois l'ID unique du deck et l'ID de l'utilisateur (pour le support multi-joueurs/cloud).
- **Métadonnées** : Inclut le nom du deck, la faction et la date de création pour l'affichage dans les menus.
- **`cardIds`** : Une liste plate de 20 identifiants de base. C'est l'inventaire brut du deck.

### 3. CreateDeckPayload

Une version allégée du deck, optimisée pour l'envoi vers une API ou une base de données (ommettant les champs générés automatiquement comme `id` ou `createdAt`).

---

## Pourquoi cette architecture ?

| Concept                            | Bénéfice                                                                                                                                                                    |
| :--------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Séparation des responsabilités** | Contrairement aux types d'interface (UI), ces types sont optimisés pour être **sérialisables** (faciles à transformer en JSON).                                             |
| **Typage strict des rangs**        | En définissant `rank1` comme un tableau de 3 éléments, on empêche le stockage d'une formation invalide.                                                                     |
| **Indépendance du moteur**         | Le stockage ne sauvegarde que les `baseIds` (ex: "sots-2"). Les propriétés dynamiques (PV actuels, position X/Y, orientation) ne sont calculées qu'au moment du chargement. |

---

## Cycle de vie d'un deck

1. **Création** : Le joueur choisit ses cartes → `CreateDeckPayload`.
2. **Stockage** : Enregistrement dans Supabase → `SavedDeck`.
3. **Chargement** : Récupération du JSON → `SavedDeck`.
4. **Activation** : Passage dans `buildDeckFromSaved()` → `Card[]` (Objets de jeu).

---

## Résumé

Ce fichier est le **socle de la persistance**. Il garantit que les armées des joueurs sont stockées de manière légère, cohérente et robuste, permettant une synchronisation parfaite entre le stockage cloud et le moteur de combat.
