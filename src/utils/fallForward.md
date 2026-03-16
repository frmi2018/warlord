# Documentation de fallForward.ts

## Localisation

`src/utils/fallForward.ts`

## Rôle du fichier

Ce fichier implémente la règle de **"Fall Forward"** (Chute en avant). Dans _Warlord_, c'est le mécanisme de correction automatique ou forcé lorsqu'une formation devient illégale (par exemple, si trop de personnages au premier rang ont été éliminés, laissant le deuxième rang plus nombreux).

Plutôt que de simplement interdire le jeu, le système autorise le mouvement mais "punit" physiquement les troupes qui doivent se précipiter vers l'avant pour combler les trous, simulant le chaos d'un champ de bataille.

---

## Logique de Progression et Fatigue

Lorsqu'une carte "tombe en avant", elle avance d'un rang ($posY - 1$) mais subit un malus d'orientation représentant l'effort fourni :

### 1. Dégradation de l'état

La fonction `getNewOrientation` gère la transition d'état :

- **`ready` → `spent`** : Le personnage court vers l'avant et utilise son action pour ce tour.
- **`spent` → `stunned`** : Le personnage était déjà fatigué ; il arrive sur la ligne de front totalement désorienté (étourdi).
- **`stunned` → `stunned`** : Si la carte était déjà étourdie, elle le reste (pas de double étourdissement).

### 2. Positionnement Horizontal

La fonction `getNextPosXInRank` assure que la carte qui tombe ne se superpose pas à une unité déjà présente.

- **Comportement** : La carte est placée par défaut à l'extrême droite du nouveau rang (`maxPosX + 1`).

---

## Fonction Principale : `fallForward()`

Cette fonction est appelée soit par le joueur (choix de l'unité à sacrifier), soit par l'IA pour corriger sa formation.

- **Sécurité** : Elle vérifie que la carte n'est pas déjà au Rang 1 (front line), car il est impossible de "tomber" plus en avant que la première ligne.
- **Immuabilité** : Elle retourne une copie profonde du tableau de cartes, garantissant que React détecte le changement de position et d'orientation pour déclencher les animations.

---

## Pourquoi cette mécanique est-elle importante ?

| Concept               | Impact en jeu                                                                                                                                               |
| :-------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Gestion du risque** | Un joueur peut délibérément créer un rang illégal pour amener une unité puissante au front, au prix de son activation (`spent`).                            |
| **Effet Domino**      | Si le Rang 1 est vide, les unités du Rang 2 tombent au Rang 1, ce qui peut rendre le Rang 3 illégal par rapport au Rang 2, provoquant une chute en cascade. |
| **Réalisme**          | Empêche les unités de rester "en sécurité" à l'arrière si personne ne les protège devant.                                                                   |

---

## Résumé

Ce fichier garantit que :

- La **formation de combat** revient toujours vers un état légal.
- Le **coût tactique** de la réorganisation est appliqué (perte d'activation).
- Les **états de cartes** (`spent`, `stunned`) sont gérés de manière cohérente avec la 4e édition.
