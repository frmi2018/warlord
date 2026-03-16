# priority5_pass.ts

## Localisation

`src/ai/priorities/priority5_pass.ts`

## Rôle du fichier

Le fichier `passPriority` est l'ultime rempart de la chaîne de décision de l'IA. C'est une règle de **sécurité** indispensable qui garantit que l'IA ne restera jamais bloquée sans savoir quoi faire. Si toutes les priorités précédentes (Attaques, Poses de cartes, Actions spéciales) ont retourné `null`, cette règle intervient pour mettre fin proprement au tour de l'IA.

---

## Fonctionnement

Contrairement aux autres règles qui analysent le `GameState` (l'état du jeu), `passPriority` est une fonction **déterministe** et **inconditionnelle** :

- **Pas de condition** : Elle ne vérifie aucune carte ni aucun score.
- **Retour systématique** : Elle retourne toujours l'action `{ type: "pass" }`.
- **Non-skippable** : Dans le moteur de décision `aiDecide.ts`, cette priorité est marquée comme "non-skippable". Même si l'IA est réglée en mode "Facile" avec une chance de sauter des actions, elle ne pourra jamais sauter celle-ci.

---

## Pourquoi est-ce crucial ?

1.  **Prévention du "Freeze"** : Sans cette règle, si aucune action n'était valide, le moteur de jeu attendrait indéfiniment une décision de l'IA, bloquant ainsi la partie pour le joueur humain.
2.  **Gestion des ressources** : Si l'IA estime qu'attaquer est trop risqué et qu'elle n'a rien de bénéfique à jouer, "Passer" est la décision stratégique la plus sage pour conserver ses unités intactes pour le tour suivant.
3.  **Clarté du flux** : Cela permet de déclencher proprement les événements de "Fin de tour" (redressement des cartes, effets de fin de tour, changement de joueur actif).

---

## Résumé

Ce fichier est responsable de :

- **Clore le tour de l'IA** de manière formelle.
- **Garantir la fluidité du jeu** en fournissant une réponse par défaut.
- **Servir de filet de sécurité** pour la machine de décision.

---
