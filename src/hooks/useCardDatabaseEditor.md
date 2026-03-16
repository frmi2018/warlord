# Documentation de useCardDatabaseEditor.ts

## Localisation

`src/hooks/useCardDatabaseEditor.ts`

## Rôle du fichier

Ce hook est le **moteur de gestion de données** pour l'interface d'édition des cartes. Il permet de manipuler la base de données brute des cartes (`cardDatabase`) en fournissant une interface CRUD (Create, Read, Update, Delete) simplifiée.

Il résout un problème majeur : la **désynchronisation des formats**. Les données stockées (`DeckCard`) sont optimisées pour le moteur de jeu (tableaux de nombres, types stricts), tandis que les formulaires UI manipulent principalement des chaînes de caractères (CSV) pour faciliter la saisie.

---

## Mécanismes de Transformation (Sérialisation)

Le hook s'appuie sur des fonctions de transformation pures pour faire le pont entre l'état du formulaire et l'état du jeu :

### 1. `deckCardToForm` (Lecture)

Transforme une carte complexe en un objet plat `CardFormState`.

- **CSV** : Les tableaux (traits, bonus) deviennent des chaînes séparées par des virgules.
- **Actions** : Les capacités spéciales (`[type, description][]`) sont sérialisées en texte (`type|description;type|description`).

### 2. `formToDeckCard` (Écriture)

Nettoie et convertit les saisies utilisateur en types utilisables par le moteur.

- **Parsing** : Transforme les chaînes de caractères en tableaux de nombres (ex: `attackValues`).
- **Nettoyage** : Supprime les clés vides (`traits`, `bonus`) pour garder un objet JSON léger et propre.

---

## Gestion de l'état d'édition

Le hook gère deux modes distincts via `editMode` :

- **Mode "add"** : Prépare un formulaire vide (`DEFAULT_FORM`) pour une nouvelle création.
- **Mode "edit"** : Verrouille l'ID d'une carte existante et remplit le formulaire avec ses données actuelles.

---

## Fonctions principales

### `saveCard()`

La fonction pivot. Elle :

1.  **Valide** les données (ID unique, nom présent).
2.  **Convertit** le formulaire en carte via `formToDeckCard`.
3.  **Met à jour** la liste locale des cartes (ajout ou remplacement).
4.  **Réinitialise** l'interface pour l'action suivante.

### `moveCard(id, direction)`

Permet de réorganiser l'ordre des cartes dans la base de données. C'est essentiel pour l'exportation ou la hiérarchie d'affichage dans le constructeur de deck.

### `updateField(key, value)`

Un setter générique type-safe qui met à jour une seule propriété du formulaire tout en effaçant les erreurs de validation précédentes.

---

## Interaction avec le Système

- **`cardDatabase.ts`** : La source de données initiale importée.
- **`DeckCard` (Types)** : Le contrat technique que les données éditées doivent respecter à la sortie.
- **UI Editor** : Ce hook est consommé par un composant d'administration (non inclus ici) qui lie les inputs aux fonctions retournées.

---

## Résumé

Ce fichier est responsable de :

- **Simplifier la saisie** de données complexes via des formats texte (CSV).
- **Garantir l'intégrité** de la base de données (IDs uniques, types respectés).
- **Gérer les états visuels** de l'éditeur (erreurs, mode édition vs ajout).
- **Fournir une liste de cartes ordonnée** et manipulable.
  """
