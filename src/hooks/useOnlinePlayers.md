# Documentation de useOnlinePlayers.ts

## Localisation

`src/hooks/useOnlinePlayers.ts`

## Rôle du fichier

Ce hook est le **cerveau de la présence sociale** du projet. Il gère l'affichage des joueurs connectés, le système d'invitations en temps réel et le mécanisme de "Heartbeat" (battement de cœur) qui informe la base de données Supabase que le joueur est toujours actif.

Il transforme les données brutes de la base de données en une liste interactive (`PlayerRow[]`) où chaque joueur est associé à un statut contextuel (Peut être invité, Invitation envoyée, Invitation reçue).

---

## Mécanismes Clés

### 1. Le Heartbeat (Battement de cœur)

Pour éviter qu'une déconnexion brutale (fermeture d'onglet) ne laisse un joueur "fantôme" dans la liste :

- Un `setInterval` s'exécute toutes les **2 minutes**.
- Il met à jour la colonne `updated_at` dans Supabase.
- **Sécurité** : Le TTL (Time To Live) côté serveur est de 3 minutes. Si aucun heartbeat n'est reçu pendant ce délai, un processus automatique (Cron) marquera le joueur comme hors ligne.

### 2. Gestion Propre du Logout

Le hook utilise une astuce de `useEffect` pour garantir que le joueur est retiré de la liste dès qu'il clique sur "Déconnexion" :

- Il capture l'ID de l'utilisateur dans une constante locale au sein de l'effet.
- Lors du nettoyage (`cleanup`), même si la session est déjà devenue `null`, la fonction de retrait possède toujours l'ID original pour nettoyer la base de données.

---

## États des Joueurs (`PlayerRowStatus`)

Le hook calcule dynamiquement le statut de chaque ligne pour l'UI :

| Statut    | Signification                        | Action UI associée            |
| :-------- | :----------------------------------- | :---------------------------- |
| `invite`  | Le joueur est libre.                 | Bouton "Inviter"              |
| `pending` | Vous lui avez envoyé une invitation. | Bouton "Annuler" (attente)    |
| `join`    | Il vous a envoyé une invitation.     | Bouton "Accepter / Rejoindre" |

---

## Fonctions principales

### `refresh()`

Récupère simultanément la liste des joueurs en ligne et les invitations actives pour reconstruire les lignes de l'interface.

### `invite(targetUserId)` / `cancelInvite(targetUserId)`

Gère la création et la suppression des invitations dans la base de données, tout en mettant à jour l'état local pour un retour visuel instantané.

### `join(fromUserId)`

L'étape finale : supprime l'invitation (consommation) et déclenche le callback `onGameStart` pour basculer l'application vers l'écran de combat.

---

## Interaction avec les Services

Ce hook orchestre les appels vers `onlinePlayersService.ts` :

- `setPlayerOnline` / `setPlayerOffline` : Gestion de la table de présence.
- `fetchOnlinePlayers` / `fetchMyInvitations` : Récupération des données.
- `sendInvitation` / `deleteInvitation` : Logique de mise en relation.

---

## Résumé

Ce fichier est responsable de :

- **Maintenir la visibilité** du joueur pour les autres utilisateurs (Heartbeat).
- **Calculer les relations** entre joueurs (qui a invité qui).
- **Assurer un nettoyage propre** des données lors de la déconnexion.
- **Déclencher le passage en jeu** dès qu'un match est accepté.
  """
