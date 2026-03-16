# Documentation de deployReducer.ts

## Localisation

`src/engine/reducers/deployReducer.ts`

## Rôle du fichier

Ce fichier est un **reducer de domaine** spécialisé dans la gestion du déploiement des unités sur le plateau de jeu.

Il sert d'interface entre les différentes sources de déploiement (l'interface utilisateur, la logique interne du moteur ou l'IA) et les fonctions qui modifient réellement les listes de cartes dans l'état global.

---

## Concept : Les deux chemins de déploiement

Le moteur de jeu distingue deux manières de faire entrer un personnage en jeu :

1.  **Le chemin "Moteur" (`deploy` / `aiDeploy`)** : Utilisé lorsque les coordonnées précises (`rank` et `posX`) sont déjà connues. C'est le cas pour l'IA ou pour des effets de jeu automatiques.
2.  **Le chemin "UI" (`deployCharacter`)** : Utilisé lorsque le joueur interagit avec l'interface (comme une modale de déploiement). Ici, on utilise des index de slots et on gère des règles spécifiques comme la **pénalité de loyauté**.

---

## Actions gérées

### `deploy`

Action standard déclenchée par le moteur. Elle déplace une carte de la main vers les rangs à une position spécifique.

- **Handler** : `applyDeploy`.

### `deployCharacter`

Action déclenchée par l'interface utilisateur. Elle permet une transition propre entre la sélection d'une carte dans la main et son placement physique sur la grille de combat.

- **Handler** : `applyDeployCharacter`.

### `aiDeploy`

Action générée par l'intelligence artificielle.

- **Réutilisation** : Elle convertit les données de l'IA au format de l'action `deploy` standard pour réutiliser la même logique `applyDeploy`, garantissant une cohérence parfaite entre les règles appliquées à l'humain et à l'ordinateur.

---

## Variables et Types importants

| Élément             | Description                                                                 |
| :------------------ | :-------------------------------------------------------------------------- |
| `DeployAction`      | Sous-type de `GameAction` regroupant uniquement les actions de déploiement. |
| `rank` / `posX`     | Coordonnées de destination sur la grille de jeu.                            |
| `hasLoyaltyPenalty` | Booléen indiquant si le déploiement coûte plus cher (règle de loyauté).     |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `gameActions.ts` → Pour la définition des actions sources.
- `handlers/applyDeploy.ts` → Pour la logique de mouvement des cartes entre zones.
- `handlers/applyDeployCharacter.ts` → Pour la logique spécifique à l'interaction humaine.

Il est appelé par le **Reducer Central** (`gameReducer.ts`) dès qu'une action de type "deploy" est détectée.

---

## Résumé

Ce fichier est responsable de :

- **Unifier les sources de déploiement** (Joueur, IA, Moteur).
- **Router les actions** vers le bon traitement logique selon le contexte (UI ou Coordonnées).
- **Garantir l'immuabilité** de l'état lors de l'entrée en jeu de nouvelles unités.
  """
