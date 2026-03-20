# Documentation de RanksDisplay.tsx

## Localisation

`src/components/RanksDisplay.tsx`

## Rôle du fichier

Le `RanksDisplay` est le **moteur de rendu du champ de bataille**. Son rôle est d'afficher la formation d'un joueur (ou de l'IA), de gérer l'organisation spatiale des cartes en rangs et de permettre la navigation fluide au sein de ces formations.

C'est un composant complexe qui combine affichage statique, calculs de modificateurs de statistiques et logique de défilement (scrolling) horizontal et vertical.

---

## Fonctionnement : Organisation et Navigation

Le composant orchestre l'affichage selon plusieurs axes :

- **Gestion des Rangs** : Il affiche par défaut un minimum de 3 rangs. Si une formation dépasse cette limite, un système de navigation verticale (flèches haut/bas) permet de faire défiler les rangs.
- **Navigation Horizontale** : Chaque rang peut contenir plus de cartes qu'il n'est possible d'en afficher simultanément (limite de 3). Des flèches gauche/droite permettent de naviguer à l'intérieur d'un rang spécifique.
- **Synchronisation Minimap** : Grâce à un `useEffect`, le composant peut recevoir un signal de "focus" (venant de la minimap) pour centrer automatiquement la vue sur une carte ou un rang précis.

---

## Les Composants Internes

### `CardSlot`

C'est l'unité de rendu pour chaque emplacement de carte. Sa particularité est de gérer la **superposition** :

- **Cartes Équipées** : Si un personnage possède des objets, le premier est affiché en retrait sous la carte principale. Un badge indique le nombre d'objets supplémentaires.
- **Zones de Clic Intelligentes** : Le slot définit des zones de collision spécifiques pour permettre de cliquer sur l'objet équipé (pour ouvrir la modale de détails) sans interférer avec les actions de la carte personnage.

### `EmptyRankPlaceholder`

Affiche un retour visuel ("— vide —") lorsqu'un rang ne contient aucune unité, maintenant ainsi la structure visuelle du plateau.

---

## Concepts Clés pour le Développement

### Gestion des Orientations

Le slot de carte (`SLOT_WIDTH`) est fixe à 200px (correspondant à la hauteur d'une carte). Cela garantit que lorsqu'une carte est pivotée à 90° (état `spent`), elle ne déborde pas sur les slots voisins et reste entièrement visible.

### Calcul des Statistiques Dynamiques

Le composant utilise le hook `useCardEffects` pour récupérer en temps réel les bonus ou malus (`StatModifiers`) appliqués aux cartes en fonction de la situation de jeu (effets de zone, capacités passives, etc.).

---

## Résumé

Le `RanksDisplay` est responsable de :

- **Structurer spatialement** le champ de bataille par rangs et colonnes.
- **Gérer le défilement** des formations denses (plus de 3 rangs ou 3 unités par rang).
- **Afficher les relations personnage-objets** via un système de couches (layers).
- **Interfacer les interactions utilisateur** (clic, survol) avec le reste du moteur de jeu.
