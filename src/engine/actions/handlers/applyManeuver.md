# Documentation de applyManeuver.ts

## Localisation

`src/engine/actions/handlers/applyManeuver.ts`

## Rôle du fichier

Ce fichier gère la logique de **manœuvre**, c'est-à-dire le déplacement volontaire d'une unité d'un rang à un autre ou d'une position à une autre au sein de la formation.

Contrairement au "Fall Forward" qui est automatique, la manœuvre est une action délibérée qui consomme généralement l'activation de l'unité (elle devient `spent`).

---

## Concept : Déplacement et Formation

La manœuvre permet de réorganiser tactiquement l'armée. Une unité peut monter au front pour renforcer une ligne ou reculer pour se protéger.

---

## Fonctionnement de l'Action

### `applyManeuver(state, action)`

La fonction exécute les étapes suivantes :

1.  **Récupération de l'unité** : Elle identifie la carte dans `playerCards`. Si la carte n'existe pas, elle lève une erreur.
2.  **Préparation de la cible** : Elle construit un objet de type `ManeuverTarget`.
    - **Nouveauté T1.1.6** : Le `slotIndex` (la position horizontale dans le rang) est désormais dynamique et transmis par l'action, permettant un placement précis.
3.  **Délégation à l'Utilitaire** : Elle appelle `applyManeuverUtil` qui se charge de :
    - Mettre à jour les coordonnées `posX` et `posY`.
    - Marquer l'unité comme ayant bougé ce tour (`movedThisTurn`).
    - Passer l'unité en mode incliné (`spent`).
4.  **Émission d'événement** : Elle déclenche l'événement `rank_entered`. C'est essentiel pour les capacités de cartes qui s'activent lorsqu'une unité change de rang (ex: "Quand ce personnage entre au premier rang, gagnez +1 en attaque").

---

## Variables et Paramètres de l'Action

| Paramètre   | Type     | Description                                                     |
| :---------- | :------- | :-------------------------------------------------------------- |
| `cardId`    | `string` | L'identifiant unique de la carte qui effectue la manœuvre.      |
| `toRank`    | `number` | Le rang de destination (ex: 1 pour le front, 2 pour l'arrière). |
| `slotIndex` | `number` | La position spécifique (colonne) dans le rang cible.            |

---

## Interaction avec d'autres fichiers

- **`getManeuverTargets.ts` (Utils)** : Contient la logique complexe qui valide où une unité a le droit d'aller et applique les changements d'état (orientation `spent`).
- **`globalEventBus.ts`** : Permet de notifier le système du changement de rang pour déclencher des effets en cascade.
- **`playerActions.ts`** : Définit la structure de l'action `maneuver`.

---

## Résumé

Ce fichier garantit que :

- Les déplacements sont **précis** grâce à la gestion des slots indexés.
- L'unité est correctement **"épuisée"** après son mouvement.
- Le système de jeu est informé du **changement de position** pour activer d'éventuels bonus ou capacités spéciales.
