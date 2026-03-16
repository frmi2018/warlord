# cardRegistry.test.ts

## Localisation

`src/__tests__/cardRegistry.test.ts`

## Rôle du fichier

Ce fichier de test valide la refonte majeure du **système de données des cartes** effectuée lors du Sprint 1.3. L'objectif principal est de s'assurer que la transition vers un "Registre Unifié" (alliant données de cartes et effets logiques) s'est faite sans perte de données ni régression fonctionnelle.

Il vérifie que le moteur de jeu peut désormais séparer les données pures des cartes (utilisées pour l'affichage) de la logique complexe des effets (utilisée par le moteur de calcul).

---

## Fonctionnalités vérifiées

### 1. Intégrité du Registre (`allCards`)

Le test s'assure que la nouvelle structure contient l'intégralité du catalogue de cartes :

- **Volume** : Vérifie qu'aucune carte n'a disparu lors de la migration.
- **Unicité** : Garantit qu'aucun identifiant (`id`) n'est présent en double, ce qui briserait le moteur de rendu.
- **Richesse des données** : Vérifie que les effets (passifs ou actifs) sont bien attachés aux objets de cartes (ex: King Xod, Axe, Hammer, Charge).

### 2. Rétrocompatibilité (`cardDatabase`)

Pour éviter de casser les composants UI existants qui ne gèrent pas encore les fonctions logiques :

- **Filtrage** : Vérifie que l'export `cardDatabase` contient bien les mêmes cartes mais **sans** le champ `effects`. Cela permet de garder des objets de données "propres" et sérialisables pour l'affichage simple.

### 3. Registre d'Effets (`effectRegistry`)

C'est le cœur du système de calcul. Les tests valident que :

- **Mapping intelligent** : Le système retrouve les effets d'une carte qu'on utilise son ID de base (`sots-35`) ou un ID généré au runtime (`p-sots-35-ab12` pour un joueur).
- **Isolation** : Une carte sans effet (comme Slayer) retourne bien une liste vide au lieu de faire planter le moteur.
- **Logique métier** : Un test simule "Axe" avec "Hammer" à côté de lui et vérifie que le bonus de **+2 ATK** est correctement calculé par la fonction `apply` de l'effet.

---

## Concepts Clés

- **Séparation des préoccupations (SoC)** : Les données visuelles restent dans `cardDatabase`, tandis que l'intelligence du jeu réside dans `effectRegistry`.
- **Runtime ID Management** : Capacité à identifier une instance de carte sur le plateau (qui a un ID unique préfixé) et à la relier à ses règles d'origine.
- **Tests de contrat** : Garantit que si un développeur ajoute une carte, il n'oublie pas de respecter les contraintes d'identifiants uniques.

---

## Résumé Technique

- **Moteur de test** : Vitest.
- **Sujets testés** : L'index des données cartes et le registre des effets.
- **Sécurité** : Prévient les erreurs de type "Undefined" lors de l'accès aux effets en plein combat.
