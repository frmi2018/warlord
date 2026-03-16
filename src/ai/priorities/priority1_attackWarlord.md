# priority1_attackWarlord.ts

## Localisation

`src/ai/priorities/priority1_attackWarlord.ts`

## Rôle du fichier

Le fichier `attackWarlordPriority` définit la **stratégie de victoire immédiate** de l'IA. Dans Warlord, la partie se termine dès que le Warlord (Seigneur de Guerre) adverse est éliminé.

Par conséquent, si le Warlord du joueur commet l'erreur de se retrouver en première ligne (Rang 1), l'IA abandonne toute autre considération tactique (développement, renforcement) pour concentrer ses forces sur l'objectif principal. C'est la priorité la plus haute juste après l'utilisation des cartes Actions.

---

## Logique de Sélection de l'Attaquant

L'IA ne se contente pas de frapper avec la première unité venue ; elle utilise un algorithme de tri (`reduce`) pour maximiser ses chances d'éliminer le Warlord en un seul tour :

### 1a. Priorité au Volume de Frappes (_Multi-strike_)

L'IA choisit d'abord l'unité possédant le plus grand nombre de valeurs d'attaque (`strikeCount`).

- **Pourquoi ?** Plus il y a de dés lancés, plus les chances d'obtenir un "Natural 20" (coup critique) ou d'épuiser les points de vie restants du Warlord sont élevées.

### 1b. Priorité à la Puissance Brute (_ATK_)

En cas d'égalité sur le nombre de frappes (par exemple, deux combattants ayant chacun 2 attaques), l'IA compare leur bonus d'attaque pur. Elle sélectionne celui qui a la valeur la plus élevée pour surpasser la Classe d'Armure (AC) souvent élevée des Warlords.

### 1c. Engagement de la Ligne de Front

L'unité sélectionnée doit impérativement être "Ready" (prête) et située en première ligne (`getAiFrontlineAttackers`), car le Warlord au rang 1 n'est généralement atteignable qu'en mêlée.

---

## Concepts Clés

- **Instinct de Tueur** : Cette règle garantit que l'IA ne laissera jamais passer une opportunité de finir la partie.
- **Optimisation Statistique** : En combinant le nombre de frappes et le bonus d'attaque, l'IA calcule mathématiquement l'attaquant qui a la plus haute probabilité de succès.
- **Simplicité Tactique** : Contrairement aux priorités de combat standard (P3), l'IA ignore ici les risques de "Fall Forward" ou de contre-attaque. La mort du Warlord adverse annule tout risque futur.

---

## Interaction avec les Helpers

- **`getPlayerWarlordAtFront`** : Scanne le premier rang du joueur à la recherche d'une carte possédant le trait "Warlord".
- **`getStrikeCount`** : Compte le nombre d'attaques disponibles sur une carte.
- **`getCardAtk`** : Extrait la valeur d'attaque la plus élevée pour servir de bris d'égalité.

---

## Résumé

Ce fichier est responsable de :

- **Détecter l'opportunité de victoire** finale.
- **Sélectionner l'attaquant le plus efficace** selon une hiérarchie "Strikes > Bonus ATK".
- **Déclencher l'offensive fatale** pour terminer la partie.
