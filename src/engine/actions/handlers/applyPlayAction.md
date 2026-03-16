# Documentation de applyPlayAction.ts

## Localisation

`src/engine/actions/handlers/applyPlayAction.ts`

## Rôle du fichier

Ce fichier est le **handler dédié à l'exécution des cartes d'action** (sorts, tactiques, ordres) jouées depuis la main du joueur.

Contrairement aux unités qui restent sur le plateau, les cartes d'action déclenchent un effet immédiat, sont souvent liées à un "performer" (le personnage qui lance l'action) et finissent généralement dans la défausse.

---

## Concept : Résolution des Actions

Le processus de résolution suit trois étapes clés :

1.  **Identification** : Trouver la carte jouée et le personnage qui l'exécute.
2.  **Exécution de l'Effet** : Appliquer la logique spécifique à la carte (ex: "Charge").
3.  **Notification** : Signaler au reste du système qu'une carte a été jouée pour déclencher d'éventuels triggers de réaction.

---

## Logique métier

### Cas spécial : "Charge"

La carte "Charge" possède sa propre logique complexe déportée dans `applyCharge.ts`. Elle modifie généralement l'état du personnage lanceur (mouvement et bonus d'attaque).

### Cas général (Fallback)

Pour les cartes dont l'effet n'est pas encore spécifiquement implémenté (TODO), le moteur assure une transition de base : la carte quitte la main pour rejoindre la **défausse** (`playerDiscard`). Cela garantit que la carte est consommée et ne reste pas indéfiniment en main.

---

## Fonctions principales

### `applyPlayAction(state, action): GameState`

- **Gestion de l'immuabilité** : Crée une copie du `newState` en mettant à jour le tableau `playerCards`.
- **Emission d'événement** : Appelle le `globalEventBus.emit` avec le type `card_played`. Cet événement est crucial pour les capacités passives qui réagissent aux sorts (ex: "Quand vous jouez une carte action, piochez 1").

---

## Variables et Types importants

| Élément         | Description                                                                       |
| :-------------- | :-------------------------------------------------------------------------------- |
| `cardId`        | L'identifiant de la carte action jouée.                                           |
| `performerId`   | L'ID du personnage qui lance l'action (nécessaire pour les coûts ou les portées). |
| `playerDiscard` | La zone de destination finale de la carte après résolution.                       |

---

## Interaction avec d'autres fichiers

Ce handler collabore étroitement avec :

- `applyCharge.ts` : Pour la résolution spécifique de la mécanique de charge.
- `eventBus.ts` : Pour informer les autres cartes de l'action effectuée.
- `actionReducer.ts` : Qui appelle ce handler lorsque le joueur ou l'IA joue une action.

---

## Résumé

Ce fichier est responsable de :

- **Traduire l'intention de jouer une carte** en modifications concrètes sur le plateau.
- **Gérer le cycle de vie** des cartes éphémères (Main -> Défausse).
- **Router les effets complexes** vers des sous-systèmes spécialisés.
  """
