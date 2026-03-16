# Documentation de deployCharacterAi.ts

## Localisation

`src/utils/deployCharacterAi.ts`

## Rôle du fichier

Ce fichier est le pendant de `deployCharacter.ts`, spécifiquement optimisé pour l'**Intelligence Artificielle**. Il gère la logique de déploiement des personnages depuis la main de l'IA (`aiHand`) vers ses rangs (`aiRanks`).

La séparation en deux fichiers permet de simplifier le code : là où le joueur a besoin d'une interface complexe pour choisir précisément sa position (slots), l'IA applique une stratégie simplifiée de remplissage automatique.

---

## Logique de l'IA

### 1. Stratégie de Placement

Contrairement au joueur qui peut insérer une carte entre deux autres, l'IA utilise une règle de **décalage systématique** :

- La nouvelle carte est toujours placée à l'**index 0** (extrême gauche du rang).
- Les cartes déjà présentes dans ce rang voient leur position horizontale (`posX`) incrémentée de +1.

### 2. Validation des Règles

L'IA est soumise aux mêmes contraintes strictes que le joueur pour garantir l'équité de la partie :

- **Niveau/Rang** : Le personnage doit être posé dans le rang égal à son niveau.
- **Support** : Un rang $N$ ne peut être occupé que si le rang $N-1$ possède au moins une unité.
- **Pyramide** : Vérification via `checkIllegalRanks` pour s'assurer que l'IA ne surcharge pas ses rangs arrière.
- **Unicité** : Interdiction de poser un deuxième exemplaire d'une carte `unique`.

---

## Fonctionnement des Fonctions

### `canDeployAi(card, aiCards)`

Analyse la légalité du coup pour l'IA.

- **Loyalty Penalty** : Calcule automatiquement si l'IA va subir une pénalité d'allégeance (si la faction du personnage diffère de celle de son Warlord).
- **Simulation** : Crée une carte temporaire dans un environnement virtuel pour tester la validité de la formation avant de confirmer l'action.

### `deployCharacterAi(card, aiCards, hasLoyaltyPenalty)`

Exécute le mouvement de la carte.

- **Mutation de Zone** : Change la zone de `aiHand` à `aiRanks`.
- **Application du Stun** : Si `hasLoyaltyPenalty` est vrai, l'orientation de la carte est immédiatement fixée sur `stunned`.

---

## Comparaison : Joueur vs IA

| Caractéristique       | Version Joueur                       | Version IA                         |
| :-------------------- | :----------------------------------- | :--------------------------------- |
| **Position (`posX`)** | Choix manuel via des slots UI.       | Toujours position 0 (gauche).      |
| **Interface**         | Nécessite des labels et des modales. | Exécution directe en arrière-plan. |
| **Contraintes**       | Identiques (Règles 4e Édition).      | Identiques (Règles 4e Édition).    |

---

## Pourquoi cette architecture ?

1.  **Performance** : En évitant de calculer des "slots" de déploiement visuels pour l'IA, on économise des cycles de calcul inutiles.
2.  **Maintenance** : Si une règle de déploiement change (ex: une nouvelle extension), il est facile de comparer les deux fichiers pour s'assurer que l'IA et le joueur restent synchronisés.
3.  **Prédictibilité** : Le placement à l'extrême gauche rend le comportement de l'IA prévisible et facile à déboguer lors des tests de formation.

---

## Résumé

Ce fichier garantit que :

- L'IA respecte scrupuleusement les **règles de déploiement**.
- La **formation de l'IA** évolue de manière logique et fluide.
- Le moteur de jeu peut traiter les tours de l'ordinateur de manière **autonome et rapide**.
