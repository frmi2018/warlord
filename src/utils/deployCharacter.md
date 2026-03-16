# Documentation de deployCharacter.ts

## Localisation

`src/utils/deployCharacter.ts`

## Rôle du fichier

Ce fichier implémente la logique complexe du **Déploiement** (poser une carte en jeu). C'est l'action principale de la **Decree Phase**.

Contrairement à de nombreux jeux de cartes où les unités sont posées librement, _Warlord_ impose des contraintes strictes basées sur le niveau de la carte, la formation de l'armée et l'allégeance au Warlord.

---

## Règles de Déploiement (4e Édition)

Le script valide et exécute le placement selon cinq piliers fondamentaux :

### 1. Le Rang Obligatoire

Un personnage ne peut être posé que dans le rang correspondant à son niveau ($Level = Rang$).

- Une carte de **Niveau 1** va au premier rang (front line).
- Une carte de **Niveau 2** va au deuxième rang, etc.

### 2. La Cohésion de la Formation

Un personnage ne peut être déployé que si le rang situé immédiatement devant lui possède déjà au moins une carte. Il est impossible de créer un "vide" dans la formation. De plus, l'ajout ne doit pas briser la règle de la pyramide (vérifiée via `checkIllegalRanks`).

### 3. La Loyalty Penalty (Pénalité d'Allégeance)

Si le personnage appartient à une faction différente de celle du Warlord :

- **Condition** : La carte n'a pas le trait `"Mercenary"`.
- **Effet** : La carte entre en jeu dans l'état **`stunned`** (étourdie), la rendant inutile pour le tour actuel et le suivant.

### 4. Unicité

Si une carte possède le flag `unique`, le moteur interdit d'en poser une seconde instance si un exemplaire du même nom est déjà présent dans les rangs.

### 5. Gestion des Emplacements (Slots)

Le script calcule dynamiquement les espaces vides. Si un rang contient $N$ cartes, il existe $N+1$ emplacements possibles :

- À l'extrême gauche (`slotIndex: 0`).
- Entre chaque carte existante.
- À l'extrême droite.

---

## Fonctions Principales

### `canDeploy(card, playerCards)`

Analyse si une carte de la main peut être posée.

- **Retourne** : Un objet `CanDeployResult` contenant la liste des slots disponibles (`availableSlots`) et les avertissements de pénalité de loyauté.

### `deployCharacter(card, slotIndex, ...)`

Effectue le déplacement effectif de la carte de la zone `playerHand` vers `playerRanks`.

- **Décalage automatique** : Cette fonction recalcule les positions horizontales (`posX`) de toutes les cartes déjà présentes dans le rang pour "faire de la place" à la nouvelle unité au `slotIndex` choisi.

---

## Pourquoi cette architecture ?

| Avantage               | Description                                                                                                                                         |
| :--------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Simulation**         | `canDeploy` simule l'ajout d'une carte fictive pour vérifier la légalité des rangs avant même que le joueur ne clique, permettant une UI proactive. |
| **Précision Tactique** | La gestion des `posX` permet de maintenir l'ordre des cartes, essentiel pour les capacités qui ciblent les "voisins" ou les cartes "en face".       |
| **Loyalty Engine**     | Automatiser le statut `stunned` évite les erreurs de règles manuelles fréquentes dans les parties physiques.                                        |

---

## Résumé

Ce fichier garantit que :

- L'**ordre de bataille** est toujours légal et cohérent.
- Les **contraintes de niveau** et de **loyauté** sont appliquées sans faille.
- L'interface peut proposer des **zones de drop précises** entre les cartes existantes.
