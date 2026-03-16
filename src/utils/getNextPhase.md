# Documentation de getNextPhase.ts

## Localisation

`src/engine/phases/getNextPhase.ts`

## Rôle du fichier

Ce fichier agit comme le **métronome logique** du moteur de jeu. Il définit l'ordre cyclique des phases d'un tour de _Warlord: Saga of the Storm_. Sa fonction unique est de calculer, à partir de la phase actuelle, quelle sera la prochaine étape de la partie.

Cette logique permet de faire boucler le jeu indéfiniment (ou jusqu'à la victoire) en revenant automatiquement à la première phase une fois le tour terminé.

---

## Le Cycle des Phases

Le moteur suit strictement l'ordre défini par les règles du jeu :

1.  **`Ready Phase`** : Redressement des troupes et réinitialisation des capacités.
2.  **`Draw Phase`** : Pioche de nouvelles ressources.
3.  **`Initiative Phase`** : Détermination du joueur qui agira en premier.
4.  **`Decree Phase`** : Cœur de l'action (Déploiements, Attaques, Manœuvres, Feats).
5.  **`End of Turn Phase`** : Nettoyage des bonus temporaires.

Une fois la **End of Turn Phase** terminée, l'algorithme utilise l'opérateur modulo (`%`) pour pointer de nouveau vers la **Ready Phase**, entamant ainsi un nouveau tour.

---

## Fonctionnement technique

### `getNextPhase(currentPhase)`

La fonction est une fonction pure, ce qui la rend extrêmement prévisible et facile à tester.

- **Entrée** : Une chaîne de caractères de type `GamePhase`.
- **Logique** :
  - Elle localise l'index de la phase actuelle dans le tableau constant `phases`.
  - Elle incrémente l'index de 1.
  - L'utilisation de `% phases.length` garantit que si l'on dépasse la fin du tableau (index 4), on revient à 0.
- **Sortie** : La phase suivante.

---

## Pourquoi cette architecture ?

| Avantage        | Description                                                                                                                                                               |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Simplicité**  | Centraliser l'ordre des phases dans un seul tableau évite d'éparpiller des conditions `if/else` complexes dans tout le code.                                              |
| **Évolutivité** | Si une extension du jeu nécessite l'ajout d'une phase intermédiaire (ex: "Upkeep Phase"), il suffit de l'insérer dans le tableau pour que tout le moteur de jeu s'adapte. |
| **Robustesse**  | Le typage TypeScript `GamePhase` empêche de passer une phase inexistante en paramètre, éliminant les erreurs de navigation à la compilation.                              |

---

## Résumé

Ce fichier garantit que :

- Le **déroulement du jeu** respecte l'ordre chronologique officiel.
- La **transition entre les tours** est fluide et automatique.
- La logique de progression est **prévisible** pour les autres modules (UI, IA).
