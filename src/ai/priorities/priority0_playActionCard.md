# priority0_playActionCard.ts

## Localisation

`src/ai/priorities/priority0_playActionCard.ts`

## Rôle du fichier

La priorité **"Play Action Card"** est l'échelon le plus élevé de l'intelligence tactique de l'IA. Elle permet à l'ordinateur d'utiliser des cartes d'événement (comme _Charge_) qui modifient les règles normales du plateau avant de prendre toute autre décision.

Placée en tout premier dans la chaîne, cette règle permet à l'IA de réaliser des **combos**. Par exemple, utiliser une carte pour déplacer un guerrier puissant vers l'avant _avant_ de vérifier s'il peut attaquer le Warlord adverse.

---

## Mécanisme de Scoring et Seuil

Contrairement aux autres priorités qui sont souvent binaires (possible ou impossible), l'utilisation d'une carte action repose sur un **score d'opportunité** :

1.  **Calcul du Score** : L'IA évalue la situation actuelle via des fonctions de scoring spécifiques (ex: `scoreCharge`).
2.  **Seuil de Rentabilité (`MIN_SCORE_TO_PLAY`)** : Une carte action est une ressource unique qui part à la défausse. L'IA ne la jouera que si le score dépasse **35**.
    - _Pourquoi 35 ?_ Cela garantit que l'action apporte un avantage réel (comme rapprocher un attaquant d'élite du front) et n'est pas simplement gaspillée pour un gain marginal.

---

## La Logique de "Charge" (Exemple actuel)

Actuellement, le système se concentre sur la carte **Charge** :

- L'IA cherche un combattant ("Fighter") bloqué dans les rangs arrières (Rang 2 ou 3).
- Si le chemin devant lui est libre, le score augmente.
- Si le mouvement permet d'aligner une attaque dévastatrice au tour suivant, le score explose.
- Si le score final est suffisant, l'IA déclenche l'action.

---

## Architecture Évolutive

Ce fichier est conçu comme un **hub central**. Son architecture permet d'ajouter facilement de nouvelles capacités à l'IA sans modifier la structure globale :

- **Ajout de cartes** : Pour ajouter _Rally_ ou _Shield Wall_, il suffit de créer leur module de calcul de score et de les comparer ici.
- **Arbitrage** : Si l'IA a plusieurs cartes actions en main, elle comparera les scores de chacune et choisira celle qui a l'impact le plus massif sur la partie (`bestCandidate`).

---

## Résumé

Ce fichier est responsable de :

- **Initier des manœuvres complexes** avant les phases d'attaque.
- **Préserver les ressources précieuses** en refusant de jouer des cartes pour des gains trop faibles.
- **Calculer le "ROI" (Retour sur Investissement)** tactique de chaque carte action en main.
- **Servir de point d'entrée** pour toutes les logiques d'actions spéciales de l'IA.
