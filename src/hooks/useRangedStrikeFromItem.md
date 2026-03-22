# Documentation de useRangedStrikeFromItem.ts

## Localisation

`src/hooks/useRangedStrikeFromItem.ts`

## Rôle du fichier

Ce hook personnalisé gère le **flux logique complet d'une attaque à distance** déclenchée par un objet équipé (ex: un arc). Il orchestre les étapes entre la sélection de la cible, la communication avec le moteur de jeu (reducer) et la résolution des dés.

---

## Cycle de Vie d'une Frappe à Distance

1.  **Initialisation (`startRangedStrike`)** : Active le mode de sélection et calcule les cibles valides selon la règle de portée (posY attaquant + posY cible = 3).
2.  **Sélection de Cible (`selectRangedTarget`)** : Envoie une action au dispatcher pour marquer le porteur comme "épuisé" (`spent`) et prépare l'ouverture de la modale de combat.
3.  **Résolution (`confirmRangedCombat`)** : Envoie les résultats du jet de dés au moteur pour appliquer les blessures et termine l'attaque.
4.  **Annulation (`cancelRangedStrike`)** : Réinitialise l'état et, si l'attaque avait déjà commencé, envoie une action pour "redresser" l'unité.

---

## Concepts Clés

- **Gestion d'État Local** : Utilise `useState` pour suivre l'ID de la cible et l'étape actuelle du flux (sélection vs jet de dés) sans encombrer l'état global prématurément.
- **Calcul de Portée** : Implémente la règle spécifique où la somme des positions Y doit être égale à 3 pour autoriser un tir à distance.

---

## Résumé

Le `useRangedStrikeFromItem` est responsable de :

- **Coordonner les étapes** de l'attaque de A à Z.
- **Faire le lien** entre l'UI (Bannières/Modales) et le moteur de jeu (Actions/Dispatch).
- **Assurer la cohérence** de l'état du porteur de l'objet durant tout le processus.
