# Documentation de powerattack_and_combatReducer.test.ts

## Localisation

`src/__tests__/powerattack_and_combatReducer.test.ts`

## Rôle du fichier

Ce fichier contient les tests unitaires validant les correctifs appliqués suite au **Bug Powerattack (test-004)**.

Son rôle est de garantir que les capacités spéciales (Feats) comme Powerattack fonctionnent correctement lorsqu'elles sont utilisées par l'IA, et que les bonus de dégâts sont bien appliqués puis nettoyés pour ne pas polluer les combats suivants.

---

## Concept : Correction des Bugs #1 à #4

Le fichier est découpé en quatre sections correspondant aux quatre bugs identifiés :

1.  **Bug #1 (Détection)** : Permettre à la capacité Powerattack de s'activer aussi bien sur une attaque joueur (`spendAttacker`) que sur une attaque IA (`aiAttack`).
2.  **Bug #2 (Application du bonus)** : S'assurer que le bonus de dégâts (`pendingAttackBonus`) est lu, appliqué à la bonne cible, puis supprimé (qu'on touche ou qu'on rate la cible).
3.  **Bug #3 (Mise à jour de l'état)** : Vérifier que lorsqu'une carte IA attaque, c'est bien la carte dans la liste `aiCards` qui est inclinée (`spent`).
4.  **Bug #4 (Pipeline de Réaction)** : S'assurer que l'attaque de l'IA n'est pas "invisible" pour le système et qu'elle permet bien l'ouverture d'une fenêtre de réaction.

---

## Fonctions de test principales

### `powerattackFeat.canTrigger`

Vérifie les conditions d'activation de la capacité :

- **Vrai** si c'est le tour de l'attaquant et qu'il possède le trait.
- **Faux** si l'attaquant est étourdi (`stunned`) ou si le timing est incorrect.

### `applyResolveAttack`

Vérifie la gestion des dégâts :

- Additionne les dégâts de base et les `extraWounds` du bonus en attente.
- Nettoie systématiquement le bonus après l'attaque pour éviter qu'il ne s'applique par erreur à l'attaque suivante.
- Gère le cas où le bonus cumulé est suffisant pour éliminer la carte (envoi en défausse).

### `combatReducer`

Vérifie la manipulation des listes de cartes :

- Cible spécifiquement `aiCards` pour les actions de l'IA.
- Garantit que les cartes du joueur (`playerCards`) ne sont pas modifiées par erreur lors d'un tour adverse.

### `reactPipeline`

Vérifie l'interception des actions :

- `interceptBefore` doit pouvoir "geler" une attaque IA pour laisser les joueurs réagir.

---

## Helpers de test

- **`makeCharacter`** : Crée un personnage avec des valeurs par défaut pour les tests.
- **`makeHitResult`** : Simule un résultat de jet de dés (réussite ou échec) pour tester les conséquences d'un combat.

---

## Variables et États testés

| Élément              | Description                                                             |
| :------------------- | :---------------------------------------------------------------------- |
| `pendingAttackBonus` | Stocke le bonus temporaire (+1 dégât) de Powerattack.                   |
| `attackerId`         | Utilisé pour vérifier que le bonus appartient bien à celui qui attaque. |
| `orientation`        | Vérifie le passage de `ready` à `spent`.                                |
| `zone`               | Gère le déplacement des cartes (Ranks vs Discard).                      |

---

## Résumé

Ce fichier est le **garde-fou** de la logique de combat complexe. Il assure que :

1.  L'IA et le Joueur suivent les **mêmes règles de calcul**.
2.  Les **effets temporaires** ne restent pas actifs plus longtemps que prévu.
3.  Le moteur de jeu identifie correctement **qui attaque qui**.
