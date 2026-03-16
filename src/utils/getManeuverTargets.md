# Documentation de getManeuverTargets.ts

## Localisation

`src/utils/getManeuverTargets.ts`

## Rôle du fichier

Ce fichier contient la logique de calcul et d'exécution pour l'action **Maneuver** (Manœuvre). C'est l'un des utilitaires les plus complexes du moteur, car il doit gérer le déplacement spatial d'une carte dans une grille 2D flexible (les Rangs) tout en recalculant dynamiquement la position des autres cartes pour leur faire de la place.

Il sert à la fois au joueur (pour afficher les destinations possibles dans l'interface) et à l'IA (pour simuler ses coups).

---

## Logique de Déplacement

Selon les règles de la 4ème édition, une manœuvre offre trois types de destinations :

1.  **Avancement** : Le rang immédiatement devant ($posY - 1$).
2.  **Recul** : Le rang immédiatement derrière ($posY + 1$).
3.  **Repositionnement** : Le même rang ($posY$ actuel), mais à une position horizontale différente.

### Le Système de "Slots"

Pour offrir une liberté totale, le moteur ne raisonne pas par "case" fixe mais par **interstices**. Si un rang contient 3 cartes, il existe 4 destinations possibles (slots) :

- `Slot 0` : Extrême gauche.
- `Slot 1` : Entre la carte 1 et la carte 2.
- `Slot 2` : Entre la carte 2 et la carte 3.
- `Slot 3` : Extrême droite.

---

## Fonctions Principales

### 1. `getManeuverTargets()`

Calcule toutes les destinations valides pour une carte donnée.

- **Simulation de légalité** : Pour chaque destination potentielle, la fonction appelle `checkIllegalRanks` en arrière-plan. Si le mouvement rend la formation invalide (ex: reculer alors qu'on est le seul bloqueur au front), la propriété `willCauseIllegalRank` passera à `true`. L'interface pourra ainsi prévenir le joueur par un avertissement visuel.
- **Filtrage** : Exclut la position actuelle de la carte pour éviter les manœuvres "nulles".

### 2. `applyManeuver()`

Exécute physiquement le mouvement et met à jour l'état du jeu.

- **Coût de l'action** : La carte est marquée comme `spent` (inclinée) et le flag `movedThisTurn` passe à `true`.
- **Réorganisation spatiale** :
  - Dans le **rang de destination**, les cartes existantes sont décalées vers la droite à partir de l'index du slot choisi.
  - Dans le **rang d'origine**, les cartes restantes sont resserrées pour combler le vide laissé par le départ de la carte.

---

## Architecture et Flexibilité

| Caractéristique          | Bénéfice                                                                                                                                             |
| :----------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Paramètre `rankZone`** | Permet d'utiliser le même code pour le joueur (`playerRanks`) et l'IA (`aiRanks`), garantissant que les deux respectent exactement les mêmes règles. |
| **Immuabilité**          | Retourne systématiquement un nouveau tableau de cartes, ce qui est indispensable pour la réactivité de l'interface React.                            |
| **Labels UI**            | Génère des descriptions textuelles (ex: "Rang 2 (reculer)") directement utilisables dans des menus déroulants ou des boutons.                        |

---

## Résumé

Ce fichier garantit que :

- Les **mouvements tactiques** sont fluides et précis.
- La **formation de l'armée** est recalculée automatiquement après chaque déplacement.
- Le joueur est **averti des conséquences** de ses mouvements sur la légalité de ses rangs avant de confirmer son action.
