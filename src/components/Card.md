# Documentation de Card.tsx

## Localisation

`src/components/Card.tsx`

## Rôle du fichier

Le composant `Card` est l'unité visuelle fondamentale du jeu. Il est responsable de l'affichage complet d'une carte (personnage, objet ou action), incluant ses illustrations, ses statistiques dynamiques et ses indicateurs d'état.

C'est un composant de présentation qui réagit aux survols et aux clics, tout en intégrant des calques visuels pour les dégâts et les modificateurs.

---

## Fonctionnalités Principales

### Rendu Visuel et États

- **Gestion de l'Orientation** : Applique des classes CSS spécifiques selon que la carte est prête (`ready`), épuisée (`spent` à 90°) ou étourdie (`stunned` à 180°).
- **Illustration Dynamique** : Utilise `CardProto` pour le fond de carte et récupère l'image spécifique via une URL générée.
- **Indicateurs de Dégâts** : Affiche dynamiquement des jetons de blessure (`injury-marker-token`) en fonction du compteur `wounds` de la carte.

### Statistiques et Modificateurs

- **Badges de Stats** : Affiche les bonus ou malus de compétence (`skill`) et de niveau (`level`) uniquement s'ils sont présents.
- **Code Couleur Dynamique** : Les badges changent de couleur (vert pour un bonus, rouge pour un malus) pour une lecture immédiate de l'état de l'unité.

---

## Concepts Techniques Clés

### Feedback Utilisateur

- **Mise en évidence (Highlight)** : Le composant peut être entouré d'une bordure colorée (ex: vert pour une cible valide) via les props `isHighlighted` et `highlightColor`.
- **Interaction au survol** : Gère un état `isHovered` local pour permettre des effets visuels lors du passage de la souris.

---

## Résumé

Le `Card` est responsable de :

- **Traduire l'état technique** d'une carte en une représentation visuelle fidèle.
- **Afficher les altérations** (blessures, bonus, orientation).
- **Servir de point d'entrée** pour les interactions de sélection dans le jeu.
