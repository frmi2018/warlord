# Documentation de ReactWindowModal.tsx

## Localisation

`src/components/ReactWindowModal.tsx`

## Rôle du fichier

Le `ReactWindowModal` est une **interface d'interruption tactique**. Il s'affiche lorsqu'une fenêtre "React" est ouverte, permettant au joueur d'utiliser des capacités spéciales (Feats), des objets ou des actions en réponse immédiate à un événement de jeu.

C'est un composant **passif** : il affiche les données fournies par la logique métier et communique les choix de l'utilisateur via des fonctions de rappel (callbacks).

---

## Fonctionnement : Flux de Réaction

La modale structure les informations pour faciliter la prise de décision rapide :

- **Contexte du Déclencheur** : Affiche une description textuelle (`triggerDescription`) expliquant l'événement qui permet la réaction.
- **Choix des Actions** : Liste toutes les réactions disponibles (`availableReacts`) sous forme de boutons détaillés.
- **Option de Sortie** : Propose un bouton "Passer" ou "Ne pas réagir" pour déclencher la fonction `onPass`.

---

## Les Éléments d'Interface

### `ReactButton`

Chaque bouton de réaction affiche des métadonnées critiques pour le joueur :

- **Source** : Identifie si la capacité provient d'un talent (`feat`), d'un objet (`item`) ou d'une action.
- **Timing** : Indique si la réaction se produit "Avant" ou "Après" le déclencheur.
- **Coût** : Un badge "Dépense" s'affiche si la réaction nécessite de consommer une ressource ou d'épuiser la carte.

### `ReactCheckResultDisplay`

Lorsqu'une réaction implique un jet de compétence, cette section affiche le résultat détaillé :

- Le score brut du dé (🎲) et le bonus de compétence (`skill`).
- Le total final comparé au Degré de Difficulté (DC).
- Un indicateur visuel de succès ou d'échec avec une description du résultat.

---

## Concepts Clés pour le Développement

### Absence de Logique Interne

Conformément à son rôle de composant de présentation, `ReactWindowModal` ne contient aucune logique de validation ou d'état local complexe. Si aucun "React" n'est disponible, il affiche un message d'information expliquant pourquoi (personnages déjà inclinés, manque de capacités, etc.).

---

## Résumé

Le `ReactWindowModal` est responsable de :

- **Présenter les opportunités de réaction** de manière claire et catégorisée.
- **Afficher les résultats des jets de dés** liés aux capacités de réaction.
- **Capturer la décision finale** du joueur pour la transmettre au dispatcher via `onReact` ou `onPass`.
