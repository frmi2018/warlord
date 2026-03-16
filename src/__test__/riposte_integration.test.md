# riposte_integration.test.ts

## Localisation

`src/__tests__/riposte_integration.test.ts`

## Rôle du fichier

Ce fichier est un **test d'intégration** complexe qui simule des scénarios réels de combat pour valider le talent **Riposte**. Contrairement aux tests unitaires simples, il vérifie l'interaction entre plusieurs systèmes : le moteur de combat, le pipeline d'interception et d'autres talents comme _Defend_.

Il sert de document de référence pour corriger 6 bugs spécifiques identifiés lors du développement de la mécanique de contre-attaque.

---

## Règles de la Riposte (Règle 4e-rules.pdf)

> **Riposte (React)** : Une fois par tour, après qu'un personnage a effectué une frappe de mêlée contre ce personnage : Réussissez un jet de Riposte (DC 20) pour effectuer une frappe de mêlée contre l'assaillant. (Cette frappe ne peut pas être redirigée).

---

## Bugs corrigés et scénarios testés

### 1. Timing et Déclenchement (Bugs #1 & #4)

Vérifie que la Riposte ne se déclenche que **juste après** la résolution de l'attaque (`resolveAttack`).

- Elle ne doit pas s'activer au moment de la déclaration de l'attaque (`spendAttacker`), car l'attaque n'a pas encore eu lieu.
- L'intercepteur (`interceptAfter`) doit ouvrir une fenêtre de réaction spécifiquement après un `resolveAttack` si le défenseur possède le trait.

### 2. Architecture "Once per Turn" (Bug #2)

Le test valide une subtilité d'architecture :

- La fonction `canTrigger` doit rester "pure" et ne vérifier que les conditions physiques (est-ce la bonne cible ? suis-je assommé ?).
- La restriction "une fois par tour" est déléguée au système de filtrage global (`getAvailableReacts`). Cela évite d'ouvrir des fenêtres de réaction vides qui bloqueraient le flux du jeu.

### 3. Symétrie IA et Joueur (Bug #5)

Garantit que le joueur peut riposter que l'attaque vienne d'une action manuelle du joueur adverse ou d'une décision automatique de l'IA. Le pipeline traite les deux de manière identique.

### 4. Non-Redirection (Bug #6 - Cas Critique)

C'est le test du "Rouge Attendu" (TDD) :

- **Règle** : Une riposte est une vengeance directe qui ne peut pas être interceptée par un garde du corps.
- **Test** : Si un personnage utilise _Defend_ pour tenter de protéger l'attaquant initial contre la riposte, le système doit refuser l'interception (`canTrigger` de _Defend_ retourne `false` si l'action est marquée `isRiposte: true`).

---

## Limites actuelles (Hors couverture)

Le test mentionne explicitement qu'il ne vérifie pas encore si la Riposte est limitée à la **mêlée** (elle ne devrait pas fonctionner contre un archer). Cela nécessite une mise à jour préalable des types de données du moteur pour distinguer les types de frappes dans les événements de résolution.

---

## Résumé Technique

- **Complexité** : Haute (Inter-systèmes).
- **Marqueur Spécial** : Utilise le flag `isRiposte: true` pour suivre l'origine de l'attaque à travers le bus d'événements.
- **Fiabilité** : Utilise des helpers comme `makeResolveAttack` pour garantir des données de combat cohérentes entre les tests.

---
