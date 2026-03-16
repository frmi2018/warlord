# Documentation de main.tsx

## Localisation

`src/main.tsx`

## Rôle du fichier

Ce fichier est le **point d'entrée d'exécution** de l'application. Son rôle est d'amorcer l'environnement de jeu avant même que le premier pixel ne soit affiché. Il fait le pont entre ton code TypeScript et le DOM (Document Object Model) du navigateur.

Il ne se contente pas de rendre le composant racine ; il orchestre également les vérifications de sécurité et l'enregistrement des écouteurs d'événements globaux du moteur.

---

## Logique d'Amorçage (Bootstrap)

Le fichier suit une séquence d'initialisation précise en trois étapes :

### 1. Validation de la Base de Données (Mode DEV uniquement)

Pour garantir que les nouvelles cartes ajoutées respectent les règles du moteur :

- Le script appelle `validateCardDatabase` au chargement.
- Si des erreurs sont détectées (ex: un personnage sans PV), un rapport détaillé est affiché dans la console.
- **Optimisation** : Cette vérification est ignorée en production (`import.meta.env.DEV`) pour ne pas impacter les performances des joueurs.

### 2. Enregistrement des Handlers Globaux

Appelle `registerGlobalHandlers()` pour préparer le moteur d'effets.

- Cela permet d'écouter des événements système qui ne sont pas liés à un composant React spécifique (ex: calculs de triggers "OnTurnStart" ou gestion des modificateurs globaux).

### 3. Rendu React (StrictMode)

L'application est montée dans l'élément `#root` de ton fichier HTML.

- **`StrictMode`** : Activé pour détecter les effets de bord inattendus et les pratiques dépréciées pendant le développement.

---

## Pourquoi cette architecture ?

| Avantage                          | Description                                                                                                                                                      |
| :-------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Fail-Fast**                     | En validant la base de données dès le démarrage, on évite que des bugs de données ne se manifestent en pleine partie, là où ils sont plus difficiles à débugger. |
| **Initialisation Centralisée**    | Garantit que les services globaux (effets, handlers) sont prêts avant que le composant `App` ne commence à gérer l'état du jeu.                                  |
| **Séparation des Préoccupations** | `main.tsx` gère l'environnement, `App.tsx` gère la navigation, et le moteur gère les règles.                                                                     |

---

## Résumé

`main.tsx` est la **fondation** sur laquelle repose l'application. Il s'assure que les données sont intègres et que le moteur est prêt à réagir aux effets de cartes avant de passer le relais à l'interface utilisateur.
