# Documentation de globalHandlers.ts

## Localisation

`src/engine/effects/globalHandlers.ts`

## Rôle du fichier

Ce fichier gère les **effets à l'échelle du plateau** (auras globales). Contrairement aux effets passifs classiques qui ne concernent que la carte elle-même ou ses voisins immédiats, les `globalHandlers` écoutent les événements majeurs du jeu pour activer ou désactiver des bonus qui s'appliquent à toute une catégorie d'unités (ex: tous les Nains).

Il assure la persistance de ces bonus dans l'état global du jeu (`GameState`) et dans un objet de référence rapide (`globalBonuses`).

---

## Concept : Les Auras d'Événements

Le système repose sur l'observation du cycle de vie des cartes clés :

1.  **Entrée en jeu** (`unit_deployed`) : Si une unité légendaire (comme King Xod) arrive sur le plateau, son aura s'active.
2.  **Sortie de jeu** (`unit_died`) : Si cette unité meurt ou quitte le plateau, son aura doit être immédiatement retirée pour éviter que le bonus ne persiste "fantôme".

---

## Cas d'école : King Xod (sots-20)

Le fichier implémente actuellement la règle spécifique du Roi Xod :

- **Effet** : Accorde un bonus de +1 au niveau effectif des Nains pour l'équipement d'objets.
- **Logique** :
  - Au déploiement de `sots-20`, `dwarfEquipLevelBonus` passe à 1.
  - À la mort de `sots-20`, `dwarfEquipLevelBonus` repasse à 0.

---

## Fonctions principales

### `registerGlobalHandlers()`

La fonction d'initialisation. Elle doit être appelée au lancement du moteur pour brancher les "oreilles" du bus d'événements sur les fonctions de détection.

### `handleUnitDeployed` / `handleUnitDied`

Ces fonctions reçoivent l'événement et l'état actuel :

1. Elles vérifient l'identité de la carte concernée via `isKingXod`.
2. Elles mettent à jour l'objet singleton `globalBonuses` (pour un accès synchrone hors-reducer).
3. Elles retournent un `Partial<GameState>` pour mettre à jour l'état immuable de l'application.

---

## Variables et Types importants

| Élément          | Description                                                                 |
| :--------------- | :-------------------------------------------------------------------------- |
| `globalEventBus` | Le système de messagerie qui propage les actions (mort, déploiement).       |
| `globalBonuses`  | Un objet de stockage global pour les bonus actifs.                          |
| `isKingXod`      | Helper utilisant une comparaison de chaîne (`startsWith`) sur l'ID de base. |

---

## Interaction avec d'autres fichiers

- `eventBus.ts` : Fournit l'infrastructure de souscription.
- `gameState.ts` : Reçoit les mises à jour des bonus dans sa propriété `bonuses`.
- `applyEquip.ts` : Consultera ces bonus pour savoir si un Nain peut porter une armure de haut niveau.

---

## Résumé

Ce fichier est responsable de :

- **Surveiller le champ de bataille** pour les unités à forte influence.
- **Activer dynamiquement les auras** et bonus d'armée.
- **Garantir le nettoyage** des bonus lorsqu'un leader tombe au combat.
  """
