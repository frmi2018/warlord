# Documentation de executeReadyPhase.ts

## Localisation

`src/engine/phases/executeReadyPhase.ts`

## Rôle du fichier

Ce fichier gère la **Ready Phase** (Phase de Redressement). C'est une phase de maintenance cruciale qui se produit au début du tour. Elle remet les unités dans un état opérationnel et nettoie les compteurs de capacités pour le nouveau tour.

Son rôle est de transformer l'état physique des cartes sur le plateau pour refléter le passage du temps et la récupération des troupes.

---

## Mécanique de Redressement (Orientation)

Le moteur parcourt toutes les cartes et ajuste leur propriété `orientation` selon les règles suivantes :

| État Initial  | État Final  | Description                                                                                                                  |
| :------------ | :---------- | :--------------------------------------------------------------------------------------------------------------------------- |
| **`ready`**   | `ready`     | La carte est déjà prête, aucun changement.                                                                                   |
| **`spent`**   | **`ready`** | La carte s'était activée au tour précédent ; elle se redresse.                                                               |
| **`stunned`** | **`spent`** | Une carte étourdie nécessite deux phases pour revenir à `ready`. Elle passe d'abord de "Inclinée à 180°" à "Inclinée à 90°". |

---

## Réinitialisation des Capacités ("Once per Turn")

Outre l'aspect visuel des cartes, cette phase est responsable du nettoyage des états logiques du `GameState` (souvent via le handler de début de tour qui appelle cette fonction).

Elle réinitialise notamment :

- **`reactUsedThisTurn`** : Permet aux personnages d'utiliser à nouveau leurs capacités spéciales de type "React" ou "Feat".
- **Actions en attente** : Vide les files `pendingActions` et les bonus temporaires (`pendingAttackBonus`, etc.) pour éviter que des effets du tour précédent ne "bavent" sur le nouveau.

---

## Pourquoi cette architecture ?

1.  **Immuabilité** : La fonction utilise `.map()` pour créer un nouveau tableau de cartes. Cela garantit que le moteur React détecte le changement d'état et déclenche les animations de rotation des cartes à l'écran.
2.  **Gestion du Stun (Étourdissement)** : La logique gère nativement le fait qu'une unité étourdie ne peut pas agir immédiatement le tour suivant. Elle doit d'abord "récupérer" (passer de `stunned` à `spent`).
3.  **Déterminisme** : En centralisant les réinitialisations (`reactUsedThisTurn`, `pendingReroll`) dans cette phase, on s'assure qu'aucun bug de persistance de bonus ne survient entre les tours.

---

## Résumé

Ce fichier garantit que :

- Les armées **récupèrent leurs forces** au début du tour.
- Les **capacités limitées** (Une fois par tour) sont débloquées.
- Le plateau de jeu est **nettoyé de ses effets temporaires**, offrant un état de départ sain pour la phase de pioche suivante.
