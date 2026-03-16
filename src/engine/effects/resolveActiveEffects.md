# Documentation de resolveActiveEffects.ts

## Localisation

`src/engine/effects/resolveActiveEffects.ts`

## Rôle du fichier

Ce fichier sert de **moteur de filtrage et de visibilité** pour les capacités spéciales des cartes. Il permet de déterminer, à un instant T, quelles compétences "actives" un personnage possède et s'il a le droit de les déclencher selon le contexte actuel du plateau.

Il fait le pont entre la définition brute des effets dans le registre et les besoins concrets de l'interface (pour griser/activer un bouton) ou de l'IA (pour évaluer ses options).

---

## Concept : Effets Actifs vs Passifs

Le moteur distingue les effets selon leur mode de déclenchement :

- **Actifs** : Nécessitent une décision (joueur ou IA) pour être activés.
- **Passifs / Triggered** : S'activent automatiquement via le bus d'événements (non gérés ici).

Ce fichier se concentre exclusivement sur les **ActiveEffects**, qui possèdent une méthode `canUse()` définissant leurs conditions d'activation (ex: "nécessite d'être au premier rang", "une seule fois par tour", etc.).

---

## Fonctions principales

### `getUsableActiveEffects(card, alliedCards, enemyCards): ActiveEffect[]`

C'est la fonction "cerveau" pour les prises de décision.

1.  **Récupération** : Cherche tous les effets associés à l'ID de la carte via `getEffectsForCard`.
2.  **Filtrage de type** : Ne garde que les effets de type `"active"`.
3.  **Validation contextuelle** : Exécute la fonction `canUse(ctx)` de chaque effet. Si elle renvoie `false`, l'effet est écarté.

- _Usage_ : Utilisé par l'IA pour savoir ce qu'elle peut faire, ou par le dispatcher d'actions.

### `getAllActiveEffects(card): ActiveEffect[]`

Une fonction utilitaire plus permissive.

- Elle retourne tous les effets actifs associés à une carte, **sans vérifier** si les conditions d'utilisation sont remplies.
- _Usage_ : Idéal pour l'UI (Modales de cartes) afin d'afficher la liste complète des capacités d'un héros, même si elles sont actuellement inutilisables (grisées).

---

## Variables et Types importants

| Élément             | Description                                                                              |
| :------------------ | :--------------------------------------------------------------------------------------- |
| `ctx`               | Objet de contexte contenant `self` (la carte), `alliedCards` et `enemyCards`.            |
| `ActiveEffect`      | Type d'effet possédant une méthode `canUse` et une logique d'application.                |
| `getEffectsForCard` | La fonction du registre qui fait le lien entre l'ID d'une carte et sa logique technique. |

---

## Interaction avec d'autres fichiers

Ce module est un outil de consultation pour :

- `CardActionsModal.tsx` : Pour savoir quels boutons d'action afficher au joueur.
- `aiDecide.ts` : Pour permettre à l'IA d'analyser ses capacités de combat ou de support.
- `effectRegistry.ts` : La source de données où sont stockées les définitions d'effets.

---

## Résumé

Ce fichier est responsable de :

- **Interroger le registre** des effets pour une carte donnée.
- **Évaluer la légalité** d'une action spéciale selon la situation de jeu.
- **Fournir les données nécessaires** à l'UI pour informer le joueur de ses possibilités.
  """
