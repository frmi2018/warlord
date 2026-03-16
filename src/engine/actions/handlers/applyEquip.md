# Documentation de applyEquip.ts

## Localisation

`src/engine/actions/handlers/applyEquip.ts`

## Rôle du fichier

Ce fichier gère la logique d'**équipement d'un objet (Item)** sur un personnage. Il transforme l'intention du joueur ("Je veux mettre cette épée sur ce guerrier") en une modification concrète des données dans le `GameState`.

Il assure la liaison entre le moteur d'équipement et le système d'événements global du jeu.

---

## Fonctionnement de l'Action

### `applyEquip(state, action)`

La fonction suit un processus en trois étapes :

1.  **Exécution de l'équipement** : Elle appelle `applyEquipItem`. Ce helper est responsable de :
    - Vérifier si l'objet et le personnage existent.
    - Attacher l'objet au personnage (souvent en mettant l'ID du personnage dans une propriété de l'objet).
    - Gérer le déplacement de la carte (de la main vers le personnage).
    - Définir la zone de repli en cas de destruction (ici `playerDiscard`).

2.  **Mise à jour de l'état** : Elle crée une copie du `GameState` avec la nouvelle liste de cartes `playerCards` mise à jour.

3.  **Notification via l'Event Bus** :
    - Elle recherche l'objet et le personnage dans le nouvel état.
    - Si les deux sont trouvés, elle émet l'événement `item_equipped`.
    - Cet événement est crucial pour déclencher des capacités passives (ex: "Chaque fois que vous équipez un objet, piochez une carte").

---

## Dépendances clés

- **`applyEquipItem`** : Le moteur logique qui gère les règles strictes d'attachement des cartes d'objets.
- **`globalEventBus`** : Le système qui permet aux autres parties du code (comme les capacités spéciales ou l'interface) de savoir qu'un équipement a eu lieu.

---

## Variables et Paramètres de l'Action

| Paramètre           | Type     | Description                                          |
| :------------------ | :------- | :--------------------------------------------------- |
| `itemId`            | `string` | L'identifiant unique de la carte Objet à équiper.    |
| `targetCharacterId` | `string` | L'identifiant du personnage qui va recevoir l'objet. |

---

## Interaction avec le pipeline

Ce handler est conçu pour être intégré dans `applyAction.ts`. Il respecte le principe d'immuabilité : il ne modifie jamais le `state` original mais retourne toujours une nouvelle version modifiée.

---

## Résumé

Ce fichier garantit que :

- L'action d'équiper un objet est **proprement enregistrée** dans le moteur.
- Les **conséquences secondaires** (capacités déclenchées) sont activées via l'Event Bus.
- Le cycle de vie de l'objet est géré (notamment sa destination finale en défausse).
