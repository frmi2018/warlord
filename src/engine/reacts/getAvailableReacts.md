# Documentation de getAvailableReacts.ts

## Localisation

`src/engine/reacts/getAvailableReacts.ts`

## Rôle du fichier

Ce fichier calcule **toutes les réactions (Reacts) possibles** qu'un
joueur peut jouer lorsqu'une **fenêtre React** est ouverte.

Il analyse : - les **personnages en jeu** - leurs **feats (capacités
spéciales)** - les **actions React dans le texte des cartes** - les
**items équipés** - les **cartes Action React dans la main**

Le résultat est une liste d'objets `AvailableReact` qui seront proposés
au joueur ou à l'IA.

---

# Fonction principale

## `getAvailableReacts()`

```ts
getAvailableReacts(
  owner: "player" | "ai",
  triggerAction: GameAction,
  state: GameState,
  timing: ReactTiming
): AvailableReact[]
```

### Paramètres

Paramètre Description

---

`owner` Le joueur qui peut réagir (`player` ou `ai`)
`triggerAction` L'action qui a déclenché la fenêtre React
`state` L'état complet du jeu
`timing` Moment de la réaction (`before` ou `after`)

### Retour

Une liste de **réactions possibles** (`AvailableReact[]`).

Chaque réaction contient : - le personnage qui agit - la source de la
réaction - la carte ou feat utilisé - si l'action nécessite de
**dépenser** le personnage - le timing

---

# Étapes de calcul

La fonction recherche les réactions dans **4 sources différentes**.

---

## 1. Réactions venant des Feats

Chaque personnage en jeu peut posséder des **traits (traits)**.

Ces traits correspondent à des **Feats** enregistrés dans le
`featRegistry`.

Pour chaque personnage : 1. On vérifie si le personnage possède le trait
correspondant. 2. On appelle `feat.canTrigger()` pour vérifier si la
réaction est possible. 3. On vérifie : - si la réaction a déjà été
utilisée dans la fenêtre React - si elle est limitée à **une fois par
tour** - si le personnage est **stunned** ou **spent**.

Si tout est valide, la réaction est ajoutée.

Fonction utilisée :

    getFeatReactsForCharacter()

---

## 2. Réactions dans le texte des cartes de personnage

Certaines cartes peuvent contenir des **actions "react" dans leur
texte**.

Exemple :

    ["react", "Annule une attaque"]

La fonction :

    getCardTextReacts()

analyse les actions de la carte et ajoute les réactions possibles.

---

## 3. Réactions provenant des items équipés

Les **items équipés par un personnage** peuvent aussi contenir des
actions `react`.

Le système : 1. parcourt les `equippedItems` 2. vérifie les actions
React 3. les ajoute aux réactions disponibles.

---

## 4. Cartes Action React dans la main

Les cartes Action en main peuvent aussi être utilisées comme réaction.

Conditions : - la carte doit contenir une action `react` - un personnage
doit être **capable de la jouer** - elle ne doit pas avoir été utilisée
dans cette fenêtre React - elle ne doit pas avoir été utilisée **plus
d'une fois ce tour**.

Pour vérifier si un personnage peut jouer la carte :

    findEligiblePerformer()

Cette fonction vérifie : - le **niveau du personnage** - la **classe
compatible** (Fighter, Wizard, Rogue, Cleric) - ou si la carte est
**universelle** (`oval`).

---

# Cas particulier : attaque ratée

Si l'action déclencheuse est une **attaque ratée** :

    resolveAttack
    aiAttack

et que :

    hits === false

alors **aucune réaction "after" n'est possible**.

La fonction retourne immédiatement :

    []

---

# Fonctions internes

## `getFeatReactsForCharacter()`

Calcule les réactions venant des **Feats d'un personnage**.

Vérifie : - les conditions du feat (`canTrigger`) - les limites
d'utilisation - l'état du personnage (spent / stunned).

---

## `getCardTextReacts()`

Cherche les **actions "react" dans le texte d'une carte**.

Fonctionne pour : - les cartes personnage - les items - certaines cartes
action.

---

## `findEligiblePerformer()`

Trouve **le personnage capable d'utiliser une carte action**.

Vérifie : - le niveau minimum - la compatibilité de classe.

---

# Données importantes

Élément Description

---

`reactsPlayed` liste des réactions déjà jouées dans la fenêtre
`reactUsedThisTurn` compteur d'utilisation par tour
`charactersInPlay` personnages actifs du joueur
`equippedItems` objets équipés par un personnage
`featRegistry` registre global des Feats du jeu

---

# Résumé

Ce fichier est **le moteur de détection des réactions possibles**.

Il : - analyse toutes les cartes pertinentes - applique les règles du
jeu - empêche les **boucles ou doubles réactions** - retourne une liste
propre de **Reacts utilisables**.

C'est une pièce centrale du système **React Window** du moteur de jeu.
