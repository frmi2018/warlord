# Documentation de aiDecide.ts

## Localisation

`src/ai/aiDecide.ts`

## Rôle du fichier

`aiDecide` est le **cerveau central** de l'intelligence artificielle. C'est l'arbitre qui détermine l'action que l'ordinateur va entreprendre à chaque tour. Son architecture repose sur un principe de **Priority Chain** (Chaîne de Priorités) : il teste une liste de règles ordonnées de la plus stratégique à la plus basique, et exécute la première qui est applicable.

---

## Fonctionnement de la Chaîne de Priorité

L'IA ne réfléchit pas de manière globale ; elle vérifie séquentiellement ses options. Imaginez un entonnoir de décision :

1.  **Agression (Kill Warlord)** : Puis-je gagner la partie immédiatement ?
2.  **Développement (Play/Equip)** : Puis-je renforcer ma position ?
3.  **Contrôle (Tactical Moves)** : Dois-je repositionner mes troupes ?
4.  **Défense (Attack Chaff)** : Dois-je éliminer une menace mineure ?
5.  **Passer le tour** : Si rien d'autre n'est avantageux.

---

## Concepts Clés

### 1. Le "Skip Chance" (Difficulté variable)

La fonction accepte un paramètre `randomSkipChance`.

- Si ce paramètre est > 0, l'IA a une probabilité de "sauter" une règle de haute priorité (comme une attaque optimale).
- Cela permet de simuler des niveaux de difficulté : une IA "Facile" sautera souvent les meilleures opportunités, tandis qu'une IA "Expert" (chance à 0) jouera toujours le coup le plus optimal selon sa chaîne.

### 2. Les Indices Non-Skippables

Certaines décisions sont obligatoires pour que le jeu ne "freeze" pas.

- L'action de **Passer le tour** (souvent la dernière de la chaîne) ne peut jamais être sautée. Sans cela, l'IA pourrait se retrouver dans une boucle infinie sans savoir quoi faire si elle saute toutes ses options.

### 3. Métadonnées enrichies

`aiDecide` ne renvoie pas juste une action technique (ex: `attack id:123`). Elle renvoie un objet `AiActionWithMeta` qui inclut :

- `priorityNumber` : L'étage de la pyramide de décision utilisé.
- `priorityReason` : Une explication textuelle (ex: _"Je déploie ce personnage car mon rang 1 est vide"_).
  Cela permet à l'interface d'afficher des **bulles de pensée** ou des toasts expliquant les intentions de l'IA au joueur.

---

## Architecture Technique

Le hook parcourt la constante `AI_PRIORITY_CHAIN`. Chaque élément de cette chaîne est composé de :

- **`rule`** : Une fonction pure qui analyse l'état du jeu (`state`) et renvoie soit une `GameAction`, soit `null`.
- **`meta`** : Les informations descriptives de cette règle.

---

## Résumé

Ce fichier est responsable de :

- **L'arbitrage** entre les différentes stratégies disponibles.
- **La gestion de la difficulté** via le mécanisme de saut aléatoire.
- **La communication** de l'intention de l'IA vers l'UI (explication du coup).
- **La garantie** qu'une action est toujours retournée (évite le blocage du jeu).
  """
