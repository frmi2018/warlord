# Documentation de triggerValidator.ts

## Localisation

`src/engine/reacts/triggerValidator.ts`

## Rôle du fichier

Ce fichier contient la **logique de vérification de validité** d'une action déclencheuse (trigger) pendant une fenêtre de réaction.

Selon les règles officielles, après chaque réaction jouée, le moteur doit vérifier si l'action initiale qui a ouvert la fenêtre est toujours "légale". Si un effet de jeu (comme une esquive ou la mort d'un personnage) rend l'action impossible, ce fichier permet de détecter qu'il faut annuler la suite des événements.

---

## Concept : Validation du Trigger

Le système de réaction permet d'interrompre le cours normal du jeu. Ce fichier gère les cas d'annulation automatique :

1.  **Annulation explicite** : Une capacité comme _Stealth_ ou _Magic Resistance_ marque une action comme annulée via un identifiant (`canceledActionId`).
2.  **Disparition des entités** : Si un personnage impliqué dans une attaque est éliminé du plateau par une réaction, l'attaque ne peut plus aboutir.
3.  **Timing** : La validation est plus stricte en timing `"after"` qu'en timing `"before"`, car les conséquences physiques (mort, déplacement) sont déjà prises en compte.

---

## Fonctions principales

### `isTriggerStillValid(state, window): boolean`

Fonction point d'entrée qui analyse le type d'action déclencheuse et redirige vers le bon helper de validation.

- **Retourne** : `true` si l'action peut continuer, `false` si elle doit être avortée.

### `isMeleeAttackStillValid(...)` (Helper)

Vérifie la validité d'une déclaration d'attaque (`spendAttacker`).

- Invalide si l'attaquant est marqué comme annulé par un effet de _Stealth_.
- Invalide si l'attaquant ou la cible (en timing "after") n'est plus présente dans les rangs de combat.

### `isActionCardStillValid(...)` (Helper)

Vérifie la validité d'un sort ou d'une carte action jouée.

- Invalide si l'ID de la carte correspond au `canceledActionId` (cas typique de la _Magic Resistance_).

---

## Variables et Types importants

| Élément            | Description                                                                                           |
| :----------------- | :---------------------------------------------------------------------------------------------------- |
| `canceledActionId` | Stocké dans le `GameState`, il contient l'ID de l'entité dont l'action est annulée.                   |
| `triggerAction`    | L'action initiale (attaque, pose de carte) en cours de vérification.                                  |
| `isCharacter`      | Garde de type utilisé pour s'assurer qu'on manipule bien un personnage et non un autre type de carte. |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `reactTypes.ts` → Pour accéder à la structure de la fenêtre de réaction.
- `cardGuards.ts` → Pour vérifier la nature des cartes (personnages).
- `gameState.ts` → Pour analyser l'état du plateau et les annulations en cours.

---

## Résumé

Ce fichier est le **système de contrôle de cohérence** des réactions. Il :

- Applique la règle : "Une action annulée ne se résout jamais".
- Empêche les bugs visuels ou logiques (ex: une attaque qui frappe un personnage mort).
- Assure que le flux du jeu reste fidèle aux règles du livret officiel après chaque interruption.
  """
