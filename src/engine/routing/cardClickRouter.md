# Documentation de cardClickRouter.ts

## Localisation

`src/engine/routing/cardClickRouter.ts`

## Rôle du fichier

Ce fichier agit comme un **aiguilleur logique**. Sa mission est de transformer un clic utilisateur sur une carte en une intention claire (une action de routage).

En extrayant cette logique de l'interface (`GameBoard`), on s'assure que les règles de sélection (qui est une cible valide ? quand peut-on ouvrir le menu d'actions ?) sont centralisées, pures et facilement testables sans avoir besoin de simuler un navigateur ou React.

---

## Concept : Routage des Intentions

Au lieu d'exécuter directement une fonction, le routeur analyse le **contexte** du jeu au moment du clic et retourne un objet typé (`CardClickResult`).

1. **Sécurité** : Si l'IA réfléchit ou si une manœuvre est en cours, le clic est ignoré (`blocked`).
2. **Contextualisation** : Si le joueur est en train de choisir une cible pour une attaque, le routeur vérifie si la carte cliquée est une cible légale (ex: une carte IA au premier rang).
3. **Exploration** : Par défaut, cliquer sur une carte ouvre ses détails ou ses options d'action (`openActions`).

---

## Fonctions principales

### `routeCardClick(ctx): CardClickResult`

La fonction maîtresse qui décide du destin du clic.

- **Bloqué** : Si `isActionBlocked`, `isAiBusy` ou `isManeuverActive` est vrai.
- **Ciblage** : Si `isSelectingTarget` est actif, elle valide que la carte appartient aux `aiRanks` et respecte la règle `isFrontRank`.
- **Menu** : Si aucune sélection n'est en cours, elle autorise l'ouverture des actions pour les cartes en main ou sur le plateau.

### `resolveAiTargets(aiRanks, isSelectingTarget): Card[]`

Une fonction utilitaire qui pré-calcule quelles cartes de l'IA peuvent être ciblées.

- Elle injecte dynamiquement la propriété `canMove` (utilisée ici visuellement comme indicateur de "ciblable") sur les cartes ennemies situées au premier rang.

---

## Types de retour (`CardClickResult`)

| Type            | Signification                                                     |
| :-------------- | :---------------------------------------------------------------- |
| `blocked`       | Le clic doit être ignoré (UI occupée ou verrouillée).             |
| `selectTarget`  | La carte cliquée est confirmée comme cible d'une action.          |
| `invalidTarget` | Le joueur essaie de cibler une carte illégale (ex: rang arrière). |
| `openActions`   | Le clic doit ouvrir le menu contextuel ou la fiche de la carte.   |

---

## Interaction avec d'autres fichiers

Ce fichier est le partenaire logique de l'UI :

- `GameBoard.tsx` : Appelle `routeCardClick` dans son gestionnaire d'événements.
- `rankRules.ts` : Fournit la règle `isFrontRank` pour déterminer l'accessibilité des cibles.
- `card.ts` : Utilise la structure des cartes pour lire les zones (`zone`) et positions (`posY`).

---

## Résumé

Ce fichier est responsable de :

- **Décharger l'UI** de la logique de décision complexe.
- **Appliquer les règles de ciblage** (on ne peut pas attaquer le rang arrière si le rang avant est occupé).
- **Verrouiller les interactions** pendant les phases critiques (tour de l'IA).
  """
