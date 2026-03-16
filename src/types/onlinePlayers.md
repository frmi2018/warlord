# Documentation de onlinePlayers.ts

## Localisation

`src/types/onlinePlayers.ts`

## Rôle du fichier

Ce fichier définit les **contrats de données pour les fonctionnalités multijoueurs**. Il structure la manière dont les joueurs sont perçus par le système (Présence) et comment ils interagissent entre eux (Invitations).

En restant "pur" (sans dépendance aux SDK comme Supabase), ce fichier permet de partager la logique de type entre le frontend, les services de gestion d'état et potentiellement un backend.

---

## Architecture des Données

### 1. Profil et Présence

- **`UserProfile`** : L'identité de base (ID et pseudonyme).
- **`OnlinePlayer`** : Étend le profil avec un état de disponibilité.
  - `status`: Permet de distinguer un joueur libre d'un joueur déjà engagé dans une bataille (`in_game`).
  - `updatedAt`: Crucial pour le système de "heartbeat" (permet de considérer un joueur comme déconnecté s'il n'a pas donné de nouvelles depuis $X$ secondes).

### 2. Système d'Invitations (`GameInvitation`)

Structure le cycle de vie d'un défi entre deux joueurs :

- **`id`** : Identifiant unique du duel proposé.
- **`status`** :
  - `pending` : Le défi est lancé, la notification apparaît chez l'adversaire.
  - `accepted` : Déclenche l'initialisation de la salle de jeu.
  - `declined` : Supprime l'invitation ou informe l'expéditeur.

### 3. État de l'Interface (`PlayerRowStatus`)

C'est un type de "vue" calculé. Il détermine quel bouton afficher dans la liste des joueurs en ligne :

- **`invite`** : Affiche "Défier".
- **`join`** : Affiche "Accepter l'invitation" (si l'autre a dégainé en premier).
- **`pending`** : Affiche "En attente..." (bouton désactivé).

---

## Flux Logique d'une Connexion

1. **Initialisation** : Le joueur se connecte, une entrée `OnlinePlayer` est créée avec le statut `online`.
2. **Découverte** : Le client récupère la liste des `OnlinePlayer`. Pour chaque ligne, il calcule le `PlayerRowStatus` en croisant les données avec la table des `GameInvitation`.
3. **Action** : Un clic sur "Défier" crée une `GameInvitation` avec le statut `pending`.
4. **Transition** : Une fois le défi accepté, le statut du joueur passe de `online` à `in_game`.

---

## Pourquoi cette architecture ?

| Concept                        | Bénéfice                                                                                                                                          |
| :----------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Statuts explicites**         | Évite les erreurs d'interface où un joueur pourrait être invité alors qu'il est déjà en pleine partie.                                            |
| **Indépendance technologique** | Que vous utilisiez des WebSockets, Supabase Realtime ou du Polling HTTP, ces structures restent valides.                                          |
| **Séparation Vue/Donnée**      | Le type `PlayerRowStatus` permet de garder la logique de l'UI propre et réactive sans polluer les données brutes provenant de la base de données. |

---

## Résumé

Ce fichier constitue la **colonne vertébrale sociale** du jeu. Il transforme une simple liste d'utilisateurs en un lobby dynamique, gérant la visibilité des joueurs et la synchronisation des défis avant le lancement d'une session de jeu.
