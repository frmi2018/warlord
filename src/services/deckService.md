# Documentation de deckService.ts

## Localisation

`src/services/deckService.ts`

## Rôle du fichier

Ce service est la passerelle exclusive entre l'application et la table des decks dans **Supabase**. Il contient les fonctions asynchrones permettant de lire, créer et supprimer les decks des utilisateurs.

Il est conçu sans aucune dépendance à React, ce qui permet de l'utiliser dans n'importe quel contexte de logique métier ou de script utilitaire.

---

## Structure de la Table (`player_decks`)

Le service interagit avec une table SQL structurée pour stocker la complexité d'une armée Warlord :

| Colonne              | Type     | Description                                            |
| :------------------- | :------- | :----------------------------------------------------- |
| `id`                 | `uuid`   | Clé primaire unique générée par la base.               |
| `user_id`            | `uuid`   | Lien vers l'utilisateur (Auth Supabase).               |
| `card_ids`           | `text[]` | Tableau de tous les identifiants de cartes du deck.    |
| `starting_formation` | `jsonb`  | Objet JSON décrivant le placement des Rangs 1, 2 et 3. |

---

## Sécurité (RLS - Row Level Security)

Le fichier contient les instructions SQL nécessaires pour sécuriser les données. Grâce à ces politiques, **un joueur ne peut jamais accéder aux decks d'un autre joueur**, même s'il connaît l'ID du deck.

- **Politique `select`** : Autorisé uniquement si `auth.uid() == user_id`.
- **Politique `insert`** : L'utilisateur ne peut insérer que des decks liés à son propre ID.
- **Politique `delete`** : Suppression restreinte au propriétaire légitime.

---

## Fonctions de l'API

### 1. Mapping de Données

- **`rowToSavedDeck`** : Cette fonction interne transforme le format "snake_case" de la base de données (ex: `warlord_id`) en format "camelCase" utilisé dans le code TypeScript (`warlordId`). Elle assure également le typage correct du champ JSONB de formation.

### 2. Récupération (`fetchUserDecks`)

Récupère tous les decks d'un utilisateur, triés par date de création (du plus récent au plus ancien).

- **Retour** : Un tableau d'objets `SavedDeck`. En cas d'erreur, retourne un tableau vide pour éviter de faire planter l'interface.

### 3. Création (`createUserDeck`)

Insère une nouvelle configuration de deck.

- **Validation** : Effectue un `.trim()` sur le nom du deck pour éviter les noms vides ou remplis d'espaces.
- **Retour** : L'objet complet créé (incluant l'ID généré) ou `null` en cas d'échec.

### 4. Suppression (`deleteUserDeck`)

Supprime définitivement un deck de la base de données.

- **Retour** : Un booléen `true` si l'opération a réussi.

---

## Pourquoi cette architecture ?

- **Consistance** : Centraliser les appels à Supabase ici permet de modifier la structure de la base de données à un seul endroit sans impacter les composants UI.
- **Typage Fort** : L'utilisation de types TypeScript (`SavedDeck`, `CreateDeckPayload`) garantit que les données envoyées au serveur sont toujours conformes aux attentes du moteur de jeu.
- **Gestion d'Erreurs** : Chaque fonction capture ses propres erreurs et les loggue en console avec un préfixe clair (`[deckService]`), facilitant le débogage en production.

---

## Résumé

Ce fichier garantit que :

- La **persistance des données** est robuste et sécurisée.
- La **traduction des formats** entre la base de données et l'application est transparente.
- Les **règles de sécurité** de Supabase sont respectées à chaque requête.
