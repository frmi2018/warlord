# eventBus.test.ts

## Localisation

`src/__tests__/eventBus.test.ts`

## Rôle du fichier

Ce fichier de test valide le fonctionnement du **GameEventBus**, le système nerveux central du moteur de jeu. Le "Bus d'Événements" permet de découpler la logique globale (début de tour, mort d'une carte, fin de phase) des effets spécifiques des cartes ou des mécaniques de jeu.

Au lieu d'écrire un code massif qui gère tout, chaque partie du jeu peut "s'abonner" (subscribe) à des événements précis et réagir en conséquence, rendant le code plus modulaire et facile à maintenir.

---

## Fonctionnalités vérifiées

### 1. Publication et Souscription (Pub/Sub)

Vérifie que le bus peut enregistrer des fonctions de rappel (handlers) pour des événements spécifiques (ex: `turn_start`) et les déclencher avec les bonnes données (`GameEvent`) et l'état actuel du jeu (`GameState`).

### 2. Mutation de l'État par les Handlers

C'est une spécificité forte de cette implémentation : les handlers peuvent retourner des modifications de l'état. Le test vérifie que :

- Si un handler demande de modifier `turnNumber`, le bus retourne un **nouvel état** mis à jour.
- **Fusion des changements** : Si plusieurs cartes réagissent au même événement (ex: deux nains gagnent un bonus au début du tour), le bus fusionne toutes leurs modifications de manière cohérente.

### 3. Gestion du Cycle de Vie

Garantit que le bus ne fuit pas et ne déclenche pas d'effets fantômes :

- **Désabonnement (Unsubscribe)** : Vérifie qu'un handler retiré ne reçoit plus d'événements.
- **Isolation** : Un événement de "début de tour" ne doit jamais déclencher un handler écoutant la "fin de tour".
- **Nettoyage (Clear)** : Vérifie la possibilité de vider tous les abonnés d'un coup (utile lors d'un changement de partie).

---

## Concepts Clés

- **Pattern Observateur** : Le bus gère une liste d'abonnés par type d'événement.
- **Immuabilité** : Le bus ne modifie pas l'état `mockState` directement mais retourne une version étendue si des modifications sont demandées, respectant les principes de React/Redux.
- **Découplage** : Le moteur peut émettre un événement sans savoir qui l'écoute, ce qui permet d'ajouter de nouvelles cartes avec des effets complexes sans toucher au code central.

---

## Résumé Technique

- **Outils** : Vitest (describe, it, expect, beforeEach).
- **Fiabilité** : Utilise `makeTestState` pour garantir que l'objet de jeu simulé est toujours complet et valide.
- **Performance** : Vérifie que le bus peut gérer plusieurs handlers simultanés sans collision de données.

---
