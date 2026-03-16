# Documentation de reactPipeline.ts

## Localisation

`src/engine/reacts/reactPipeline.ts`

## Rôle du fichier

Ce fichier sert de **pont entre le pipeline d'actions du jeu et le
système de Reacts**.

Son objectif est de déterminer **si une action doit ouvrir une fenêtre
de réaction (React Window)** : - **avant** l'exécution de l'action
(`before`) - **après** l'exécution de l'action (`after`)

Il intercepte les actions qui passent dans le moteur et décide si : -
l'action doit être **gelée temporairement** - une **fenêtre React doit
être ouverte** - ou si l'action peut continuer normalement.

---

# Concept : Pipeline d'action

Lorsqu'une action est dispatchée dans le moteur :

    dispatch(action)
       ↓
    interceptBefore
       ↓
    executeHandler (reducer)
       ↓
    interceptAfter

Le fichier `reactPipeline.ts` intervient dans **les deux phases
d'interception**.

---

# Fonction `interceptBefore()`

```ts
interceptBefore(action: GameAction, state: GameState)
```

Cette fonction vérifie si une **fenêtre React "before"** doit s'ouvrir.

### Fonctionnement

1.  Vérifie si l'action est **interne au système React**
2.  Vérifie qu'aucune fenêtre React n'est déjà ouverte
3.  Vérifie qu'aucune action n'est déjà en attente
    (`pendingTriggerAction`)
4.  Appelle `opensReactWindow()` pour savoir si l'action peut déclencher
    une réaction

Si oui : - une **React Window est créée** - l'action est stockée dans
`pendingTriggerAction` - le pipeline est **gelé (`frozen = true`)**

L'action sera exécutée **après la fermeture de la fenêtre React**.

---

# Fonction `interceptAfter()`

```ts
interceptAfter(action: GameAction, stateAfter: GameState)
```

Cette fonction vérifie si une **fenêtre React "after"** doit s'ouvrir.

### Fonctionnement

1.  Ignore les actions internes au système React
2.  Nettoie `pendingTriggerAction`
3.  Vérifie si une fenêtre React doit s'ouvrir
4.  Si oui → crée une nouvelle React Window

Contrairement au `before`, l'action a **déjà été exécutée** à ce moment.

---

# Actions internes React

Certaines actions **ne doivent jamais déclencher de fenêtre React**.

Elles sont définies dans :

    REACT_INTERNAL_ACTIONS

Liste actuelle :

- `react`
- `reactPass`
- `aiReact`
- `aiReactPass`
- `pass`
- `fallForward`
- `deployCharacter`

Ces actions font partie : - du **mécanisme React** - ou d'**actions
système non-interruptibles**.

---

# Correction importante : Bug Powerattack IA

Avant la correction :

    aiAttack ∈ REACT_INTERNAL_ACTIONS

Conséquence :

Les attaques de l'IA **ne pouvaient jamais ouvrir de React Window
"before"**.

Donc le feat **Powerattack** (timing `before`) **ne pouvait jamais se
déclencher pour l'IA**.

---

Après correction :

    aiAttack retiré de REACT_INTERNAL_ACTIONS

Résultat :

Les attaques de l'IA peuvent maintenant : - déclencher **Powerattack
(before)** - déclencher **Riposte / Defend / Stealth (after)**

exactement comme les attaques du joueur.

---

# Exemple de flux complet (attaque IA)

    dispatch(aiAttack)
       ↓
    interceptBefore(aiAttack)
       ↓
    Powerattack possible ?
       ↓
    OUI
       ↓
    React Window ouverte
       ↓
    IA joue Powerattack
       ↓
    fenêtre fermée
       ↓
    executeHandler(aiAttack)
       ↓
    interceptAfter(aiAttack)
       ↓
    Riposte / Defend possibles

---

# Fonctions utilisées

---

Fonction Rôle

---

`opensReactWindow()` Vérifie si une action peut
ouvrir une fenêtre React

`buildReactWindow()` Construit l'objet représentant
la fenêtre React

---

---

# Données importantes

Élément Description

---

`reactWindow` fenêtre React active
`pendingTriggerAction` action en attente d'exécution
`lastReactResult` résultat du dernier React joué
`frozen` indique si le pipeline est bloqué

---

# Résumé

Ce fichier gère **l'intégration du système React dans le moteur
d'action**.

Il : - intercepte les actions - ouvre les fenêtres React si nécessaire -
suspend temporairement l'exécution d'une action - relance l'action après
la phase de réaction

C'est un **composant clé du moteur de réactions**.
