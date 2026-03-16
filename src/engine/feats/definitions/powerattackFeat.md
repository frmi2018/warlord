# Documentation de powerattackFeat.ts

## Localisation

`src/engine/feats/definitions/powerattackFeat.ts`

## Rôle du fichier

Ce fichier définit la logique de la capacité **Powerattack** (Coup Puissant).

C'est un "Feat" de type **React** qui permet à un personnage d'augmenter les dégâts de son attaque de mêlée avant de lancer les dés de combat. Si le personnage réussit un test de compétence (Skill Check), son attaque infligera une blessure supplémentaire.

---

## Concept : Attaque de Puissance

- **Timing Précis** : Cette capacité ne peut être activée que durant la fenêtre `"before"`, c'est-à-dire après avoir déclaré l'attaque mais avant de résoudre les dégâts.
- **Auto-Ciblage** : Un personnage ne peut utiliser Powerattack que sur sa propre attaque.
- **Bonus de Dégâts** : En cas de succès, le moteur de jeu enregistre un bonus qui sera appliqué lors de la phase de résolution de l'attaque (`resolveAttack`).

---

## Fonctions principales

### `canTrigger(ctx): boolean`

Détermine si le personnage peut tenter un Powerattack.

- **Conditions** :
  - Le timing doit être `"before"`.
  - Le personnage ne doit pas être étourdi (`stunned`).
  - L'action déclencheuse doit être une attaque (`spendAttacker` pour le joueur ou `aiAttack` pour l'IA).
  - L'attaquant doit être le possesseur du Feat.

### `apply(ctx): FeatResult`

Exécute la logique du Feat une fois le dé lancé.

- **Calcul** : Somme du résultat du dé (`rollResult`) et du skill du personnage.
- **Succès (DC 20)** : Si le score atteint 20, injecte un objet `pendingAttackBonus` dans l'état du jeu. Ce bonus contient `extraWounds: 1`.
- **Échec** : L'état du jeu reste inchangé.

---

## Fonctions internes (Helpers)

### `getAttackerId(action): string | null`

Extrait l'identifiant de l'attaquant depuis l'action déclencheuse. Elle gère de manière transparente les actions provenant du joueur humain et celles de l'Intelligence Artificielle.

---

## Variables et Types importants

| Élément                | Description                                                                                             |
| :--------------------- | :------------------------------------------------------------------------------------------------------ |
| `POWERATTACK_DC`       | La difficulté du jet de compétence, fixée à **20**.                                                     |
| `pendingAttackBonus`   | Propriété du `GameState` utilisée pour stocker le bonus de dégâts en attendant la résolution du combat. |
| `requiresSpend: false` | Indique que ce Feat est un "React:" simple (ne nécessite pas d'épuiser le personnage).                  |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `featTypes.ts` → Pour les interfaces de définition et les contextes.
- `gameActions.ts` → Pour identifier les types d'attaques (`spendAttacker`, `aiAttack`).

---

## Résumé

Ce fichier est responsable de :

- **Détecter** une opportunité d'attaque puissante pour le personnage.
- **Valider** le jet de compétence (DC 20).
- **Préparer le bonus** de +1 blessure qui sera consommé lors de la résolution finale de l'attaque.
  """
