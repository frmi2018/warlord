# Documentation de reactReducer.ts

## Localisation

`src/engine/reducers/reactReducer.ts`

## Rôle du fichier

Ce fichier est le **reducer de domaine** dédié à la gestion du système de réactions (Reacts). Il centralise les décisions prises par les joueurs (humains ou IA) lorsqu'une fenêtre de réaction est ouverte.

Il sert d'interface entre les intentions de réaction et les handlers qui modifient concrètement l'état du jeu, en assurant que la logique de résolution reste identique quelle que soit la source de l'action.

---

## Concept : Unification des Réactions

Le système de réaction est conçu pour être symétrique. Ce reducer applique une stratégie de "pontage" (bridging) :

1.  **Abstraction de la source** : Le système distingue `react` (humain) de `aiReact` (IA) uniquement pour la validation (le joueur humain ne peut pas parler durant le tour de l'IA).
2.  **Convergence des Handlers** : Une fois la validation passée, les actions de l'IA sont reformatées en actions standards pour être traitées par `applyReact` et `applyReactPass`.
3.  **Gestion du "Pass"** : Le fait de passer son tour de réaction est l'action la plus courante. Le reducer traite `aiReactPass` et `reactPass` de manière strictement identique via `applyReactPass`.

---

## Actions gérées

| Action            | Origine | Description                                                       |
| :---------------- | :------ | :---------------------------------------------------------------- |
| **`react`**       | Joueur  | Déclenche l'utilisation d'une carte ou d'un Feat en réponse.      |
| **`aiReact`**     | IA      | Idem, mais inclut le résultat du dé (`roll`) pré-généré par l'IA. |
| **`reactPass`**   | Joueur  | Le joueur décline l'opportunité de réagir.                        |
| **`aiReactPass`** | IA      | L'IA décline l'opportunité de réagir.                             |

---

## Logique de Traitement

### `react` & `aiReact`

Ces actions appellent `applyReact`. Dans le cas de l'IA, le reducer extrait les propriétés nécessaires (`performerId`, `featName`, `cardId`, `roll`) pour recréer une action de type `react` standard. Cela garantit que la logique de coût (épuisement du personnage) et d'effet est uniforme.

### `reactPass` & `aiReactPass`

Ces deux actions convergent vers `applyReactPass(state)`. Ce handler est responsable de :

- Faire progresser le tour de réaction vers le joueur suivant.
- Gérer la clôture de la fenêtre si tous les joueurs ont passé consécutivement.

---

## Variables et Types importants

| Élément          | Description                                                             |
| :--------------- | :---------------------------------------------------------------------- |
| `ReactAction`    | Sous-type de `GameAction` regroupant les types de réactions.            |
| `performerId`    | L'identifiant de la carte qui tente de réagir.                          |
| `applyReact`     | Handler qui résout l'effet de la capacité et met à jour le `GameState`. |
| `applyReactPass` | Handler qui gère le flux de passage de tour dans la fenêtre React.      |

---

## Interaction avec d'autres fichiers

Ce fichier est un maillon essentiel pour :

- `gameReducer.ts` : Lui délègue toutes les actions liées aux réactions.
- `handlers/applyReact.ts` : Contient la logique métier lourde (calcul des effets, succès/échec).
- `validateAction.ts` : Gère la permission de lancer ces actions pour le joueur humain.

---

## Résumé

Ce fichier est responsable de :

- **Router les décisions de réaction** vers les bons modificateurs d'état.
- **Maintenir la symétrie** entre les capacités de l'IA et celles du joueur humain.
- **Simplifier le flux de données** en transformant les actions spécifiques à l'IA en formats standards avant traitement.
  """
