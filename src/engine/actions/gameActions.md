# Documentation de gameActions.ts

## Localisation

`src/engine/actions/gameActions.ts`

## Rôle du fichier

Ce fichier est le **pivot central de toutes les modifications d'état** du jeu.

Il définit le type `GameAction`, une union discriminée qui regroupe absolument toutes les intentions de jeu (joueur, IA ou système). En centralisant ces types ici, le moteur garantit que toute modification du `GameState` est prévisible, typée et traçable.

---

## Architecture des Actions

Le système sépare les actions selon leur origine pour appliquer des règles de validation distinctes :

1.  **PlayerAction** : Actions initiées par l'utilisateur (attaquer, déplacer, équiper).
2.  **AiAction** : Actions générées par l'algorithme de l'IA. Elles incluent souvent des résultats pré-calculés (dés, cartes mises à jour) pour garantir le déterminisme dans le reducer.
3.  **SystemAction** : Actions de maintenance automatique (ex: faire avancer une unité si le rang devant elle est vide).
4.  **PhaseAction** : Actions liées à l'interface utilisateur et aux transitions de phases globales.

---

## Types principaux

### `AiAction`

Représente les décisions de l'ordinateur.

- **Particularité (`aiAttack`)** : Contrairement au joueur, l'IA embarque le résultat de son combat (`result`) directement dans l'action. Cela évite que le `reducer` n'ait à générer de l'aléatoire, ce qui est interdit dans une architecture Redux/flux pure.

### `SystemAction`

Gère la cohérence interne du moteur.

- `fallForward` : Applique la règle de "chute vers l'avant" lorsqu'un vide se crée dans les rangs de bataille.
- `pass` : Gère le passage de tour technique.

### `PhaseAction`

Fait le pont entre l'UI (React) et le moteur.

- `deployCharacter` : Capture l'intention de déploiement depuis une modale UI avant qu'elle ne soit transformée en coordonnées précises sur le plateau.

---

## Variables et Types importants

| Élément          | Description                                                                  |
| :--------------- | :--------------------------------------------------------------------------- |
| `GameAction`     | L'union finale utilisée par le `gameReducer` et le `useActionDispatcher`.    |
| `isRiposte`      | Drapeau utilisé dans les actions d'attaque pour signaler une contre-attaque. |
| `updatedAiCards` | Version pré-calculée des cartes IA après une résolution de combat.           |

---

## Interaction avec d'autres fichiers

Ce fichier est le **cœur de la communication** pour :

- `gameReducer.ts` : Utilise `GameAction` pour savoir comment transformer le `GameState`.
- `playerActions.ts` : Fournit la base des actions utilisateur.
- `useActionDispatcher.ts` : Le hook qui distribue ces actions à travers l'application.
- `riposteFeat.ts` / `powerattackFeat.ts` : Utilisent ces types pour identifier les triggers de réaction.

---

## Résumé

Ce fichier est la **colonne vertébrale du flux de données**. Il :

- Standardise la façon dont le monde extérieur interagit avec l'état du jeu.
- Permet à l'IA et au Joueur de partager la même logique de résolution.
- Garantit que le moteur de jeu reste "pur" en transportant les résultats aléatoires (dés) à l'intérieur des actions.
  """
