# Documentation de useReactWindow.ts

## Localisation

`src/hooks/useReactWindow.ts`

## Rôle du fichier

Ce hook est le **traducteur d'état pour l'UI**. Il extrait les données brutes du `reactWindow` (présentes dans le `GameState`) et les transforme en informations directement consommables par les composants visuels (comme la `ReactWindowModal`).

Il suit le principe de **responsabilité unique** : il ne contient aucune logique de décision ou de modification d'état. Son seul but est de répondre à la question : _"Que doit voir le joueur dans la fenêtre de réaction actuelle ?"_

---

## Architecture de la Fenêtre de Réaction

Le hook gère la visibilité et le contenu de la modal de réaction en fonction de qui doit agir :

1.  **Visibilité** : Si `reactWindow` est `null`, l'UI ne doit rien afficher.
2.  **Tour de rôle** :
    - Si `currentReactor === "player"`, l'UI affiche les boutons de capacités.
    - Si `currentReactor === "ai"`, l'UI peut afficher un indicateur de "réflexion de l'adversaire", mais bloque les interactions humaines.
3.  **Contenu Dynamique** : Il calcule quelles capacités (`availableReacts`) le joueur peut légalement utiliser en réponse à l'action spécifique qui a déclenché la fenêtre.

---

## Concept : Protection des données résiduelles

Une attention particulière est portée à la cohérence de l'affichage du jet de dé :

- **Le Problème** : `gameState.lastReactResult` conserve le résultat du dernier jet de compétence effectué (ex: un jet de _Powerattack_ réussi). Quand une _nouvelle_ fenêtre s'ouvre pour une autre attaque, l'ancien résultat est toujours dans le state.
- **La Solution** : Le hook n'expose `lastReactResult` que si `window.reactsPlayed.length > 0`.
  - Si la fenêtre vient de s'ouvrir, le résultat est `null`.
  - Dès qu'un premier effet est joué dans cette fenêtre, le résultat (succès/échec) s'affiche.

---

## Fonctions principales

### `useReactWindow(gameState)`

Retourne un objet `UseReactWindowResult` contenant :

- `isOpen` : Booléen pilotant l'affichage de la modale.
- `availableReacts` : Liste filtrée des capacités jouables (calculée via `getAvailableReacts`).
- `triggerDescription` : Un texte en langage naturel expliquant la source de l'interruption (ex: "Une attaque est déclarée").

### `describeTriggerAction(action)`

Une fonction de mapping qui transforme un type d'action système (ex: `aiAttack` ou `spendAttacker`) en une chaîne de caractères lisible avec des icônes pour améliorer l'expérience utilisateur.

---

## Interaction avec d'autres fichiers

- **`getAvailableReacts.ts`** : Utilisé pour scanner la main et le plateau du joueur à la recherche de capacités de réaction valides.
- **`ReactWindowModal.tsx`** : Le composant principal qui consomme les données de ce hook.
- **`reactTriggers.ts`** : Définit les actions (`GameAction`) qui peuvent déclencher cette fenêtre.

---

## Résumé

Ce fichier est responsable de :

- **Déterminer si la modale doit être affichée.**
- **Filtrer les réactions** pour ne montrer que celles qui sont pertinentes au timing actuel.
- **Formater les descriptions** pour le joueur.
- **Nettoyer l'affichage** des résultats de dés entre deux fenêtres distinctes.
  """
