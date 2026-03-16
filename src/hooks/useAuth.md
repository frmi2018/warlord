# Documentation de useAuth.ts

## Localisation

`src/hooks/useAuth.ts`

## Rôle du fichier

Ce hook React est le **point d'entrée central pour la gestion des utilisateurs**. Il orchestre la communication avec Supabase Auth pour l'authentification et gère la synchronisation du profil utilisateur (pseudo).

Il permet à n'importe quel composant de l'application de savoir qui est connecté et de déclencher des actions de connexion, d'inscription ou de déconnexion.

---

## Fonctionnalités Principales

### 1. Gestion de la Session

Le hook écoute en temps réel les changements d'état (via `onAuthStateChange`). Si un utilisateur se connecte ou se déconnecte dans un autre onglet, l'application se met à jour automatiquement.

### 2. Double Stockage (Auth + Profil)

Contrairement à une authentification standard, ce hook lie deux informations :

- **L'identité** : Gérée par Supabase (email, mot de passe).
- **Le Pseudo (`username`)** : Stocké dans une table personnalisée pour permettre d'afficher un nom d'utilisateur humain plutôt qu'une adresse email.

### 3. Cycle de Vie "Online/Offline"

Lors de la déconnexion (`logout`), le hook informe le service `onlinePlayersService` pour retirer l'utilisateur de la liste des joueurs actifs avant de fermer la session.

---

## Détail de l'Interface (`UseAuthReturn`)

| Propriété    | Type              | Description                                                           |
| :----------- | :---------------- | :-------------------------------------------------------------------- |
| `session`    | `Session \| null` | Contient les jetons d'accès et les infos de base Supabase.            |
| `username`   | `string \| null`  | Le pseudo du joueur récupéré depuis son profil.                       |
| `isLoading`  | `boolean`         | `true` pendant la vérification de session ou le chargement du profil. |
| `login()`    | `function`        | Connecte l'utilisateur avec email/password.                           |
| `register()` | `function`        | Crée un compte ET un profil avec pseudo unique.                       |
| `logout()`   | `function`        | Nettoie la session et passe le joueur en "Hors-ligne".                |

---

## Gestion des Erreurs Utilisateur

Le hook intègre un formateur d'erreurs (`formatAuthError`) pour transformer les codes techniques de la base de données en messages compréhensibles :

> [!NOTE]
>
> - "Invalid login credentials" → **"Email ou mot de passe incorrect."**
> - "User already registered" → **"Cet email est déjà utilisé."**

---

## Sécurité et Optimisation

- **`useRef` pour le Logout** : Le hook utilise une référence (`sessionRef`) pour la déconnexion. Cela permet de quitter proprement même si le composant n'a pas été re-rendu, tout en évitant des dépendances circulaires dans les `useCallback`.
- **Persistance** : La session est automatiquement récupérée au rafraîchissement de la page grâce à `supabase.auth.getSession()`.

---

## Résumé

Ce hook garantit que :

- L'état de connexion est **toujours synchronisé** avec le serveur.
- Le **pseudo du joueur** est disponible partout dans l'application (pour le log de combat, le lobby, etc.).
- Les **erreurs d'authentification** sont gérées proprement et affichées de manière humaine.
