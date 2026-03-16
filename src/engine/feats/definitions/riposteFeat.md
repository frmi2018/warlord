# Documentation de riposteFeat.ts

## Localisation

`src/engine/feats/definitions/riposteFeat.ts`

## Rôle du fichier

Ce fichier définit la logique de la capacité **Riposte**.

C'est un "Feat" de type **React** qui permet à un personnage de contre-attaquer immédiatement après avoir été touché par une attaque de mêlée. Si le test de compétence réussit, l'attaquant initial reçoit immédiatement une blessure en retour.

---

## Concept : La Contre-Attaque

- **Timing Réactif** : Contrairement à Powerattack, Riposte s'active uniquement en timing `"after"`, une fois que l'attaque initiale a été résolue.
- **Condition de Succès Adverse** : La riposte ne peut se déclencher que si l'attaquant a réussi à toucher (`hits: true`) le personnage possédant ce Feat.
- **Dégâts Directs** : L'effet applique une blessure directe à l'attaquant sans passer par un nouveau jet d'attaque, simulant une réponse immédiate et instinctive.
- **Sécurité anti-boucle** : Le code empêche qu'une riposte déclenche elle-même une autre riposte, évitant ainsi des boucles infinies de contre-attaques.

---

## Fonctions principales

### `canTrigger(ctx): boolean`

Vérifie si le personnage est en mesure de riposter.

- **Conditions** :
  - Le timing doit être `"after"`.
  - Le personnage ne doit pas être étourdi (`stunned`).
  - L'action doit être une résolution d'attaque (`resolveAttack` ou `aiAttack`) ayant réussi à toucher.
  - Le personnage possédant le Feat doit être la **cible** de cette attaque.

### `apply(ctx): FeatResult`

Applique les conséquences de la riposte en cas de réussite du jet de compétence (DC 20).

- **Calcul** : Somme du dé (`rollResult`) et du skill du personnage.
- **Effet** : Identifie l'attaquant original dans les listes `playerCards` ou `aiCards` et incrémente son compteur de blessures (`wounds`).

---

## Fonctions internes (Helpers)

### `extractAttackData(ctx)`

Normalise les données de l'attaque déclencheuse (ID de l'attaquant, de la cible et résultat du toucher) pour que la logique du Feat fonctionne de la même manière, que l'attaquant soit un joueur humain ou l'IA.

---

## Variables et Types importants

| Élément                | Description                                                                             |
| :--------------------- | :-------------------------------------------------------------------------------------- |
| `RIPOSTE_DC`           | Difficulté du test fixée à **20**.                                                      |
| `requiresSpend: false` | Ce Feat est un "React:", il ne nécessite pas d'épuiser le personnage pour être utilisé. |
| `isRiposte`            | Drapeau optionnel dans l'action pour éviter les récursions de contre-attaque.           |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `featTypes.ts` → Pour les interfaces de définition.
- `card.ts` → Pour manipuler les structures des cartes et appliquer les blessures.
- `gameState.ts` → Pour mettre à jour les listes de cartes après la blessure infligée.

---

## Résumé

Ce fichier est responsable de :

- **Surveiller** les attaques réussies contre le personnage.
- **Valider** le jet de riposte (DC 20).
- **Infliger une blessure punitive** à l'attaquant adverse de manière immédiate.
  """
