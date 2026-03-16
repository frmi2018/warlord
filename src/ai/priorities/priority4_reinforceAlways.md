# priority4_reinforceAlways.ts

## Localisation

`src/ai/priorities/priority4_reinforceAlways.ts`

## Rôle du fichier

La priorité **"Reinforce Always"** est la logique de développement de dernier recours de l'IA. Elle intervient lorsque l'IA n'a pas pu attaquer ou se développer en toute sécurité. Ici, l'IA décide qu'il est préférable de jouer une carte, même si la situation est tendue (présence d'ennemis prêts à frapper), plutôt que de ne rien faire.

C'est ce module qui garantit que l'IA remplit ses rangs et utilise ses ressources pour ne pas se laisser distancer par le joueur.

---

## Logique de Décision (Sous-priorités)

L'IA suit quatre étapes pour optimiser son déploiement sous pression :

### 4a & 4b. Déploiement de Personnages

L'IA cherche d'abord à poser des combattants :

- **Priorité au Rang 1** : Elle cherche d'abord des personnages de niveau 1 pour occuper la ligne de front.
- **Priorité au Niveau** : S'il n'y a pas de contrainte de rang, elle choisit toujours le personnage avec le plus haut niveau (`level`) en main pour maximiser sa puissance brute sur le plateau.

### 4c. Équipement Tactique (Sécurité relative)

L'IA souhaite équiper ses objets, mais elle est prudente :

- Elle n'équipera **JAMAIS** une unité au Rang 1 si un ennemi est "Ready" en face.
- **Pourquoi ?** Équiper une carte "Spend" (épuise) le personnage. Faire cela devant un ennemi prêt à attaquer reviendrait à offrir une cible gratuite sans défense.

### 4d. Équipement de l'Arrière-garde

Si le front est trop dangereux, l'IA équipe ses objets sur ses unités de soutien ou ses archers situés aux rangs 2 et 3, là où le risque de riposte immédiate est moindre.

---

## Concepts Clés

- **Gestion du Risque** : Contrairement à la priorité 2 (_ReinforceIfSafe_), celle-ci accepte de poser des personnages même si le joueur menace le front, car une unité sur le terrain vaut mieux qu'une unité en main.
- **Validation des Niveaux** : L'IA utilise une réduction (`reduce`) pour toujours identifier la carte la plus "chère" ou puissante légalement jouable, assurant une montée en puissance constante.

---

## Interaction avec les Helpers

Ce fichier s'appuie fortement sur `aiHelpers.ts` pour rester lisible :

- **`getPlayableAiHandCards`** : Filtre les cartes de la main que l'IA a les moyens de poser.
- **`getBestEquipAction`** : Scanne les combinaisons Personnage + Item pour trouver le meilleur bonus disponible.
- **`getPlayerReadyTargets`** : Identifie les menaces immédiates du joueur (unités prêtes à attaquer).

---

## Résumé

Ce fichier est responsable de :

- **Assurer la croissance** de l'armée de l'IA tour après tour.
- **Prioriser les personnages** par rapport aux objets.
- **Éviter les erreurs fatales** (épuiser un défenseur de première ligne par un équipement mal calculé).
- **Consommer les ressources** de manière optimale en choisissant les cartes de plus haut niveau.
