# reactSystem.test.ts

## Localisation

`src/__tests__/reactSystem.test.ts`

## Rôle du fichier

Ce fichier est le banc d'essai complet du **Système de Réactions (Reacts)**, l'un des aspects les plus tactiques de _Warlord_. Il valide le cycle de vie d'une "fenêtre de réaction" : de sa détection (qu'est-ce qui déclenche quoi ?) à sa résolution (quels sont les effets sur le jeu ?), en passant par l'arbitrage entre les joueurs.

Ces tests garantissent que les **Feats** (prouesses) des personnages s'activent au bon moment et respectent les limitations de fréquence (ex: une fois par tour).

---

## Cycle de Vie de la Fenêtre React

Le système fonctionne comme un dialogue entre les joueurs :

1.  **Ouverture** : `opensReactWindow` détecte si une action (ex: une attaque) permet à quelqu'un de réagir.
2.  **Arbitrage** : `buildReactWindow` donne la priorité au joueur ayant l'initiative.
3.  **Rotation** : `advanceReactWindow` et `applyReactPass` gèrent le passage de main. La fenêtre ne se ferme que lorsque **les deux joueurs ont passé consécutivement**.
4.  **Exécution** : Si un joueur réagit, le système applique l'effet et réévalue si d'autres réactions sont possibles.

---

## Feats (Prouesses) Validés

Le fichier teste individuellement la logique de chaque talent spécial :

| Feat            | Type   | Condition de déclenchement                       | Effet en cas de succès                                     |
| :-------------- | :----- | :----------------------------------------------- | :--------------------------------------------------------- |
| **Defend**      | After  | Un allié **adjacent** est ciblé par une attaque. | L'attaque est **redirigée** sur le protecteur.             |
| **Riposte**     | After  | Le personnage vient de subir une attaque.        | Inflige une **blessure immédiate** à l'assaillant.         |
| **Powerattack** | Before | Le personnage déclare une attaque.               | Ajoute +1 blessure (**Extra Wounds**) si l'attaque touche. |
| **Stealth**     | After  | Le personnage est ciblé par une frappe.          | Permet d'esquiver (limité à **une fois par tour**).        |

---

## Points Clés de la Logique Tactique

### 1. Adjacence et Positionnement

Pour le Feat **Defend**, le test valide rigoureusement la géométrie du plateau : le protecteur doit être sur le même rang (`posY`) et sur une case voisine (`posX`) de la victime pour intervenir.

### 2. Jets de Compétence (Skill Checks)

Chaque réaction nécessite un succès contre une difficulté fixe (DC 20). Les tests simulent :

- **Le Succès** : Le bonus de `skill` + le jet de dé permet d'atteindre 20.
- **L'Échec** : Si le total est insuffisant, la réaction est consommée mais l'effet ne se produit pas.

### 3. Gestion du "Once per Turn"

Le système suit l'utilisation des talents via `reactUsedThisTurn`. Le test **Stealth** vérifie que si un personnage a déjà utilisé sa compétence, elle n'apparaît plus dans la liste des actions disponibles (`getAvailableReacts`).

---

## Résumé Technique

- **Isolation** : Tests 100% logique pure, sans dépendance à l'interface graphique.
- **Immuabilité** : Chaque étape produit un nouvel état de jeu (`newState`) sans modifier l'ancien.
- **Conformité** : Implémente les règles de la 4e édition sur les fenêtres d'opportunité.

---
