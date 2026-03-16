# Documentation de applyDeploy.ts

## Localisation

`src/engine/actions/handlers/applyDeploy.ts`

## Rôle du fichier

Ce fichier contient la logique permettant de **déployer un personnage** depuis la main du joueur vers ses rangs (le plateau de jeu). Il fait le pont entre l'intention du joueur (l'action `deploy`) et les modifications effectives de l'état du jeu (`GameState`).

Il assure trois missions critiques :

1. **Validation** : Vérifier que la carte existe et que le placement est légal.
2. **Exécution** : Calculer la nouvelle position et appliquer d'éventuelles pénalités (Loyauté).
3. **Notification** : Informer le reste du moteur (via l'Event Bus) qu'une unité est entrée en jeu.

---

## Fonctionnement de l'Action

### `applyDeploy(state, action)`

La fonction suit un flux linéaire et sécurisé :

1.  **Recherche de la carte** : Elle cherche la carte dans `state.playerCards`. Si elle ne la trouve pas, elle lance une erreur pour stopper l'exécution (fail-fast).
2.  **Vérification des règles** : Elle utilise le helper `canDeploy` pour valider si le rang et la position sont autorisés (en tenant compte des règles de cohésion et du niveau de la carte).
3.  **Mise à jour des cartes** : Elle délègue le calcul mathématique du placement à `deployCharacter`. C'est ici que la carte passe de la zone `playerHand` à `playerRanks`.
4.  **Gestion de la loyauté** : Si `deployInfo.hasLoyaltyPenalty` est vrai, la carte est déployée avec une pénalité (souvent un coût supplémentaire ou une orientation spécifique).
5.  **Bus d'événements** : Elle déclenche l'événement `unit_deployed`. C'est ce qui permet, par exemple, d'activer des capacités "Quand ce personnage arrive en jeu...".

---

## Dépendances clés

- **`deployCharacter`** : Le moteur qui calcule les nouvelles coordonnées (`posX`, `posY`) et change la zone de la carte.
- **`canDeploy`** : Le gardien des règles (vérifie les limites de rangs et de niveau).
- **`globalEventBus`** : Le système de communication qui propage l'information du déploiement aux autres modules du jeu.

---

## Variables et Paramètres

| Paramètre           | Type      | Description                                            |
| :------------------ | :-------- | :----------------------------------------------------- |
| `action.cardId`     | `string`  | L'identifiant unique de la carte à poser.              |
| `action.posX`       | `number`  | La colonne choisie sur le plateau.                     |
| `action.rank`       | `number`  | Le rang visé (équivalent à `posY`).                    |
| `hasLoyaltyPenalty` | `boolean` | Indique si le déploiement est "forcé" ou hors-faction. |

---

## Résumé

Ce fichier garantit que :

- Un joueur ne peut pas tricher sur le placement de ses unités.
- Le passage de la main au plateau est **atomique** (tout se passe bien ou rien ne change).
- Les **effets de bord** (capacités spéciales au déploiement) sont correctement amorcés via l'Event Bus.
