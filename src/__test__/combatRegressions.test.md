# combatRegressions.test.ts

## Localisation

`src/__tests__/combatRegressions.test.ts`

## Rôle du fichier

Ce fichier de tests unitaires est le "gardien du temple" du système de combat. Il a été conçu pour corriger et prévenir la réapparition de trois bugs majeurs identifiés lors du développement. Il utilise **Vitest** pour simuler des situations de combat complexes et vérifier que le `combatReducer` et les handlers d'actions réagissent exactement selon les règles de la 4e édition.

---

## Bugs Corrigés & Vérifications

### Bug #1 & #3 : Multi-strike et Gestion de l'Épuisement

Dans _Warlord_, certains personnages puissants peuvent attaquer plusieurs fois par tour.

- **Logique de Frappe** : Le test vérifie qu'un attaquant possédant 2 frappes reste en état `ready` (prêt) après sa première attaque. Il ne doit passer en `spent` (épuisé) qu'après sa dernière frappe ou via l'action explicite `finishAttack`.
- **Cas Standard** : À l'inverse, il garantit qu'un personnage avec une seule frappe est immédiatement épuisé après son action.

### Bug #2 : Powerattack & Nettoyage des Bonus

Le talent **Powerattack** permet d'ajouter des blessures supplémentaires.

- **Application** : Vérifie que le bonus (+1 blessure) est bien ajouté aux dégâts de base lors de la résolution.
- **Éphémérité** : Crucial pour l'équilibrage, le test s'assure que le bonus est supprimé de l'état global (`pendingAttackBonus`) immédiatement après la frappe pour ne pas s'appliquer par erreur aux attaques suivantes.

### Protection contre les Boucles de Riposte

Le système de "Réaction" permet de riposter après avoir été attaqué.

- **Anti-Boucle** : Le test `Anti-Boucle` vérifie qu'il est impossible de déclencher une riposte... en réponse à une riposte. Cela évite que deux personnages ne s'attaquent indéfiniment jusqu'à la mort dans le même tour.
- **Conservation d'état** : Une riposte ne doit pas "consommer" l'action du tour du défenseur. Le test confirme que le défenseur reste `ready` après avoir riposté.

---

## Scénarios Techniques Couverts

| Test                   | Objectif                                                     |
| :--------------------- | :----------------------------------------------------------- |
| **Multi-strike**       | Garde l'attaquant `ready` s'il lui reste des `attackValues`. |
| **Finish Attack**      | Force l'état `spent` pour clore le tour de combat.           |
| **Powerattack**        | Calcule $Dégâts_{base} + 1$ et nettoie le cache.             |
| **Riposte Protection** | Empêche l'épuisement de la carte lors d'une contre-attaque.  |
| **Cancel Attack**      | Restaure l'état `ready` si l'attaque est annulée.            |

---

## Résumé Technique

- **Composants testés** : `combatReducer.ts`, `applyResolveAttack.ts`, `getAvailableReacts.ts`.
- **Fiabilité** : Utilise `makeHero` et `makeTestState` pour isoler les variables et garantir des tests reproductibles.
- **Règles métier** : Applique strictement les priorités de la 4e édition sur le chaînage des attaques.

---
