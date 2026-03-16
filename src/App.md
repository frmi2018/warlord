# Documentation de App.tsx

## Localisation

`src/App.tsx`

## Rôle du fichier

Ce fichier est la **racine de l'application** (Root Component). Sa responsabilité est de gérer le routage de haut niveau entre les deux états principaux de l'expérience utilisateur :

1.  **Le Lobby** (`HomePage`) : Où le joueur configure son deck et celui de l'adversaire.
2.  **La Table de Jeu** (`GamePage`) : Où le moteur de combat prend le relais.

---

## Architecture de Navigation

L'application utilise un système de navigation par état (`useState`) plutôt qu'une bibliothèque de routage complexe (comme react-router), ce qui est idéal pour un jeu où l'état doit être réinitialisé proprement entre deux sessions.

### 1. Gestion des Écrans (`AppScreen`)

Le type `AppScreen` définit strictement les vues disponibles : `"home"` ou `"game"`. Cela garantit qu'aucun autre état de vue ne peut être injecté par erreur.

### 2. Transmission des Données de Deck

Lors du passage au mode jeu, `App.tsx` capture et stocke les configurations sélectionnées :

- `playerDeckInfo` : Les préférences du joueur (deck choisi ou mode aléatoire).
- `aiDeckInfo` : Les préférences de l'IA.

Ces informations sont persistées au niveau racine pour être transmises à la `GamePage` lors de son montage.

---

## Logique de Rendu

Le rendu est conditionnel et sécurisé par une vérification de présence des données :

- **Entrée en jeu** : Le composant ne monte `GamePage` que si le switch est sur `"game"` **ET** que les deux configurations de deck sont valides.
- **Retour au Lobby** : La fonction `handleBackToHome` réinitialise simplement la vue, ce qui démonte la partie en cours et libère la mémoire associée au moteur de jeu.

---

## Pourquoi cette architecture ?

| Avantage                                | Description                                                                                                                                                     |
| :-------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Simplicité (Single Source of Truth)** | `App.tsx` sait exactement quelle partie est en cours et avec quels paramètres, facilitant le débogage global.                                                   |
| **Performance**                         | L'utilisation de `useCallback` pour les fonctions de transition évite des re-rendus inutiles des pages `Home` et `Game` lors des mises à jour de l'état parent. |
| **Encapsulation**                       | La logique lourde du moteur de jeu reste confinée dans `GamePage`. `App` ne sert que de chef d'orchestre.                                                       |

---

## Résumé

`App.tsx` est le **pivot de l'application**. Il sécurise la transition entre la configuration et l'action, s'assurant que la table de jeu ne s'installe que lorsque tous les préparatifs (choix des decks) sont terminés.
