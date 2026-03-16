# Documentation de useSavedDecks.ts

## Localisation

`src/hooks/useSavedDecks.ts`

## Rôle du fichier

Ce hook React gère la **persistance des decks** créés par le joueur. Il orchestre la synchronisation entre la base de données distante (**Supabase**) et le stockage local du navigateur (**localStorage**).

Sa mission est de garantir que le joueur retrouve ses armées à chaque session, tout en permettant une consultation rapide (même hors-ligne) grâce au cache local.

---

## Stratégie de Stockage Hybride

Le hook utilise un système à deux niveaux pour optimiser l'expérience utilisateur :

1.  **localStorage (`warlord_saved_decks`)** : Sert de cache immédiat. Au chargement de l'application, les decks sont lus instantanément depuis le navigateur pour éviter tout temps d'attente (chargement à 0ms).
2.  **Supabase** : Sert de source de vérité. Les créations, suppressions et synchronisations sont envoyées au serveur dès que le joueur est connecté.

---

## Fonctionnalités Clés

### 1. Synchronisation Automatique

Dès qu'une session utilisateur est détectée (`session.user.id`), le hook déclenche automatiquement `fetchUserDecks`. Il met à jour l'état local ET le cache localStorage avec les données les plus fraîches du serveur.

### 2. Gestion des Actions (CRUD)

- **`createDeck`** : Envoie le nouveau deck à Supabase, récupère l'objet créé (avec son ID final) et l'ajoute en tête de liste localement.
- **`removeDeck`** : Supprime le deck côté serveur, puis le retire de la liste locale et du cache.
- **`syncFromRemote`** : Permet de forcer manuellement une mise à jour des données (utile après une déconnexion/reconnexion).

### 3. Protection et Sécurité

Le hook vérifie systématiquement la présence d'une session avant d'autoriser une modification. Si l'utilisateur n'est pas connecté, une erreur explicite est renvoyée ("Tu dois être connecté pour sauvegarder un deck").

---

## Interface du Hook (`UseSavedDecksReturn`)

| Élément            | Type          | Description                                                       |
| :----------------- | :------------ | :---------------------------------------------------------------- |
| `decks`            | `SavedDeck[]` | La liste actuelle des decks du joueur (triée par le plus récent). |
| `isLoading`        | `boolean`     | Indique si une opération réseau est en cours.                     |
| `createDeck()`     | `function`    | Sauvegarde une nouvelle armée.                                    |
| `removeDeck()`     | `function`    | Supprime une armée définitivement.                                |
| `syncFromRemote()` | `function`    | Écrase le cache local par les données du serveur.                 |

---

## Pourquoi cette architecture ?

- **Résilience** : Si Supabase est temporairement indisponible, le joueur peut toujours voir ses decks précédemment chargés grâce au `localStorage`.
- **Performance** : L'interface utilisateur ne "saute" pas lors du chargement, car les données locales sont affichées immédiatement en attendant la réponse du serveur.
- **Expérience Multijoueur** : Garantit que les decks utilisés dans le lobby ou en partie sont bien ceux sauvegardés sur le profil du joueur.

---

## Résumé

Ce hook garantit que :

- Les **decks créés** sont sauvegardés de manière pérenne.
- Le **chargement est instantané** grâce au cache local.
- Les **erreurs réseau** sont interceptées et affichées proprement au joueur.
