# Documentation de onlinePlayersService.ts

## Localisation

`src/services/onlinePlayersService.ts`

## Rôle du fichier

Ce service gère la dimension "sociale" et temps réel de l'application. Il communique avec Supabase pour gérer trois piliers essentiels :

1.  **Les Profils** : L'identité publique des joueurs (pseudos).
2.  **La Présence** : Savoir qui est actuellement en ligne pour jouer.
3.  **Le Matchmaking** : Envoyer, recevoir et supprimer des invitations de duel.

Il fonctionne de concert avec un système de "Heartbeat" (battement de coeur) pour maintenir une liste de joueurs actifs précise.

---

## Fonctionnalités Principales

### 1. Gestion des Profils (`Profiles`)

Contrairement au compte email privé, le profil est public.

- **`createProfile`** : Initialise le pseudo d'un utilisateur. La table SQL possède une contrainte `UNIQUE` sur le pseudo pour garantir l'identité.
- **`isUsernameTaken`** : Permet de vérifier la disponibilité d'un pseudo en temps réel lors de l'inscription.

### 2. Système de Présence (`Online Players`)

Le service gère une table volatile qui liste les joueurs connectés.

- **`setPlayerOnline`** : Utilise un `upsert`. Si le joueur est déjà listé, sa date `updated_at` est rafraîchie.
- **Heartbeat & TTL** : Pour éviter que des joueurs "fantômes" (déconnectés brutalement) ne restent dans la liste, une Cron Job côté serveur supprime les lignes dont le `updated_at` est trop vieux.
- **`setPlayerOffline`** : Nettoyage immédiat lors d'une déconnexion volontaire.

### 3. Invitations de Duel (`Game Invitations`)

Gère l'interaction entre deux joueurs dans le lobby.

- **`sendInvitation`** : Crée une demande de duel. Le service nettoie intelligemment les anciennes demandes pour éviter les conflits de clé unique.
- **`fetchMyInvitations`** : Récupère les défis lancés au joueur ou ceux qu'il a lui-même envoyés.
- **`deleteInvitation`** : Utilisé pour annuler, refuser ou conclure le processus d'invitation une fois le match lancé.

---

## Détail technique des requêtes

| Fonction             | Méthode Supabase | Description                                                          |
| :------------------- | :--------------- | :------------------------------------------------------------------- |
| `fetchProfile`       | `.maybeSingle()` | Récupère 0 ou 1 ligne sans générer d'erreur si vide.                 |
| `setPlayerOnline`    | `.upsert()`      | "Update if exists, else Insert".                                     |
| `fetchMyInvitations` | `.or(...)`       | Requête complexe cherchant l'ID soit en émetteur, soit en récepteur. |

---

## Architecture et Sécurité

- **Découplage** : Ce service ne contient aucun état React. Il est utilisé par le hook `useOnlinePlayers` qui, lui, gère le rafraîchissement de l'interface.
- **Intégrité** : Le service utilise systématiquement `.trim()` sur les pseudos pour éviter les doublons invisibles créés par des espaces.
- **Performance** : Les listes sont triées par `updated_at` pour afficher en priorité les joueurs les plus actifs.

---

## Résumé

Ce fichier garantit que :

- Chaque joueur possède un **pseudo unique**.
- Le lobby affiche une liste **fiable et dynamique** des adversaires potentiels.
- Le système de défi est **fluide** et auto-nettoyant.
