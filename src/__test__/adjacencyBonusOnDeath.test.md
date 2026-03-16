# adjacencyBonusOnDeath.test.ts

## Localisation

`src/__tests__/adjacencyBonusOnDeath.test.ts`

## Rôle du fichier

Ce fichier est un **test de non-régression** critique pour le moteur de jeu. Il se concentre sur une mécanique fondamentale de _Warlord_ : **l'adjacence dynamique**.

Son rôle est de garantir que lorsqu'une carte meurt (et quitte donc le plateau), les cartes restantes se "resserrent" correctement et que les bonus de proximité (comme ceux des nains **Axe** et **Hammer**) se mettent à jour instantanément.

---

## Le Scénario de Test

Le test simule une situation précise pour débusquer un bug historique dans la gestion des coordonnées :

1.  **Configuration initiale** : Trois cartes sont alignées au rang 1.
    - `posX: 0` -> **Axe** (Bonus si adjacent à Hammer)
    - `posX: 1` -> **Middle** (Une carte quelconque qui fait "tampon")
    - `posX: 2` -> **Hammer** (Bonus si adjacent à Axe)
2.  **État initial** : Axe et Hammer ne sont pas adjacents car la carte "Middle" les sépare. Aucun bonus n'est appliqué.
3.  **L'Action** : La carte `Middle` meurt et est envoyée en défausse via la fonction `moveCard`.
4.  **Résultat attendu** :
    - Le système de coordonnées doit se renormaliser automatiquement.
    - `Hammer` doit passer de `posX: 2` à `posX: 1`.
    - Axe (`0`) et Hammer (`1`) devenant voisins, les effets passifs doivent s'activer.

---

## Fonctionnalités vérifiées

### 1. Renormalisation des Positions (`moveCard`)

Le test vérifie que `moveCard` ne se contente pas de changer la zone d'une carte, mais qu'il recalcule les `posX` des cartes restantes pour combler le vide. Sans cela, les cartes resteraient séparées par un "fantôme" à la position 1.

### 2. Calcul des Effets Passifs (`resolvePassiveEffects`)

Il valide que le moteur d'effets recalcule les bonus de statistiques dès que le voisinage change :

- **Bonus d'Attaque** : Axe doit recevoir ses +2 ATK.
- **Bonus d'Armure** : Hammer doit recevoir ses +2 AC.

### 3. Gestion de la Double Adjacence

Une section spécifique s'assure que les bonus sont **cumulables**. Si un `Hammer` est entouré de deux `Axe`, il doit recevoir deux fois son bonus (+4 AC au total). Cela garantit que le code utilise bien des filtres (`.filter()`) et non une simple recherche du premier voisin (`.find()`).

---

## Résumé Technique

- **Moteur de test** : Vitest.
- **Utilitaires testés** : `moveCard.ts`, `areAdjacent.ts`, `resolvePassiveEffects.ts`.
- **Importance** : Ce test est le garant de la viabilité des decks "Nains" (Dwarves) dont la puissance dépend de ces synergies de positionnement. Si ce test échoue, toute la logique de formation de combat est compromise.
