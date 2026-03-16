# Documentation de gameReducer.ts

## Localisation

`src/engine/gameReducer.ts`

## Rôle du fichier

Le `gameReducer` est le **cerveau logistique** du moteur. C'est l'unique point d'entrée pour toute modification de l'état global du jeu (`GameState`).

Son rôle n'est pas de calculer les règles du jeu, mais de **router** chaque action reçue vers le sous-reducer spécialisé (domaine) compétent. Cette architecture permet de garder un code propre, modulaire et extrêmement facile à tester.

---

## Architecture : Le Dispatcher Central

Le `gameReducer` agit comme un commutateur (switch) géant qui classifie les actions par thématiques :

1.  **Domaine Combat** (`combatReducer`) : Encaisse tout ce qui touche aux attaques, blessures et résolutions de duel.
2.  **Domaine Déploiement** (`deployReducer`) : Gère l'entrée en jeu des cartes, que ce soit via l'UI ou l'IA.
3.  **Domaine Actions** (`actionReducer`) : Centralise les manœuvres, l'équipement d'objets et l'utilisation de cartes actions.
4.  **Domaine Réactions** (`reactReducer`) : Pilote le flux complexe des fenêtres de réaction (Reacts).
5.  **Domaine Système** (`systemReducer`) : S'occupe de la maintenance automatique (nettoyage des rangs, fin de tour).

---

## Pipeline d'exécution

Il est crucial de comprendre que ce reducer est **100% pur**. Il ne sait pas si une action vient d'un bouton React ou d'un test unitaire.

Le flux standard est :

1.  **Dispatcher** : L'UI envoie une `GameAction`.
2.  **Validation** : L'action est vérifiée par `validateAction`.
3.  **Routage** : `gameReducer` l'envoie au bon sous-reducer.
4.  **Mutation** : Un nouvel état est produit et le jeu se met à jour.

---

## Pourquoi cette séparation ?

- **Testabilité** : Vous pouvez tester le `combatReducer` sans charger toute la logique de déploiement.
- **Maintenance** : Si vous ajoutez un nouveau type de capacité (ex: Magie), vous créez un `magicReducer` et ajoutez simplement un cas dans le switch central.
- **Débogage** : En lisant les logs, vous voyez exactement quel domaine a modifié l'état.

---

## Variables et Types importants

| Élément         | Description                                                                                                        |
| :-------------- | :----------------------------------------------------------------------------------------------------------------- |
| `GameAction`    | L'union de toutes les actions possibles dans le jeu.                                                               |
| `GameState`     | L'objet représentant l'intégralité de la partie à un instant T.                                                    |
| `default throw` | Le reducer lance une erreur si une action inconnue arrive, garantissant qu'aucun bug de routage ne passe inaperçu. |

---

## Interaction avec d'autres fichiers

Ce fichier est le **nœud de raccordement** pour :

- `gameActions.ts` : Source de tous les types d'actions.
- `reducers/` : Répertoire contenant tous les spécialistes (combat, deploy, etc.).
- `applyAction.ts` : Le moteur qui appelle physiquement ce reducer.

---

## Résumé

Le `gameReducer` est le **garant de l'organisation** du moteur. Il :

- **Organise** les actions par domaine de compétence.
- **Simplifie** l'ajout de nouvelles fonctionnalités.
- **Assure** qu'aucune action n'est oubliée ou mal dirigée.
  """
