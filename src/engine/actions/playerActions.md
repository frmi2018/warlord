# Documentation de playerActions.ts

## Localisation

`src/engine/actions/playerActions.ts`

## Rôle du fichier

Ce fichier définit le type **`PlayerAction`**. Il s'agit d'une **Union Discriminée** (Discriminated Union), une structure TypeScript qui liste de manière exhaustive toutes les commandes qu'un joueur peut envoyer au moteur de jeu.

Chaque action possède un champ `type` qui sert d'identifiant unique pour permettre au `gameReducer` de savoir quelle logique appliquer.

---

## Les Catégories d'Actions

### 1. Le Système de Combat (Scindé)

Le combat n'est pas atomique car il nécessite un jet de dés (UI) entre le moment où l'on attaque et le moment où l'on résout les dégâts.

- **`spendAttacker`** : L'attaquant est incliné (`spent`), la cible est désignée.
- **`resolveAttack`** : Applique les blessures finales après le jet de dés.
- **`cancelAttack` / `finishAttack`** : Gèrent les interruptions ou la clôture propre de la séquence de combat.

### 2. Mouvements et Déploiement

- **`deploy` / `deployCharacter`** : Gèrent l'entrée en jeu d'un personnage.
- **`maneuver`** : Déplacement volontaire d'une unité entre les rangs ou les colonnes.
- **`fallForward`** : Action corrective automatique qui fait avancer une unité lorsqu'un vide se crée devant elle.

### 3. Gestion des Objets et Actions

- **`equip`** : Attache un objet (Item) à un personnage.
- **`playAction`** : Joue une carte d'Action spéciale via un personnage "performer".

### 4. Système de Réactions (Sprint R.2)

Actions utilisées spécifiquement pendant une fenêtre de réaction (`reactWindow`).

- **`react`** : Joue une capacité (Feat) ou une carte en réponse à un trigger. Peut inclure un résultat de dé (`roll`).
- **`reactPass`** : Le joueur choisit de ne pas réagir, permettant à la fenêtre de se fermer.

### 5. Flux de Jeu

- **`pass`** : Termine le tour actuel du joueur pendant la phase principale.

---

## Pourquoi une Union Discriminée ?

Cette structure garantit la **sécurité du code** :

1.  **Auto-complétion** : L'IDE sait exactement quelles propriétés sont disponibles dès que vous avez choisi le `type`.
2.  **Exhaustivité** : Elle permet au moteur de vérifier que chaque type d'action est bien traité, évitant ainsi d'oublier de gérer une règle de jeu.

---

## Variables et Propriétés Communes

| Propriété               | Type      | Description                                              |
| :---------------------- | :-------- | :------------------------------------------------------- |
| `cardId` / `attackerId` | `string`  | L'identifiant de la carte qui réalise l'action.          |
| `targetId`              | `string`  | L'identifiant de la cible (pour une attaque ou un sort). |
| `slotIndex`             | `number`  | La position précise sur le plateau (colonne).            |
| `hasLoyaltyPenalty`     | `boolean` | Indique si le coût de déploiement est augmenté.          |

---

## Résumé

Ce fichier est le **contrat d'interface** entre l'interface utilisateur (React) et le moteur de jeu.

- Il centralise **toutes les intentions** possibles du joueur.
- Il permet une **migration vers le pipeline `applyAction`**, éliminant les modifications directes de l'état dans les composants UI.
- Il supporte le système complexe de **réactions en chaîne** et de **combat en deux étapes**.
