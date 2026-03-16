# Documentation de applyDeployCharacter.ts

## Localisation

`src/engine/actions/handlers/applyDeployCharacter.ts`

## Rôle du fichier

Ce fichier est un **handler pur** dont la mission est de déplacer un personnage de la main du joueur vers un emplacement spécifique sur le plateau de jeu (un "slot" de rang).

Il marque une transition importante dans l'architecture du projet : au lieu de modifier l'état directement dans les composants React (comme `GameBoard`), on passe désormais par le pipeline **`applyAction`**. Cela permet de rendre le moteur de jeu indépendant de l'interface utilisateur.

---

## Fonctionnement de l'Action

### `applyDeployCharacter(state, action)`

La fonction exécute les étapes suivantes :

1.  **Identification** : Elle cherche la carte correspondante dans `playerCards` à l'aide de l'ID fourni.
2.  **Sécurité** : Si la carte n'est pas trouvée (cas rare mais possible), elle affiche un avertissement en console et retourne l'état actuel sans le modifier pour éviter un plantage.
3.  **Délégation** : Elle appelle la fonction utilitaire `deployCharacter`.
    - Cette fonction calcule les nouvelles coordonnées de la carte.
    - Elle change sa zone de `playerHand` à `playerRanks`.
    - Elle applique une éventuelle pénalité de loyauté (si le personnage est déployé dans une faction qui n'est pas la sienne).
4.  **Mise à jour** : Elle retourne une copie du `state` avec la nouvelle liste de cartes mise à jour.

---

## Variables et Paramètres de l'Action

| Paramètre           | Type      | Description                                                        |
| :------------------ | :-------- | :----------------------------------------------------------------- |
| `cardId`            | `string`  | L'identifiant unique du personnage à déployer.                     |
| `slotIndex`         | `number`  | L'indice de l'emplacement cible sur le plateau.                    |
| `hasLoyaltyPenalty` | `boolean` | Indique si le déploiement doit déclencher une pénalité de loyauté. |

---

## Interaction avec d'autres fichiers

- **`deployCharacter.ts` (Utils)** : Contient la logique mathématique et structurelle du déploiement. C'est ce fichier qui "sait" comment placer une carte techniquement.
- **`playerActions.ts`** : Définit le type `PlayerAction` que ce handler traite.
- **`applyAction.ts`** : Le point d'entrée central qui appelle ce handler quand il reçoit une action de type `deployCharacter`.

---

## Résumé

Ce fichier garantit que :

- Le déploiement est traité comme une **action atomique** dans le moteur.
- La logique métier est centralisée, facilitant le débogage et les tests futurs.
- L'interface utilisateur ne s'occupe plus de "comment" modifier l'état, mais seulement d'envoyer la commande.
