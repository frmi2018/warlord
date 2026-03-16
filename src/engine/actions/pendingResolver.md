# Documentation de pendingResolver.ts

## Localisation

`src/engine/actions/pendingResolver.ts`

## Rôle du fichier

Ce fichier est le **gestionnaire de files d'attente** du moteur. Son rôle est de décider quand et comment exécuter les actions qui ont été mises "en pause" à cause du système de réactions (Reacts).

Il garantit que le jeu ne reste pas bloqué et que les actions s'exécutent dans le bon ordre une fois qu'une fenêtre de réaction se ferme.

---

## Concept : Les deux files d'attente

Le moteur gère deux types d'actions en attente, avec des priorités différentes :

### 1. `pendingTriggerAction` (Priorité Haute)

C'est une action qui a été **gelée juste avant** d'être exécutée pour permettre à l'adversaire de réagir (Phase "Before").

- _Exemple_ : Vous annoncez une attaque. Le jeu gèle l'attaque pour demander à l'adversaire s'il veut utiliser un bonus de défense. Une fois que l'adversaire a fini, l'attaque doit reprendre là où elle s'était arrêtée.

### 2. `pendingActions` (Priorité Basse)

C'est une **liste d'actions** générées par des réactions elles-mêmes.

- _Exemple_ : Une capacité "Riposte" génère une contre-attaque. Cette contre-attaque est ajoutée à la file pour être traitée après l'action principale.

---

## Fonctionnement : `resolvePending(state)`

Cette fonction est appelée automatiquement après chaque action. Elle fonctionne comme un entonnoir :

1.  **Vérification de sécurité** : Si une fenêtre React est encore ouverte (`reactWindow !== null`), elle ne fait rien et attend.
2.  **Traitement du Trigger** : Si une `pendingTriggerAction` existe, elle l'exécute immédiatement.
3.  **Dépilage de la file** : Si le trigger est réglé, elle prend la première action de la liste `pendingActions` et l'exécute.

### Le mécanisme du "Bypass" (Contournement)

Une règle critique est appliquée ici : la fonction appelle `executeHandler` au lieu de `applyAction`.

- **Pourquoi ?** Parce que la phase de vérification "Before" (interception) a déjà eu lieu. Si on ne la bypassait pas, le jeu détecterait à nouveau que l'adversaire peut réagir, créerait une nouvelle fenêtre, et l'action resterait bloquée dans une boucle infinie.

---

## Flux logique de résolution

| Priorité | État                            | Action entreprise                                         |
| :------- | :------------------------------ | :-------------------------------------------------------- |
| **0**    | `reactWindow` ouvert            | **STOP** : On attend la fermeture de la fenêtre.          |
| **1**    | `pendingTriggerAction` présente | **EXÉCUTER** : L'action gelée reprend son cours.          |
| **2**    | `pendingActions` non vide       | **DÉPILER** : On exécute l'action suivante dans la liste. |
| **3**    | Tout est vide                   | **TERMINER** : L'état est stable.                         |

---

## Pourquoi ce fichier est important pour la stabilité ?

- **Évite les boucles infinies** : Grâce au bypass de `interceptBefore`.
- **Respecte les coûts** : On ne re-valide pas l'action car son coût (ressources, activation) a déjà été payé au moment où elle a été gelée.
- **Gestion des effets en chaîne** : Permet à une réaction d'en entraîner une autre de manière ordonnée.

---

## Résumé

Ce fichier garantit que :

- Les actions gelées par le système de réaction finissent toujours par **s'exécuter**.
- Les **contre-attaques** et effets secondaires sont traités dans l'ordre chronologique.
- Le moteur de jeu sait **automatiquement** quand reprendre le cours normal de la partie.
