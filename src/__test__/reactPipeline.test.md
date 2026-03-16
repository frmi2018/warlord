# reactPipeline.test.ts

## Localisation

`src/__tests__/reactPipeline.test.ts`

## Rôle du fichier

Ce fichier teste le **mécanisme d'interception** du moteur de jeu. Dans _Warlord_, de nombreuses actions peuvent être interrompues par des réactions (Reacts). Le pipeline est chargé de mettre le jeu en "pause" pour ouvrir une fenêtre de réaction au bon moment.

Il vérifie que le pont entre l'envoi d'une action (`EventBus`) et le système de réaction (`ReactSystem`) fonctionne sans créer de boucles infinies ou de doubles fenêtres.

---

## Fonctionnalités vérifiées

### 1. Interception "Before" (`interceptBefore`)

Vérifie si une action doit être gelée **avant** d'être exécutée (ex: _Powerattack_).

- **Gel de l'action** : Si un talent comme _Powerattack_ est disponible, l'action est marquée comme `frozen` et stockée dans `pendingTriggerAction`.
- **Priorité** : Confirme que le premier joueur à pouvoir réagir est celui qui possède l'initiative.
- **Passage fluide** : Si aucun React n'est possible, l'action traverse l'intercepteur sans être modifiée.

### 2. Interception "After" (`interceptAfter`)

Vérifie si une fenêtre de réaction doit s'ouvrir **après** la résolution d'une action (ex: _Riposte_).

- **Déclenchement** : Après une attaque réussie (`resolveAttack`), le pipeline doit détecter si le défenseur possède _Riposte_ et ouvrir la fenêtre adéquate.
- **Nettoyage** : S'assure que les actions en attente (`pendingTriggerAction`) sont bien supprimées de la mémoire une fois résolues pour éviter les exécutions en double.

### 3. Protection et Sécurité

Le pipeline doit être robuste pour éviter que le jeu ne s'arrête indéfiniment :

- **Anti-réentrance** : On ne peut pas ouvrir une fenêtre de réaction à l'intérieur d'une fenêtre déjà ouverte.
- **Actions internes** : Les actions de type `react`, `reactPass` ou `pass` ne doivent jamais être interceptées, car elles servent précisément à résoudre les fenêtres de réaction.

---

## Résumé Technique

- **Frozen State** : Le concept de geler un état permet de suspendre la logique du `reducer` tant que les deux joueurs n'ont pas décidé de passer ou de réagir.
- **Timings** : Gère strictement la distinction entre le "Avant" (préparation) et le "Après" (conséquences).
- **Immuabilité** : Utilise `makeTestState` pour garantir que chaque test repart d'une base saine.

---
