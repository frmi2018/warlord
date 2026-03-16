# Documentation de featTypes.ts

## Localisation

`src/engine/feats/featTypes.ts`

## Rôle du fichier

Ce fichier définit le **contrat technique** (interfaces et types) que chaque capacité de type "Feat" doit respecter.

Dans ce moteur de jeu, un **Feat** est une réaction spéciale liée à un trait de personnage (ex: _Defend_, _Riposte_). Ce fichier assure que tous les Feats fonctionnent de la même manière, facilitant leur intégration et leur exécution par le moteur central.

---

## Concept : Anatomie d'un Feat

Un Feat n'est pas simplement une action ; c'est un objet structuré qui répond à trois questions :

1.  **Quand ?** (`timing`) : Se déclenche-t-il avant ou après l'action adverse ?
2.  **Peut-il ?** (`canTrigger`) : Les conditions (cible, portée, état du personnage) sont-elles remplies ?
3.  **Quoi ?** (`apply`) : Quel est l'impact sur l'état du jeu si le test de compétence (Skill Check DC 20) réussit ?

---

## Interfaces Principales

### `FeatDefinition`

C'est l'interface maîtresse. Chaque fichier de capacité (ex: `defendFeat.ts`) doit exporter un objet de ce type.

- `type`: Le nom du Feat (doit correspondre au trait sur la carte).
- `requiresSpend`: Indique si le personnage doit être redressé pour agir et s'il devient "épuisé" (spent) après.
- `canTrigger`: Une fonction de validation pure.
- `apply`: La logique de modification du `GameState`.

### `FeatTriggerContext`

Le "paquet" d'informations envoyé à un Feat pour qu'il sache s'il peut s'activer.
Il contient :

- `self`: Le personnage qui possède le pouvoir.
- `triggerAction`: L'action à laquelle on répond (peut provenir du joueur ou de l'IA).
- `state`: L'état global du jeu.

### `FeatResult`

Le résultat renvoyé par la logique du Feat après son exécution.

- `success`: `true` si le jet de dé a réussi et l'effet appliqué.
- `newState`: Le nouvel état du jeu (même en cas d'échec, le moteur a besoin de l'état actuel).
- `description`: Un texte destiné au journal de combat pour informer le joueur.

---

## Variables et Types importants

| Élément      | Description                                                             |
| :----------- | :---------------------------------------------------------------------- |
| `FeatName`   | Union de strings représentant les noms valides (Defend, Riposte, etc.). |
| `rollResult` | Le résultat brut du dé (1-20) passé au contexte `apply`.                |
| `timing`     | `"before"` ou `"after"`, crucial pour savoir quand le Feat intervient.  |

---

## Interaction avec d'autres fichiers

Ce fichier est le **socle** pour :

- `featRegistry.ts` → Qui stocke une liste de `FeatDefinition`.
- `definitions/*.ts` → Qui implémentent concrètement chaque capacité.
- `getAvailableReacts.ts` → Qui utilise `canTrigger` pour lister les options du joueur.

---

## Résumé

Ce fichier est le **standard de fabrication** des pouvoirs de personnages. Il :

- Garantit que le moteur peut manipuler n'importe quel Feat sans en connaître le détail.
- Sépare la détection (`canTrigger`) de l'exécution (`apply`).
- Permet au système de gérer de manière uniforme les succès et les échecs de compétence.
  """
