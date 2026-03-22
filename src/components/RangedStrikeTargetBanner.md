# Documentation de RangedStrikeTargetBanner.tsx

## Localisation

`src/components/RangedStrikeTargetBanner.tsx`

## Rôle du fichier

Le `RangedStrikeTargetBanner` est un **guide visuel temporaire**. Il s'affiche en superposition au centre de l'écran lorsqu'un joueur a activé une capacité de tir à distance (souvent via un objet) et doit sélectionner une cible.

---

## Interface et UX

Le bandeau est conçu pour ne pas bloquer l'interaction avec le plateau tout en fournissant les informations nécessaires :

- **Contexte d'Action** : Utilise l'icône 🏹 et un texte explicatif pour indiquer au joueur qu'il est en mode "sélection de cible à distance".
- **Rappel de Puissance** : Affiche le bonus d'attaque actuel (`attackBonus`) associé à l'arme utilisée.
- **Instruction de Cible** : Indique explicitement que seules les cibles ennemies **surlignées en vert** sont valides.

---

## Intégration

- **Annulation** : Propose un bouton "Annuler" bien visible pour sortir du mode de sélection sans consommer l'action.
- **Style Visuel** : Utilise un dégradé vert pour se différencier du bandeau de combat standard (souvent rouge/marron), renforçant l'identité visuelle de l'attaque à distance.

---

## Résumé

Le `RangedStrikeTargetBanner` est responsable de :

- **Confirmer au joueur** que son intention d'attaque à distance est enregistrée.
- **Afficher les modificateurs** de combat avant le jet de dés.
- **Permettre l'annulation** sécurisée du flux d'attaque.
