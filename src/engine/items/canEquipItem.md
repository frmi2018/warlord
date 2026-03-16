# Documentation de canEquipItem.ts

## Localisation

`src/engine/items/canEquipItem.ts`

## Rôle du fichier

Ce fichier contient la **logique de validation** (pure) pour l'équipement d'un objet.

Avant d'autoriser un joueur à attacher un item à un personnage, le moteur appelle ces fonctions pour vérifier si toutes les conditions de règles (niveau, classe, emplacement) sont respectées.

---

## Concept : Validation d'Équipement

L'équipement d'un item n'est pas automatique. Il suit plusieurs règles strictes :

1.  **Disponibilité du personnage** : Seul un personnage prêt ("ready") peut s'équiper. L'action le fait passer en état "spent".
2.  **Restriction de Classe** : Certains items sont réservés aux Guerriers, Mages, etc.
3.  **Calcul du Niveau Effectif** : Le jeu utilise une formule spécifique : `Niveau du personnage + Rang (Position Y)`.
4.  **Unicité** : Un personnage ne peut pas porter deux items ayant le même nom.

---

## Fonctions principales

### `canEquipItem(item, character): CanEquipResult`

Vérifie si l'item peut être posé sur le personnage cible.

- **Retourne** : Un objet `{ canEquip: boolean, reason?: string }`.
- **Points de contrôle** :
  - Vérifie que la carte cible est bien en jeu.
  - Vérifie que le personnage n'est pas "spent" ou "stunned".
  - Compare la `classIcon` du personnage avec la liste `onlyFor` de l'item.
  - Calcule si le niveau requis est atteint (incluant les bonus globaux comme ceux de _King Xod_).
  - Vérifie les doublons de nom.

### `getItemsToDestroy(newItem, equippedItems): Card[]`

Identifie les conflits d'emplacements (Equip Locations).

- Si vous équipez une "Weapon" alors que le personnage en porte déjà une, cette fonction retournera l'ancienne arme pour qu'elle soit envoyée à la défausse par le moteur.

---

## Fonctions internes (Helpers)

### `getEquipLocations(item): string[]`

Filtre les traits d'une carte pour extraire uniquement les emplacements officiels (ex: _Armor, Weapon, Shield, Helm_).

---

## Variables et Types importants

| Élément           | Description                                                                  |
| :---------------- | :--------------------------------------------------------------------------- |
| `CanEquipResult`  | Interface de retour (succès/échec + message d'erreur).                       |
| `EQUIP_LOCATIONS` | Set contenant les mots-clés réservés aux emplacements (Armor, Weapon, etc.). |
| `effectiveLevel`  | Résultat du calcul : `Niveau + Position + Bonus globaux`.                    |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `globalBonuses` → Pour appliquer des bonus de faction (ex: bonus aux Dwarves).
- `cardGuards.ts` → Pour valider la structure des cartes personnages.

---

## Résumé

Ce fichier est le **garde-fou** du système d'équipement. Il :

- Interdit les actions illégales selon les règles du jeu.
- Fournit des messages d'erreur clairs pour l'interface utilisateur.
- Gère la logique de remplacement des objets (emplacements occupés).
  """
