# Documentation de dice.ts

## Localisation

`src/engine/dice.ts`

## Rôle du fichier

Ce fichier centralise la logique de génération de nombres aléatoires pour le jeu, spécifiquement pour simuler le lancer d'un **dé à 20 faces (d20)**.

Initialement dispersée, cette logique a été isolée pour répondre à deux besoins critiques : le **déterminisme** (nécessaire au multijoueur) et la **testabilité** (permettre d'injecter des résultats précis lors des tests unitaires).

---

## Fonctionnement technique

### `rollD20()`

La fonction génère un entier compris entre **1 et 20 inclus**.

- **Mécanique** : Utilise `Math.random()` pour garantir une distribution uniforme.
- **Résultat** : Toujours un nombre entier (`number`).

---

## Philosophie et Multijoueur

Le système de dés suit une règle de "génération à la source" pour garantir que tous les joueurs voient le même résultat au même moment :

1.  **Génération** : Le client qui initie l'action (ex: un joueur qui utilise une capacité de "React") lance le dé localement.
2.  **Transmission** : Le résultat du dé est inclus directement dans l'objet `action` (ex: `{ type: "react", roll: 15, ... }`) avant d'être envoyé au serveur ou aux autres joueurs.
3.  **Application** : Le moteur de jeu reçoit l'action, voit que le dé est déjà présent, et utilise cette valeur au lieu d'en générer une nouvelle.

[Image showing a dice roll icon being passed from Client A to the Server, then to Client B]

---

## Pourquoi cette approche ?

| Problème              | Solution apportée par dice.ts                                                                                                                                         |
| :-------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Désynchronisation** | En solo, chaque client pourrait générer un dé différent pour la même action. En envoyant le `roll` dans l'action, on garantit que l'état reste identique pour tous.   |
| **Tests complexes**   | Auparavant, il était difficile de tester si une capacité réussissait à 15+, car le dé était aléatoire. Désormais, on peut simuler une action avec un `roll: 15` fixe. |
| **Sécurité**          | Bien que le client génère le dé, centraliser la fonction permet d'ajouter plus tard des vérifications (ex: valider que le dé n'est pas > 20) au niveau du serveur.    |

---

## Résumé

Ce fichier garantit que :

- Le hasard est **uniforme** et respecte les règles du jeu physique.
- Le moteur de jeu est **déterministe** : une action avec un dé spécifique produira toujours le même résultat.
- Le passage au **multijoueur** est simplifié par l'inclusion systématique du résultat du dé dans les messages d'action.
