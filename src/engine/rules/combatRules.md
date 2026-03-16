# Documentation de combatRules.ts

## Localisation

`src/engine/rules/combatRules.ts`

## Rôle du fichier

Ce fichier est le **garant législatif** du combat. Il centralise les règles extraites du manuel (4erules.pdf) pour déterminer qui peut attaquer, qui peut être ciblé, et comment les statistiques brutes des cartes sont transformées par les modificateurs de jeu.

Toutes les fonctions ici sont **pures** : elles ne modifient pas l'état, elles répondent simplement à des questions logiques et mathématiques.

---

## Règles de Validité (Conformité 4e)

Le moteur applique une distinction stricte entre l'attaquant et sa cible :

1.  **L'Attaquant** : Doit impérativement être dans l'état `"ready"`. S'il est `"spent"` (épuisé) ou `"stunned"` (étourdi), il ne peut pas initier d'offensive.
2.  **La Cible** : Sa seule condition de validité est d'être **déployée** sur le plateau (`playerRanks` ou `aiRanks`).
    - _Note importante :_ Contrairement à d'autres jeux, une cible `"spent"` peut tout à fait être attaquée. L'orientation de la cible ne protège pas contre les attaques de mêlée.

---

## Calcul des Statistiques Effectives

Les statistiques imprimées sur les cartes sont rarement les valeurs finales utilisées lors des jets de dés. Le fichier gère la "résolution des bonus" :

### Armor Class (AC) Effective

L'AC finale utilisée pour contrer un jet de touche.

- **Formule** : $AC_{effective} = AC_{base} + \sum Bonus_{passifs}$
- **Contexte** : Prend en compte les alliés adjacents (auras) et les objets équipés (armures).

### Attaque (ATK) Effective

La valeur ajoutée au dé d'attaque pour un strike spécifique.

- **Formule** : $ATK_{effective} = ATK_{strike} + \sum Bonus_{passifs}$
- **Gestion des strikes** : Gère l'index du strike pour les personnages ayant des attaques multiples (ex: une première frappe à 5 et une seconde à 3).

---

## Fonctions principales

### `canAttack(attacker, target): boolean`

Vérifie les conditions de base : l'attaquant est-il prêt ? La cible est-elle sur le terrain ?

### `isValidTarget(attacker, candidates): Card[]`

Filtre une liste de cartes pour ne retourner que les cibles légales. C'est cette fonction qui "allume" ou "éteint" les cibles potentielles dans l'interface utilisateur.

### `computeEffectiveAC` / `computeEffectiveATK`

Ces fonctions orchestrent la séparation des cartes entre "alliés" et "ennemis" par rapport à l'unité active afin de calculer correctement les bonus de positionnement fournis par `resolvePassiveEffects`.

---

## Interaction avec d'autres fichiers

Ce fichier est le cerveau analytique utilisé par :

- `rollCombat.ts` : Qui a besoin des valeurs effectives pour lancer les dés.
- `cardClickRouter.ts` : Qui utilise `isValidTarget` pour autoriser ou bloquer un clic de ciblage.
- `resolvePassiveEffects.ts` : Fournit les valeurs de bonus issues des talents et des objets.

---

## Résumé

Ce fichier est responsable de :

- **Appliquer les restrictions d'état** (Spending rule).
- **Valider les cibles** selon leur zone de déploiement.
- **Calculer les stats finales** en fusionnant les valeurs de base et les bonus dynamiques.
  """
