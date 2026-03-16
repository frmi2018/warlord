# Documentation de HomePage.tsx

## Localisation

`src/pages/HomePage.tsx`

## Rôle du fichier

`HomePage` est le **hub central** de l'application. C'est ici que le joueur configure sa partie avant d'entrer sur le champ de bataille. Ce composant orchestre l'authentification, la sélection des decks (joueur et IA), la gestion de la communauté en ligne et l'accès au Deck Builder.

Il transforme des sélections d'UI (IDs de carrousels) en objets `DeckInfo` complets que le moteur de jeu peut interpréter.

---

## Logique de Résolution des Decks

La page utilise des fonctions de "résolution" pour déterminer la composition exacte de l'armée au moment du clic sur **Commencer la partie** :

### 1. Types de Sélections possibles

- **Aléatoire Total** : Le moteur choisira une faction et un Warlord au hasard parmi ceux disponibles.
- **Faction Fixe** : Le joueur choisit une faction (ex: _Deveros_), mais les cartes et le Warlord seront générés aléatoirement dans cette faction.
- **Deck Sauvegardé** : Utilise une armée précise construite et nommée par le joueur dans le Deck Builder.

### 2. Fonctions `resolve...ById`

Ces fonctions transforment un ID technique (ex: `ai-random` ou un UUID de base de données) en un objet `DeckInfo` prêt à l'emploi, incluant les images de couverture et les données de formation.

---

## Fonctionnalités Principales

| Module                   | Description                                                                                         |
| :----------------------- | :-------------------------------------------------------------------------------------------------- |
| **Gestion Auth**         | Interface avec `useAuth` pour afficher le pseudo du joueur ou proposer la connexion.                |
| **Carrousels de Decks**  | Permettent de faire défiler les options d'armées pour le joueur et pour l'IA.                       |
| **Modes d'Opposition**   | Switch entre le mode **IA** (Solo) et le mode **Joueur 2** (Multi-joueurs via `OnlinePlayersList`). |
| **Modale de Matchup**    | Une étape de confirmation finale affichant les deux armées face à face avant le lancement.          |
| **Générateur de Cartes** | Accès à l'outil de création de cartes par IA (expérimental).                                        |

---

## Architecture de l'État

Le composant gère un état local riche pour piloter les nombreuses modales :

- `showDeckBuilder` : Ouvre l'outil de création de deck.
- `showMatchup` : Affiche l'écran de confrontation finale.
- `aiSavedDeckId` : Permet de faire jouer l'IA avec un deck spécifique que le joueur a lui-même créé.

---

## Pourquoi cette architecture ?

1.  **Expérience Utilisateur (UX)** : L'utilisation de `useMemo` pour les aperçus (`DeckPreview`) permet de voir instantanément le blason et le nom du deck sélectionné sans rechargement.
2.  **Modularité** : La logique complexe est déléguée à des hooks spécialisés (`useAuth`, `useSavedDecks`, `useOnlinePlayers`), gardant le fichier de page focalisé sur la mise en page.
3.  **Flexibilité** : Le système de "Prefixes" (`AI_FACTION_DECK_PREFIX`) permet d'ajouter de nouvelles factions ou de nouveaux types de decks sans modifier la structure de la page.

---

## Résumé

Ce fichier garantit que :

- Le joueur a un **contrôle total** sur les paramètres de sa partie.
- Les données envoyées au `GameBoard` sont **validées et formatées**.
- L'accès aux **fonctions sociales** (joueurs en ligne) et de **personnalisation** (decks) est fluide.
