# combatRules.test.ts

## Localisation

`src/__tests__/combatRules.test.ts`

## Rôle du fichier

Ce fichier de tests unitaires valide les piliers mathématiques et réglementaires du système de combat. Il garantit que les règles fondamentales de la **4e édition** sont respectées, notamment en ce qui concerne l'éligibilité des attaquants, la validité des cibles et le calcul des statistiques de combat.

L'objectif est de s'assurer que le moteur de jeu ne permet aucune action illégale et calcule les affrontements avec précision avant qu'ils ne soient envoyés au `combatReducer`.

---

## Logiques de Validation Testées

### 1. État de l'Attaquant (`canAttack`)

Vérifie les conditions sine qua non pour initier une attaque :

- **Orientation** : L'attaquant doit impérativement être en état `ready`. S'il est `spent` (épuisé) ou `stunned` (étourdi), l'attaque est rejetée (**false**).
- **Déploiement** : L'attaquant doit être présent dans les rangs. Une carte en main ne peut pas attaquer.

### 2. État de la Cible (Spécificité 4e Édition)

C'est un point de règle crucial testé ici :

- **Accessibilité** : Contrairement à l'attaquant, une cible peut être attaquée même si elle est `spent` ou `stunned`. Le test valide que l'état d'épuisement de la victime ne la protège pas des coups.
- **Zone de Jeu** : La cible doit être déployée sur le plateau (`playerRanks` ou `aiRanks`). Le test confirme que les cartes dans la main, le deck ou la défausse sont immunisées.

### 3. Calcul des Statistiques Effectives

Le moteur doit calculer les valeurs finales après application des modificateurs :

- **AC (Classe d'Armure)** : Vérifie que `computeEffectiveAC` retourne la valeur de base (avant intégration future des bonus passifs).
- **ATK (Attaque)** : Gère le système de **Multi-strike**. Le test vérifie que le moteur récupère la bonne valeur d'attaque en fonction de l'index de la frappe (ex: $1^{ère}$ frappe à +8, $2^{e}$ frappe à +6).

---

## Résumé Technique

- **Fiabilité des Données** : Utilise `createTestCard` pour générer des combattants "jetables" avec des attributs contrôlés (PV, AC, ATK).
- **Robustesse** : Teste les cas limites (_edge cases_) comme les `attackValues` non définies (retourne 0 au lieu de planter).
- **Conformité** : Sert de documentation technique vivante pour les règles de portée et de ciblage.

---
