# Documentation de makeTestState.ts

## Localisation

`src/__tests__/helpers/makeTestState.ts`

## Rôle du fichier

Ce fichier est un **outil utilitaire (helper)** indispensable pour l'écriture des tests unitaires et d'intégration. Son rôle est de fournir un état de jeu (`GameState`) complet, valide et cohérent en un seul appel de fonction.

Il permet d'éviter la duplication de code et garantit que les tests ne cassent pas à chaque fois qu'une nouvelle propriété est ajoutée à la structure globale du jeu.

---

## Concept : Le "Factory Pattern" simplifié

Au lieu de créer manuellement un objet géant pour chaque test, on utilise une "usine" (factory) qui :

1.  Remplit tous les champs obligatoires avec des **valeurs par défaut**.
2.  Permet d'écraser (**override**) uniquement les champs spécifiques nécessaires au test en cours.

---

## Fonctions et Objets exportés

### `TEST_PLAYER_DECK` et `TEST_AI_DECK`

Ce sont des configurations de decks minimales (Nains pour le joueur, Devereniens pour l'IA) utilisées pour remplir les métadonnées obligatoires du jeu sans charger de vraies données complexes.

### `makeTestState(overrides)`

C'est la fonction principale. Elle construit l'objet `GameState`.

- **Sans paramètre** : `makeTestState()` retourne un état neutre (Phase de décret, tour 1, aucune carte sur la table).
- **Avec paramètre** : `makeTestState({ phase: "Draw Phase" })` retourne l'état par défaut, mais modifie la phase.

---

## Variables et Valeurs par défaut

| Catégorie         | Valeurs par défaut notables                                             |
| :---------------- | :---------------------------------------------------------------------- |
| **Identité**      | ID fixe `"test-game-id"`.                                               |
| **Flux de jeu**   | Tour 1, Phase de Décret, l'initiative est au joueur.                    |
| **Cartes**        | Tableaux `playerCards` et `aiCards` vides par défaut.                   |
| **Système React** | Toutes les fenêtres et actions en attente (`pending...`) sont à `null`. |
| **Fin de partie** | `isGameOver` est à `false`.                                             |

---

## Pourquoi utiliser ce helper ?

1.  **Robustesse** : Si le type `GameState` change dans le code source (ajout d'une variable pour une nouvelle règle), seul ce fichier doit être mis à jour.
2.  **Lisibilité** : Dans un fichier de test, on ne voit que les données pertinentes pour le scénario testé (ex: juste les cartes ou juste la phase), ce qui rend le test plus facile à comprendre pour un humain.
3.  **Sécurité** : Garantit que l'objet passé au moteur de jeu contient bien toutes les clés requises, évitant les erreurs "undefined" durant l'exécution des tests.

---

## Exemple d'usage dans un test

```typescript
// On veut tester un combat : on ne définit que les cartes
const state = makeTestState({
  playerCards: [monAttaquant],
  aiCards: [maCible],
});
```
