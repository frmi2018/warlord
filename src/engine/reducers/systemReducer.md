# Documentation de systemReducer.ts

## Localisation

`src/engine/reducers/systemReducer.ts`

## Rôle du fichier

Ce fichier est le **reducer de domaine** chargé de gérer les actions dites "système". Contrairement aux actions de combat ou de déploiement qui sont le fruit d'une décision stratégique (Joueur ou IA), les actions système servent à maintenir la cohérence de l'état du jeu ou à gérer des transitions automatiques.

Il s'assure que les règles structurelles du jeu sont appliquées, même sans intervention directe des joueurs.

---

## Concept : Maintenance Automatique

Le moteur de jeu utilise ce reducer pour résoudre des situations logiques ou structurelles :

1.  **Cohérence du plateau** : Si un personnage meurt, un trou se crée dans les rangs. La règle du "Fall Forward" oblige les unités derrière à avancer. Ce reducer traite le déplacement correctif.
2.  **Flux du tour** : Gère l'action technique de passer son tour (`pass`), ce qui peut déclencher un changement de phase ou de joueur actif.

---

## Actions gérées

### `fallForward`

Action déclenchée généralement par un hook de surveillance (`useIllegalRanks`) lorsqu'une unité occupe un rang illégal (ex: Rang 2 alors que le Rang 1 est vide).

- **Handler** : `applyFallForward`.
- **Effet** : Déplace physiquement la carte vers l'avant pour combler le vide.

### `pass`

Action technique indiquant que le joueur (ou l'IA) a fini ses interactions pour la phase actuelle.

- **Handler** : `applyPass`.
- **Effet** : Calcule le prochain joueur actif ou fait basculer la partie dans la phase suivante (ex: de la Decree Phase à la fin de tour).

---

## Variables et Types importants

| Élément               | Description                                                            |
| :-------------------- | :--------------------------------------------------------------------- |
| `SystemReducerAction` | Sous-type de `GameAction` filtrant uniquement `fallForward` et `pass`. |
| `applyFallForward`    | Logique de repositionnement des cartes sur l'axe Y (rangs).            |
| `applyPass`           | Logique de transition d'état global (phase / activePlayer).            |

---

## Interaction avec d'autres fichiers

Ce fichier est indispensable pour :

- `gameReducer.ts` : Qui lui délègue la gestion des correctifs de structure.
- `useIllegalRanks.ts` : Un hook qui détecte les anomalies de placement et dispatche `fallForward`.
- `applyPass.ts` : Contient la logique de fin de tour et de changement de phase.

---

## Résumé

Ce fichier est responsable de :

- **Garantir l'intégrité** du plateau de jeu (Règle du "Fall Forward").
- **Piloter la progression** logique du tour de jeu.
- **Isoler les actions automatiques** du moteur des actions volontaires des joueurs.
  """
