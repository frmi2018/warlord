# canPlayCharge.ts

## Localisation

`src/engine/actions/charge/canPlayCharge.ts`

## Rôle du fichier

Ce module contient la **logique de validation pure** pour l'action "Charge" (carte `sots-171`). Il agit comme un garde-fou (gatekeeper) qui vérifie si un personnage spécifique remplit toutes les conditions techniques pour être la cible de cette carte action.

L'objectif est d'isoler les règles métier du moteur de jeu afin qu'elles soient testables sans interface graphique et réutilisables par l'IA comme par le joueur humain.

---

## Règles de Validation (4e Edition)

Pour qu'un personnage puisse bénéficier de "Charge", il doit passer avec succès les cinq tests suivants :

### 1. Classe de Personnage

Le personnage doit être un **Guerrier** (identifié par l'icône `gauntlet`). Les autres classes (Rogue, Cleric, Wizard) ne peuvent pas utiliser cette carte.

### 2. Niveau Minimum

L'action est réservée aux combattants d'élite. Le personnage doit être au minimum de **niveau 2**. Un personnage de niveau 1 est jugé trop inexpérimenté pour effectuer cette manœuvre.

### 3. Présence sur le Terrain

La carte ne peut pas cibler un personnage en main ou dans la défausse. Il doit être activement déployé dans les rangs (`playerRanks` ou `aiRanks`).

### 4. Capacité de Mouvement

La "Charge" consiste à avancer d'un rang vers l'avant. Si le personnage est déjà au **Rang 1** (la ligne de front), l'action est invalide car le mouvement est physiquement impossible.

### 5. État d'Orientation (Non-dépense)

Contrairement à une manœuvre standard, la Charge ne "dépense" pas le personnage. S'il est `Ready`, il le reste. Le fichier valide l'éligibilité sans altérer l'état de la carte.

---

## Fonctions Exportées

- **`canPlayCharge(fighter)`** : La fonction centrale. Elle analyse une carte et retourne un objet `CanPlayChargeResult` contenant un booléen et, en cas d'échec, la raison précise du refus (ex: "Niveau insuffisant").
- **`getChargeEligibleFighters(playerCards)`** : Une fonction utilitaire qui scanne l'ensemble des cartes du joueur pour retourner une liste filtrée de tous les candidats valides. C'est elle qui permet d'afficher les cibles potentielles dans l'interface utilisateur.

---

## Résumé Technique

- **Entrée** : Une interface `Card`.
- **Sortie** : Un diagnostic précis (`canPlay`, `reason`).
- **Indépendance** : Aucune dépendance à React ou au State global (stateless).
- **Dualité** : Gère nativement les zones de jeu du joueur et de l'IA.
