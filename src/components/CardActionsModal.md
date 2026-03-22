# Documentation de CardActionsModal.tsx

## Localisation

`src/components/CardActionsModal.tsx`

## Rôle du fichier

Le `CardActionsModal` est le **menu contextuel d'action**. Il s'ouvre lorsqu'un joueur clique sur une carte alliée pour proposer toutes les interactions possibles (Attaquer, Se déplacer, Déployer, etc.) en fonction du contexte de jeu actuel.

Ce fichier centralise les vérifications de règles pour n'activer que les boutons d'actions légitimes.

---

## Fonctionnement : Logique d'Action

Le composant filtre les actions disponibles selon plusieurs critères :

- **Phase de Jeu** : La plupart des actions sont restreintes à la `Decree Phase`. Un message d'avertissement s'affiche si le joueur tente d'agir durant une autre phase.
- **État de l'Unité** : Vérifie si le personnage est déjà épuisé (`spent`) ou étourdi avant d'autoriser une attaque.
- **Objets Équipés** : Le menu itère dynamiquement sur les objets portés par un personnage pour afficher des boutons d'actions spécifiques liés aux items (ex: utiliser un arc).

---

## Actions Gérées

- **Attaque Standard** : Déclenche le flux de combat classique.
- **Manœuvre** : Permet de déplacer l'unité vers un autre rang.
- **Déploiement / Équipement** : Permet de mettre en jeu des cartes depuis la main.
- **Capacités d'Objets** : Expose les effets "exécutables" des items équipés via l'événement `useEquippedItemEffect`.

---

## Résumé

Le `CardActionsModal` est responsable de :

- **Interroger les utilitaires de règles** (`canManeuver`, `canDeploy`, etc.) pour valider les actions.
- **Présenter une interface claire** de prise de décision.
- **Émettre des intentions d'action** vers le `GameBoard` via des événements typés.
