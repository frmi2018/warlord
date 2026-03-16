# compileDSL.test.ts

## Localisation

`src/__tests__/compileDSL.test.ts`

## Rôle du fichier

Ce fichier teste le cœur du **moteur d'effets** de Warlord : le compilateur DSL (_Domain Specific Language_). Plutôt que de coder chaque compétence de carte manuellement en JavaScript (ce qui est source d'erreurs), le projet utilise des schémas de données simples (les patterns) que ce compilateur transforme en logique de jeu réelle.

Ces tests garantissent que chaque pattern (ex: "gagner un bonus si adjacent à X") génère un comportement rigoureusement conforme aux règles du jeu, de manière isolée et prévisible.

---

## Patterns DSL Testés

### 1. Adjacence (`BONUS_IF_ADJACENT`)

C'est le pattern le plus utilisé (notamment pour les Nains). Les tests vérifient :

- **Détection de proximité** : Le bonus s'active uniquement si la cible nommée (ex: "Hammer") est sur le même rang (`posY`) et sur une position horizontale consécutive (`posX`).
- **Cumul de puissance** : Si un personnage est entouré de deux alliés requis, le bonus doit doubler (ex: 2 Hammers = +4 ATK).
- **Types de stats** : Le compilateur doit correctement mapper "ATK" vers `attackBonus` et "AC" vers `armorBonus`.

### 2. Positionnement Tactique (`BONUS_IF_RANK` / `FRONT_RANK`)

Certains personnages sont plus forts selon leur ligne :

- **Rang spécifique** : Vérifie que le bonus ne s'active que si la carte est exactement au rang demandé (ex: Rang 2).
- **Ligne de front** : Valide que le bonus de défense s'applique uniquement si la carte est au rang frontal (`posY: 0`).

### 3. Actions Spéciales (`DRAW_CARD_OF_TYPE` / `MOVE_FORWARD`)

Ici, on teste les **Effets Actifs** (ceux qui nécessitent de "dépenser" la carte) :

- **Conditions d'usage** : La fonction `canUse()` doit bloquer l'action si la carte est déjà épuisée ou si le mouvement est impossible (ex: déjà au premier rang).
- **Exécution** : Vérifie que l'action consomme bien l'unité (passage en `spent`) et renvoie le message d'instruction correct au moteur de jeu (ex: `SEARCH_DECK_FOR_ITEM:5`).

---

## Tests de Régression : Axe & Hammer

Un point d'honneur est mis sur la migration des cartes iconiques **Axe** et **Hammer**. Les tests prouvent que la nouvelle version "compilée" via le DSL se comporte exactement comme l'ancienne version codée à la main. Cela garantit une transition transparente pour les joueurs tout en simplifiant la maintenance du code.

---

## Résumé Technique

- **Architecture** : Teste des fonctions pures (`apply`, `execute`) sans effets de bord.
- **Flexibilité** : Permet d'ajouter de nouveaux patterns DSL en toute confiance.
- **Fiabilité** : Utilise des helpers comme `makeCard` pour simuler des états de plateau miniatures et précis.

---
