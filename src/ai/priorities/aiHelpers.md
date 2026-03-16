# aiHelpers.ts

## Localisation

`src/ai/priorities/aiHelpers.ts`

## Rôle du fichier

`aiHelpers.ts` est la **boîte à outils analytique** de l'IA. Contrairement aux fichiers de "priorité" qui prennent des décisions, ce fichier contient uniquement des fonctions pures (sélecteurs et calculateurs) qui scannent le plateau pour fournir des données brutes aux décideurs.

Il transforme l'état complexe du jeu en informations actionnables : "Qui peut attaquer ?", "Quelle est la cible la plus fragile ?", "Est-il risqué de poser cette carte ?".

---

## Capacités Principales

### 1. Analyse Offensive

Le module fournit des outils pour évaluer mathématiquement le combat :

- **`estimateHitChance`** : Calcule une probabilité de toucher (entre 5% et 95%) en comparant le bonus d'attaque de l'assaillant et la Classe d'Armure (AC) de la cible.
- **`getBestAttackerForTarget`** : Utilise une réduction (`reduce`) pour trouver l'unité qui a la meilleure probabilité statistique de réussir son coup.

### 2. Évaluation de la Menace

L'IA utilise ces fonctions pour identifier les cibles prioritaires :

- **`getMostDangerousTarget`** : Définit la dangerosité par le nombre de frappes (_strikes_). Une unité qui attaque 3 fois est jugée plus dangereuse qu'une unité puissante qui n'attaque qu'une fois.
- **`estimateIncomingDamage`** : Simule un tour complet de l'adversaire pour prédire combien de dégâts une carte subirait si elle était posée au premier rang.

### 3. Simulation et Projection

Certaines fonctions simulent un changement d'état pour tester sa viabilité :

- **`wouldKillCauseDangerousIllegalRank`** : "Si je tue cette carte, est-ce que les unités 'Ready' derrière vont avancer et devenir une menace ?"
- **`canMoveForwardWithoutIllegal`** : Vérifie si une unité peut avancer sans briser la structure légale des rangs.

[Image of a decision support system architecture]

---

## Logiques Spécifiques

### L'intelligence de l'équipement (`getBestEquipAction`)

L'IA ne pose pas ses objets au hasard. Elle calcule un **score d'équipement** basé sur :

- Les bonus d'attaque et d'armure de l'objet.
- La Classe d'Armure de base du personnage (on renforce les forts).
- La position (bonus si le personnage est au premier rang).

### Déblocage de la main (`maneuverToUnblockPlay`)

C'est l'une des fonctions les plus complexes. Si l'IA a une carte puissante en main mais que ses rangs sont pleins ou mal organisés pour la poser, elle va chercher une **manœuvre** (déplacement) capable de libérer l'espace nécessaire.

---

## Résumé

Ce fichier est responsable de :

- **Extraire les données pertinentes** du `GameState` (attaquants prêts, cibles faibles).
- **Calculer les probabilités** de réussite des combats.
- **Prédire les conséquences tactiques** des morts et des déplacements (système de _Fall Forward_).
- **Optimiser les synergies** entre les objets et les personnages.
- **Permettre le déploiement** en gérant les encombrements de rangs via des manœuvres.
