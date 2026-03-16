# Documentation de helpers.ts

## Localisation

`src/engine/effects/helpers.ts`

## Rôle du fichier

Ce fichier regroupe des **utilitaires de géométrie de plateau**. Il fournit la logique mathématique nécessaire pour déterminer les relations spatiales entre les cartes (principalement l'adjacence).

Ces fonctions sont cruciales pour les effets de type "Aura" ou "Synergie" (ex: "Gagne +1 Armure pour chaque allié adjacent"). Il s'agit de **fonctions pures** : elles ne modifient rien, ne dépendent d'aucun état global et sont donc parfaitement testables.

---

## Concept : La Grille de Coordonnées

Le plateau est géré par un système de coordonnées $posX$ (colonne) et $posY$ (rangée).

- **L'adjacence horizontale** est la seule forme d'adjacence gérée actuellement.
- Le système repose sur l'hypothèse que les $posX$ sont toujours compactés (0, 1, 2...) sans trous, une règle maintenue par les reducers de mouvement.

---

## Fonctions principales

### `areAdjacent(a, b): boolean`

Le coeur du calcul. Deux cartes sont considérées comme adjacentes si et seulement si :

1.  Elles partagent la même rangée ($a.posY = b.posY$).
2.  La distance absolue entre leurs colonnes est exactement de 1 ($|a.posX - b.posX| = 1$).

### `getAdjacentAllies(card, allies): Card[]`

Parcourt une liste de cartes alliées pour extraire celles qui touchent la carte de référence.

- _Note :_ Elle inclut une sécurité pour s'assurer que la carte ne se trouve pas elle-même "adjacente à elle-même".

### `getAdjacentEnemies(card, enemies): Card[]`

Similaire à la fonction précédente, mais appliquée à la liste adverse. Bien que les ennemis soient généralement sur des rangées différentes ($posY$), cette fonction est prête pour des mécaniques futures de contact ou de cartes "volantes/infiltrées".

---

## Pourquoi des helpers isolés ?

- **Réutilisabilité** : Ces fonctions sont utilisées à la fois par le moteur de combat, les passifs et l'IA.
- **Stabilité** : En isolant la logique spatiale, on évite de dupliquer les erreurs de calcul $Math.abs$ dans plusieurs fichiers.
- **Performance** : Les calculs sont des opérations $O(1)$ ou $O(n)$ très légères.

---

## Interaction avec d'autres fichiers

- `resolvePassiveEffects.ts` : Utilise ces helpers pour activer des bonus de groupe.
- `resolveActiveEffects.ts` : Peut vérifier si une cible est à portée de "contact".
- `applyManeuver.ts` : Bien qu'il ne les appelle pas directement, il garantit la validité des $posX$ sur lesquels ces helpers s'appuient.

---

## Résumé

Ce fichier est responsable de :

- **Calculer la proximité** entre les unités.
- **Extraire des listes de voisins** pour les calculs de bonus.
- **Garantir une définition unique** de l'adjacence dans tout le moteur.
  """
