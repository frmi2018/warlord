# Documentation de useGameBoardHandlers.ts

## Localisation

`src/hooks/useGameBoardHandlers.ts`

## Rôle du fichier

Ce fichier fait office de **chef d'orchestre** pour les interactions utilisateur. Il centralise tous les gestionnaires d'événements (clics, survols, actions de menu) et coordonne les appels entre l'interface et les différents hooks métier spécialisés (combat, manœuvre, équipement).

## Fonctions principales

### `handleCardClick(card)`

C'est le point d'entrée principal des interactions. Elle utilise `routeCardClick` pour déterminer l'intention du joueur selon le contexte :

- Sélectionner une cible si une attaque est en cours.
- Ouvrir le menu d'actions si la carte est au repos.
- Ignorer le clic si l'IA joue ou si l'action est bloquée.

### `handleCardAction(event)`

Traite le résultat du menu contextuel d'une carte. Elle répartit la logique vers le hook approprié : `combat.startAttack`, `maneuver.startManeuver`, ou encore le déploiement d'un nouveau personnage.

### `handleDeployConfirm(slotIndex)`

Gère la finalisation du placement d'une carte sur le plateau, incluant le retour sonore et le dispatch de l'action `deployCharacter` au moteur de jeu.

## Interaction avec le système

Ce hook ne contient aucune logique de règle de jeu ; il se contente de passer les bons arguments aux fonctions de `dispatch` ou aux notifications de changement de phase (`notifyActionDone`).
