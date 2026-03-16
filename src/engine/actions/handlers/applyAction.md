# Documentation de applyAction.ts

## Localisation

`src/engine/actions/applyAction.ts`

## Rôle du fichier

Ce fichier est le **chef d'orchestre** du moteur de jeu. C'est le point d'entrée unique par lequel doit passer toute action (joueur ou IA) pour modifier l'état du jeu (`GameState`).

Il centralise le flux d'exécution en intégrant les règles métier et le système de réactions (Reacts), garantissant qu'aucune action n'est appliquée sans vérifier si l'adversaire peut l'intercepter.

---

## Le Pipeline d'Exécution

Le moteur suit un flux rigoureux en trois étapes pour chaque action :

### 1. Phase "Before" (`interceptBefore`)

Avant l'exécution, le système vérifie si des capacités peuvent intercepter l'action.

- **Si une réaction est possible** : L'état est "gelé" (`frozen`). L'action est mise en attente et une fenêtre de réaction s'ouvre.
- **Sinon** : On passe à l'exécution.

### 2. Phase d'Exécution (`executeHandler`)

C'est le moment où la logique de jeu est appliquée (déplacement, dégâts, etc.).

- Elle délègue le travail au **`gameReducer`**, qui aiguille l'action vers le bon gestionnaire (handler).

### 3. Phase "After" (`interceptAfter`)

Une fois l'action terminée, le système vérifie si des capacités se déclenchent **en réponse** à l'événement qui vient de se produire.

---

## Fonctions principales

### `applyAction(state, action)`

C'est la fonction principale utilisée par l'application. Elle enchaîne tout le pipeline :

1. Interception "Before".
2. Si non gelé, exécution de l'action.
3. Interception "After".

### `executeHandler(state, action)`

Cette fonction appelle directement le `gameReducer`.

- **Note technique (Anti-récursion)** : Elle est exportée pour permettre au système de résolution (`resolvePending`) d'exécuter une action qui a déjà été validée par la phase "Before", évitant ainsi de recréer une boucle de fenêtres de réaction infinie.

---

## Flux de données

| Étape              | Fonction          | Rôle                                                   |
| :----------------- | :---------------- | :----------------------------------------------------- |
| **Interception**   | `interceptBefore` | Peut stopper l'action pour ouvrir une `reactWindow`.   |
| **Logique métier** | `executeHandler`  | Modifie les données (cartes, PV) via le `gameReducer`. |
| **Déclenchement**  | `interceptAfter`  | Gère les effets secondaires suite à l'action.          |

---

## Recommandation de sécurité

> [!IMPORTANT]
> Ne jamais appeler `applyAction` ou `executeHandler` directement depuis un composant React.
> Vous devez toujours passer par le dispatcher officiel (**`useActionDispatcher`**) pour garantir que l'interface et le moteur restent synchronisés.

---

## Résumé

Ce fichier garantit que :

- L'ordre de résolution des effets est **constant**.
- Le **système de réactions** est parfaitement synchronisé avec les actions.
- Le moteur est **découplé de l'interface**, facilitant les tests et la maintenance.
