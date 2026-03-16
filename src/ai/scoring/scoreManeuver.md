# scoreManeuver.ts

## Localisation

`src/ai/scoring/scoreManeuver.ts`

## Rôle du fichier

Le `scoreManeuver` est l'un des algorithmes les plus nuancés du système. Contrairement aux attaques ou aux poses de cartes, la manœuvre est un repositionnement stratégique. Ce fichier calcule le "désir" de l'IA de déplacer une unité déjà présente sur le plateau en fonction de deux forces opposées : **la peur** (repli défensif) et **l'ambition** (avance offensive).

Il permet à l'IA de ne pas rester statique et de réagir dynamiquement à l'état de santé de ses troupes et à la pression exercée par le joueur.

---

## Logique de Décision

L'IA évalue chaque mouvement possible selon un système de points pondérés :

### 1. Le Repli Défensif (Priorité Haute)

Une unité située au **Rang 1** (la ligne de front) cherchera activement à reculer si elle est en danger.

- **Critères de danger** : L'unité a perdu plus de 50% de ses PV, ou elle est blessée et la force d'attaque cumulée du joueur en face dépasse un certain seuil (**Score : +80**).
- **Pondération** : Plus une unité est proche de la mort, plus son score de repli est élevé (priorité aux blessés graves).

### 2. L'Avance Offensive

Une unité située aux rangs arrières (2 ou 3) cherchera à monter au front pour participer au combat.

- **Front Sûr** : Si la pression adverse est faible, l'IA est très encouragée à avancer (**Score : +70**).
- **Front Risqué** : Si le joueur a de gros attaquants prêts, l'IA avance prudemment (**Score : +20**).
- **Pondération** : Les unités ayant la meilleure valeur offensive (ATK + PV) sont choisies en priorité pour monter au front.

---

## Les Garde-fous (Pénalités)

Pour éviter des mouvements absurdes, l'algorithme applique des malus sévères :

- **Rang Illégal (-100)** : Interdiction quasi-totale de faire un mouvement qui briserait la structure des rangs (ex: laisser un trou au milieu de la formation).
- **Recul Inutile (-40)** : Une unité en pleine santé n'a aucune raison de fuir.
- **Stagnation (-5)** : Changer de position latérale dans le même rang est jugé peu rentable.

---

## Analyse de la Pression (`EnemyFrontlinePressure`)

L'IA calcule la "force de frappe" du joueur au tour actuel en additionnant l'attaque maximale de chaque unité du joueur au rang 1. Si ce total dépasse **8**, l'IA considère que le front est sous "forte pression", ce qui déclenche les comportements défensifs.

---

## Résumé Technique

- **`scoreManeuver`** : Point d'entrée qui parcourt tous les `candidates` (unités prêtes n'ayant pas bougé).
- **`isCardInDanger`** : Détermine l'état de panique de l'unité.
- **`computeMoveScore`** : Applique les constantes de score (Advance vs Retreat).
- **Calcul Final** : Le score est multiplié par la valeur de la carte pour s'assurer que les pièces maîtresses de l'IA sont gérées en priorité.
