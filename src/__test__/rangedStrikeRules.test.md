# rangedStrikeRules.test.ts

## Localisation

`src/__tests__/rangedStrikeRules.test.ts`

## Rôle du fichier

Ce fichier contient les tests unitaires pour les **Ranged Strikes** (Attaques à distance), une mécanique fondamentale de _Warlord_. Contrairement au combat de mêlée qui cible le rang immédiatement adjacent, le ranged strike permet de frapper des ennemis plus éloignés sans s'exposer directement.

Ces tests suivent une approche **TDD** (_Test-Driven Development_) et se réfèrent strictement aux documents de règles officiels (4e édition) pour garantir une simulation fidèle du jeu.

---

## Règles de Portée (Range)

La règle d'or testée ici est : **"Un Ranged Strike cible un personnage situé exactement à deux rangs de distance."**

Le moteur de jeu utilise un système de coordonnées `posY` (1-indexé). La distance est calculée par l'addition des positions :

- **Portée Valide (Somme = 3)** :
  - Attaquant au **Rang 1** + Cible au **Rang 2** adverse.
  - Attaquant au **Rang 2** + Cible au **Rang 1** adverse.
- **Portée Invalide** :
  - Somme = 2 (Rang 1 -> Rang 1) : **Trop proche** (nécessite une attaque de mêlée).
  - Somme = 4 (Rang 2 -> Rang 2) : **Trop loin**.

---

## Fonctionnalités vérifiées

### 1. Détection des capacités (`hasRangedStrike`)

Vérifie que le moteur identifie correctement si une carte possède la capacité de tir. Une carte sans tableau `rangedStrikes` ou avec un tableau vide est considérée comme incapable de tirer.

### 2. Indépendance des statistiques

Un point crucial des règles est l'**isolation des bonus** :

- Les bonus d'attaque de mêlée (ATK) ou le nombre de frappes de mêlée n'influencent jamais les dégâts ou le nombre de tirs à distance.
- Le test garantit que ces deux systèmes de combat coexistent sur une même carte sans s'entrechoquer.

### 3. Filtrage des cibles (`getRangedStrikeTargets`)

Cette fonction est utilisée par l'interface utilisateur pour mettre en surbrillance les cibles valides. Les tests s'assurent que :

- On ne peut pas tirer sur ses propres troupes (**Feu ami interdit**).
- Seuls les ennemis au rang précis de portée (distance 2) sont retournés.
- Les cartes en main ou dans la défausse sont invisibles pour les archers.

---

## Résumé Technique

- **Calcul de distance** : $Attaquant_{posY} + Cible_{posY} = 3$.
- **Validation des frappes** : Supporte le multi-tir (ex: `[3, 1]`).
- **Symétrie** : Les règles s'appliquent de manière identique pour le joueur humain et pour l'IA.
