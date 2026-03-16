# Documentation de canManeuver.ts

## Localisation

`src/utils/canManeuver.ts`

## Rôle du fichier

Ce fichier utilitaire implémente la validation logique de l'action **Maneuver** (Manœuvre), l'un des ordres de base les plus importants dans _Warlord: Saga of the Storm_.

En accord avec les règles de la 4ème édition, une manœuvre permet à un personnage de se déplacer d'un rang (avant/arrière) ou de changer de position latérale au sein de son rang actuel, au prix de son activation ("Spend").

---

## Critères de Validation

Pour qu'un personnage puisse manœuvrer, il doit satisfaire **quatre conditions strictes** vérifiées par la fonction `canManeuver` :

### 1. Localisation (Zone)

Le personnage doit se trouver physiquement dans les rangs de combat (`playerRanks`). Les cartes en main, dans le deck ou dans la défausse ne peuvent évidemment pas manœuvrer.

### 2. État de préparation (Orientation)

L'action de manœuvre est un "Order" qui nécessite de dépenser le personnage :

- **`spent`** : Si la carte est déjà inclinée, elle a déjà agi et ne peut plus manœuvrer.
- **`stunned`** : Une unité étourdie est incapable d'obéir aux ordres de mouvement.
- **Seul l'état `ready` est autorisé.**

### 3. Limitation par tour

Conformément aux mécaniques de fluidité du jeu, un personnage ne peut pas passer son tour à danser sur le plateau.

- Le flag **`movedThisTurn`** doit être à `false`.
- Ce flag est réinitialisé à chaque phase de fin de tour via `executeEndOfTurnPhase`.

### 4. Gestion de l'illégalité des rangs

> [!NOTE]  
> Selon la règle officielle, si un mouvement crée un rang illégal (ex: trop de personnages au premier rang), le déplacement est **autorisé**, mais la formation doit être corrigée immédiatement après. Par conséquent, ce script ne bloque pas le mouvement pour des raisons de formation, laissant cette responsabilité au moteur de résolution des rangs.

---

## Structure de Retour (`ManeuverBlockReason`)

La fonction ne renvoie pas un simple booléen, mais un objet détaillé. Cela permet à l'interface utilisateur d'afficher des **infobulles explicatives** (Tooltips) lorsque le bouton de mouvement est grisé.

| Propriété     | Type             | Description                                            |
| :------------ | :--------------- | :----------------------------------------------------- |
| `canManeuver` | `boolean`        | `true` si toutes les conditions sont remplies.         |
| `reason`      | `string \| null` | Message explicatif en français (ex: "Carte étourdie"). |

---

## Pourquoi cette approche ?

- **UX (Expérience Utilisateur)** : En renvoyant une raison textuelle, on évite la frustration du joueur qui ne comprendrait pas pourquoi il ne peut pas bouger sa carte.
- **Centralisation** : Si une nouvelle règle de mouvement est introduite (ex: un trait "Immobile"), il suffit de modifier ce fichier pour que tout le jeu (IA et Joueur) en tienne compte.
- **Performance** : La vérification est purement logique et extrêmement rapide, permettant une mise à jour en temps réel de l'UI lors du survol des cartes.

---

## Résumé

Ce fichier garantit que :

- Les **règles de mouvement** de la 4e édition sont respectées.
- Le joueur reçoit un **feedback clair** sur l'état de ses troupes.
- L'**intégrité tactique** du jeu est maintenue en empêchant les activations multiples.
