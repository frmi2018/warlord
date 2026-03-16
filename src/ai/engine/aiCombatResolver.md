# Documentation de aiCombatResolver.ts

## Localisation

`src/ai/engine/aiCombatResolver.ts`

## Rôle du fichier

Le `aiCombatResolver` est le cerveau tactique de l'IA lors des phases de combat. Contrairement à la résolution de combat du joueur qui est interactive, ce module automatise l'intégralité de la séquence d'attaque de l'IA (jets de dés, application des dégâts, et gestion des "Multi-strikes").

Il possède une intelligence capable de **pivoter** (changer de cible) si sa cible initiale meurt avant qu'il n'ait épuisé tous ses bonus d'attaque, tout en évitant de créer des situations tactiques qui favoriseraient le joueur.

---

## La Chaîne de Priorités (Ciblage)

Lorsqu'une cible est vaincue et qu'il reste des frappes (strikes) à l'attaquant IA, il sélectionne la suivante selon une hiérarchie stricte de 8 priorités :

1.  **Warlord** : Si le seigneur de guerre adverse est à portée (Rang 1), il est la cible prioritaire absolue.
2.  **Ready (AC basse)** : Les unités prêtes à agir au tour suivant sont ciblées en priorité pour les neutraliser.
3.  **Spent / Stunned (Sûres)** : Les unités déjà épuisées sont ciblées uniquement si leur mort ne provoque pas de "Fall Forward" (chute vers l'avant) dangereux pour l'IA.
4.  **Spent / Stunned (Risquées)** : En dernier recours, si aucune autre cible n'existe et qu'il n'y a pas d'unités "Ready" derrière pour prendre la place.
5.  **Abandon** : Si aucune cible ne remplit les critères de sécurité, l'IA préfère gaspiller ses frappes restantes plutôt que de libérer un emplacement pour une unité ennemie plus forte.

---

## Concepts Avancés

### 1. La Retenue Tactique (`wouldFreeReadyFromRank2`)

C'est la fonctionnalité la plus "intelligente" du moteur. L'IA vérifie si tuer une unité "Spent" (épuisée) en première ligne ne va pas forcer une unité "Ready" (prête) du deuxième rang à avancer. Si c'est le cas, l'IA **arrête d'attaquer** pour laisser l'unité épuisée encombrer le passage du joueur.

### 2. Gestion du Multi-Strike

Le moteur boucle sur `totalStrikes` (le nombre de valeurs d'attaque sur la carte). Pour chaque itération :

- Il calcule les bonus de voisinage et de commandement.
- Il lance les dés via `rollCombat`.
- Il applique les blessures.
- Si la cible meurt, il appelle `pickNextTargetForStrike` pour ne pas perdre de dégâts.

---

## Structure des données de sortie

Le hook retourne un objet `AiCombatResult` contenant :

- **`updatedCards`** : Les nouveaux états des tableaux (cartes mortes déplacées en défausse, blessures appliquées).
- **`log`** : Un historique détaillé de chaque frappe (jet de dé, bonus, succès/échec) qui sera affiché dans l'interface pour que le joueur comprenne ce qu'il s'est passé.
- **`stoppedEarlyReason`** : Un message explicatif si l'IA a décidé d'arrêter de frapper (ex: "L'IA retient son strike pour bloquer le rang 2").

---

## Résumé

Ce fichier est responsable de :

- **Automatiser les jets de dés** de l'IA.
- **Calculer les conséquences létales** et déplacer les cartes vers la défausse.
- **Optimiser le ciblage** en plein milieu d'une séquence d'attaque.
- **Appliquer une stratégie de blocage** pour empêcher le joueur de faire avancer ses troupes fraîches.
  """
