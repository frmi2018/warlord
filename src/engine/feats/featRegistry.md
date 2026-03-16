# Documentation de featRegistry.ts

## Localisation

`src/engine/feats/featRegistry.ts`

## Rôle du fichier

Ce fichier sert de **point d'entrée centralisé** pour tous les "Feats" (capacités spéciales) de type **React** du jeu.

C'est un registre qui répertorie chaque définition de Feat afin que le moteur puisse les identifier et les exécuter sans avoir à importer chaque capacité individuellement dans le reste du code.

---

## Concept : Le Registre de Feats

Le registre fonctionne comme un annuaire :

1.  **Centralisation** : Toutes les capacités de réaction (comme _Defend_, _Riposte_, _Stealth_) sont regroupées ici.
2.  **Modularité** : Pour ajouter une nouvelle capacité au jeu, il suffit de créer sa définition dans le dossier `definitions/` et de l'ajouter à la liste `FEAT_REGISTRY`.
3.  **Filtrage** : Le registre ne contient que les définitions brutes. Le moteur se chargera ensuite de vérifier lesquels sont utilisables selon la situation (via `getAvailableReacts`).

---

## Fonctions principales

### `getFeatRegistry(): FeatDefinition[]`

Retourne la liste complète de tous les Feats enregistrés dans le système.
_Note : Cette fonction est principalement utilisée par le moteur interne, l'UI doit passer par des filtres plus spécifiques._

### `getFeat(name: FeatName): FeatDefinition | undefined`

Permet de récupérer la logique et les données d'une capacité spécifique à partir de son nom.

- **name** : Le nom technique du Feat (ex: `"marksmanship"`).
- **Retourne** : La `FeatDefinition` correspondante ou `undefined` si le nom n'existe pas dans le registre.

---

## Variables et Types importants

| Élément          | Description                                                              |
| :--------------- | :----------------------------------------------------------------------- |
| `FEAT_REGISTRY`  | Tableau constant contenant toutes les instances de `FeatDefinition`.     |
| `FeatDefinition` | Interface décrivant la structure d'un Feat (coût, conditions, effets).   |
| `FeatName`       | Type (Union de strings) représentant tous les noms valides de capacités. |

---

## Interaction avec d'autres fichiers

Ce fichier dépend de :

- `featTypes.ts` & `reactTypes.ts` → Pour les définitions de types et les noms de réactions.
- `definitions/` → Importe chaque fichier de capacité individuelle (ex: `defendFeat.ts`).

---

## Résumé

Ce fichier est responsable de :

- **Répertorier** toutes les capacités de réaction disponibles.
- **Fournir un accès rapide** aux définitions des Feats par leur nom.
- **Faciliter l'extension du jeu** en offrant un endroit unique pour enregistrer de nouveaux pouvoirs.
  """
