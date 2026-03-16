# priority3_attack.ts

## Localisation

`src/ai/priorities/priority3_attack.ts`

## Rôle du fichier

Le fichier `priority3_attack` gère la phase de combat standard de l'IA. Son objectif est de maximiser l'efficacité des attaques en neutralisant les menaces les plus dangereuses (unités "Ready") tout en évitant les pièges tactiques comme le fait de libérer involontairement le passage pour des renforts adverses.

Ce module implémente une logique de **ciblage intelligent** qui distingue les frappes uniques des frappes multiples (_multi-strike_).

---

## Hiérarchie des Cibles (Sous-priorités)

L'IA évalue les cibles potentielles selon un ordre de rentabilité décroissant :

### 3a & 3b. Priorité aux unités "Ready" (Menaces directes)

L'IA cherche d'abord à éliminer les unités du joueur capables d'attaquer au prochain tour.

- **Multi-strike d'abord** : Elle utilise ses meilleurs attaquants (ceux ayant plusieurs frappes) contre les unités prêtes ayant l'AC (Classe d'Armure) la plus basse.
- **Fallback Mono-strike** : Si elle n'a pas de multi-strike, elle utilise n'importe quel attaquant disponible contre ces mêmes cibles prioritaires.

### 3c à 3f. Cibles "Weak" (Spent/Stunned) et Sécurité

Si aucune unité "Ready" n'est vulnérable, l'IA s'en prend aux unités déjà épuisées, mais avec une **grande prudence tactique** :

- **Vérification du Rang 2** : Elle évite d'attaquer si le joueur possède des unités prêtes au rang 2 qui pourraient avancer suite à la mort de la cible.
- **Évitement des Rangs Illégaux Dangereux** : L'IA utilise `wouldKillCauseDangerousIllegalRank` pour s'assurer que tuer une carte ne créera pas un trou forçant une unité puissante du joueur à avancer vers la ligne de front (mécanique de _Fall Forward_).

---

## Concepts Clés

### 1. Optimisation de l'AC (Armor Class)

L'IA utilise systématiquement `lowestAcTarget`. En ciblant la carte la plus facile à toucher parmi une catégorie de priorité, elle maximise statistiquement ses chances d'infliger des dégâts et de ne pas gaspiller son tour.

### 2. Gestion du "Fall Forward"

C'est la spécificité de Warlord : tuer un ennemi n'est pas toujours bon. Si tuer un "faible" permet à un "fort" d'avancer et d'attaquer, l'IA préférera retenir ses coups. Le code sépare les rangs illégaux en deux catégories :

- **Dangereux** : Contient au moins une carte "Ready" (menace imminente).
- **Safe** : Ne contient que des cartes épuisées ou étourdies.

---

## Fonctions de Support

- **`getAiFrontlineAttackers`** : Filtre les unités de l'IA capables d'initier un combat.
- **`getBestAttackerForTarget`** : Sélectionne l'attaquant qui a la meilleure probabilité de réussite contre une cible précise.
- **`wouldKillCauseDangerousIllegalRank`** : Simule la mort de la cible pour prédire si le mouvement de troupes adverse qui en découle est risqué pour l'IA.

---

## Résumé

Ce fichier est responsable de :

- **Neutraliser les attaquants adverses** en priorité.
- **Maximiser le potentiel des multi-strikers**.
- **Calculer le risque tactique** lié au déplacement des lignes (Fall Forward).
- **Assurer un ciblage efficace** via l'analyse des Classes d'Armure (AC).
