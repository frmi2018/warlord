# Documentation de eventBus.ts

## Localisation

`src/engine/events/eventBus.ts`

## Rôle du fichier

Ce fichier implémente un **Bus d'événements synchrone et pur**.

Il permet de découpler la logique "core" du moteur de jeu des effets spécifiques des cartes. Au lieu de modifier le code central pour chaque nouvelle règle de carte, on émet des événements (ex: "un personnage est mort", "un sort est lancé") auxquels des "handlers" (gestionnaires) peuvent réagir pour modifier l'état du jeu.

---

## Concept : Bus d'Événements Pur

Le bus respecte des contraintes strictes pour garantir la stabilité du moteur :

1.  **Synchronisme** : Tout se passe instantanément. Lorsqu'un événement est émis, le bus traite tous les abonnés et retourne le nouvel état final immédiatement.
2.  **Immuabilité** : Le bus ne modifie jamais l'état existant, il produit des copies mises à jour (`Partial<GameState>`).
3.  **Absence d'effets de bord** : Pas d'appels réseau, pas de stockage local, pas de logs. C'est un pur transformateur d'état.

---

## Méthodes de la classe GameEventBus

### `subscribe(eventType, handler): () => void`

Permet d'enregistrer une fonction qui s'exécutera à chaque fois qu'un type d'événement précis survient.

- **Retourne** : Une fonction de désabonnement (unsubscribe) pour nettoyer les écouteurs si nécessaire.

### `emit(event, state): GameState`

Diffuse un événement à tous les abonnés concernés.

- **Logique** : Elle parcourt tous les gestionnaires enregistrés, leur transmet l'état actuel, et fusionne leurs retours pour construire l'état suivant.
- **Optimisation** : Si aucun gestionnaire ne modifie l'état, elle retourne la référence originale de l'état (évite les re-renders inutiles).

### `clear(): void`

Réinitialise totalement le bus en supprimant tous les abonnés. Principalement utilisé pour garantir l'isolation entre les tests unitaires.

---

## Variables et Types importants

| Élément          | Description                                                                     |
| :--------------- | :------------------------------------------------------------------------------ |
| `EventHandler`   | Type de fonction recevant l'événement et l'état, et retournant un état partiel. |
| `GameEvent`      | L'interface globale définissant la structure de tous les événements du jeu.     |
| `globalEventBus` | L'instance unique (Singleton) partagée par l'ensemble de l'application.         |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `gameState.ts` → Pour la structure de l'état global.
- `gameEvents.ts` → Pour la définition des types d'événements disponibles.

Il est utilisé par :

- Les **Effets de Cartes** pour s'enregistrer et surveiller le jeu.
- Le **Moteur Core** pour signaler les changements majeurs (mort, dégâts, fin de tour).

---

## Résumé

Ce fichier est le **système nerveux** du moteur. Il :

- Permet aux cartes de "surveiller" ce qui se passe sur le plateau.
- Garantit que les réactions aux événements sont traitées de manière prévisible et synchrone.
- Maintient le code source propre en évitant les conditions `if/else` géantes dans le moteur central.
  """
