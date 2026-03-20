# Documentation de MinimapBoard.tsx

## Localisation

`src/components/MinimapBoard.tsx`

## Rôle du fichier

Le `MinimapBoard` est un **composant de navigation et de visualisation stratégique**. Il offre une vue d'ensemble simplifiée du champ de bataille, permettant au joueur de visualiser la disposition des unités (alliées et ennemies) sur les différents rangs sans l'encombrement visuel des cartes complètes.

C'est un composant de type "HUD" (Heads-Up Display) qui facilite le passage rapide d'un rang à l'autre dans l'interface principale.

---

## Fonctionnement : Organisation Spatiale

Le composant transforme les listes de cartes à plat en une structure hiérarchique par rangs pour refléter la topographie du jeu :

- **Groupement par Rang** : Utilise l'attribut `posY` des cartes pour les organiser dynamiquement.
- **Inversion des Axes** : Pour simuler un face-à-face, les rangs de l'IA sont affichés de manière inversée (le rang 1, le plus proche du joueur, se retrouve au centre de la minimap).
- **Représentation Abstraite** : Les cartes sont réduites à des rectangles colorés basés sur leur faction, avec des indicateurs visuels pour leur état (`spent`, `stunned`).

---

## Fonctionnalités Principales

### Visualisation de l'État

- **Code Couleur** : Applique les couleurs de faction (`factionColors`) pour identifier instantanément les groupes d'unités.
- **États d'Orientation** : Applique des classes CSS spécifiques si une unité est inclinée (épuisée) ou étourdie.
- **Indicateur de Focus** : Met en évidence le rang actuellement sélectionné par le joueur dans l'interface principale via la prop `isActive`.

### Interaction et Navigation

- **Focus au Clic** : Chaque petit rectangle d'unité est cliquable, déclenchant les fonctions `onPlayerRankFocus` ou `onAiRankFocus` pour recentrer la vue principale sur cette unité précise.
- **Survol Informatif** : L'attribut `title` affiche le nom de la carte au survol de la souris.

---

## Structure des Props

Le composant reçoit les données nécessaires pour reconstruire la scène :

- **`playerCards` / `aiCards`** : Les tableaux complets d'objets `Card`.
- **`activePlayerRank` / `activeAiRank`** : Les index des rangs actuellement actifs pour la gestion du style.
- **Callbacks de Focus** : Les fonctions permettant de remonter l'événement de sélection vers le composant parent.

---

## Concepts Techniques Clés

### Logique de Groupement

La fonction interne `groupByRank` assure le tri :

1. Filtre les cartes appartenant à la zone spécifique (`playerRanks` ou `aiRanks`).
2. Les regroupe dans une `Map` par coordonnée Y.
3. Trie les cartes à l'intérieur de chaque rang par coordonnée X pour garantir un affichage de gauche à droite cohérent.

### Calcul du Rendu Dynamique

Le composant calcule `getMaxRank` pour s'assurer qu'au moins 3 rangs sont toujours visibles, même si le plateau est vide, offrant ainsi une structure visuelle stable.

---

## Résumé

Le `MinimapBoard` est responsable de :

- **Synthétiser l'état complexe du plateau** en une vue minimaliste.
- **Permettre une navigation rapide** entre les différentes zones de combat.
- **Refléter visuellement la structure tactique** (face-à-face) du moteur de jeu.
