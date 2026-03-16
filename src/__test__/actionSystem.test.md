# actionSystem.test.ts

## Localisation

`src/__tests__/actionSystem.test.ts`

## Rôle du fichier

Ce fichier de test unitaire vérifie l'intégrité du **Command Pattern** utilisé dans le moteur de jeu. Son rôle est de s'assurer que les actions (attaques, passes, etc.) sont correctement validées et appliquées en fonction des règles de la 4e édition de Warlord.

Il utilise **Vitest** pour simuler des scénarios de jeu et garantir que les changements de code n'introduisent pas de régressions dans la logique métier.

---

## Structure des Tests

Le fichier est découpé en deux grandes responsabilités :

### 1. Validation des Actions (`validateAction`)

Cette section vérifie si une action est "légale" à un instant T.

- **Passe** : Vérifie que l'action de passer est toujours autorisée.
- **Conformité des Phases** : Un point crucial du test porte sur la **Decree Phase**. Il valide qu'une attaque (`spendAttacker`) est autorisée pendant cette phase mais strictement rejetée pendant les autres phases (comme la _Ready Phase_).
- **Contexte du Plateau** : Les tests récupèrent dynamiquement des attaquants au premier rang pour simuler une situation réelle de combat.

### 2. Application des Actions (`applyAction`)

Cette section vérifie que l'état du jeu (`GameState`) est muté correctement une fois l'action validée.

- **Logique de Fin de Tour** : Le test vérifie que passer son tour incrémente correctement le compteur `consecutivePasses`. C'est ce compteur qui permet au moteur de savoir quand changer de phase ou de tour (si les deux joueurs passent consécutivement).

---

## Concepts Clés

- **Command Pattern** : Les actions ne sont pas des fonctions directes mais des objets de données (ex: `{ type: "pass" }`). Cela permet de les logger, de les annuler (undo) ou de les transmettre via un réseau facilement.
- **Mocking de l'État** : Le test utilise `createInitialGameState` pour générer un plateau complet avec des decks (Dwarf vs Deverenian) sans avoir à démarrer l'application complète.
- **Règle de la 4e Édition** : Le fichier contient une correction explicite indiquant que l'attaque est une action de la _Decree Phase_, montrant l'évolution du moteur vers le respect strict des règles officielles.

---

## Résumé Technique

- **Outils** : Vitest (describe, it, expect).
- **Données** : Utilise les interfaces `GameState` et `DeckInfo`.
- **Fiabilité** : Garantit que les restrictions de phase (interdiction d'attaquer au mauvais moment) sont respectées.
- **Indépendance** : Les tests réinitialisent l'état avant chaque exécution (`beforeEach`) pour éviter les effets de bord.
