# Documentation de GameBoard.tsx

## Localisation

`src/components/GameBoard.tsx`

## Rôle du fichier

Le `GameBoard` est le **chef d'orchestre visuel** et le composant racine de l'expérience de jeu. Son rôle est de centraliser l'état global (`GameState`), de connecter les multiples hooks de logique métier (combat, phases, déplacements) et de distribuer les données aux composants d'interface (UI).

C'est ici que la donnée brute du moteur de jeu est transformée en une interface interactive pour l'utilisateur.

---

## Architecture : L'État Unifié

Le fichier centralise la gestion de la donnée pour assurer la cohérence du plateau :

- **`gameState`** : La source de vérité unique remplaçant les anciens états séparés. Elle contient les cartes, les zones de jeu et les métadonnées système.
- **`dispatch`** : Récupéré via `useActionDispatcher`, il est le tunnel unique pour envoyer des intentions de jeu (actions) au moteur.
- **Hooks Métier** : Le composant instancie les "cerveaux" spécialisés (`useCombat`, `usePhaseManager`, `useManeuver`, etc.) en leur injectant le state et le dispatch.

---

## Organisation du Composant

Le code est structuré pour séparer la logique de l'affichage :

1.  **Filtrage des Zones** : Utilise `useGameZones` pour répartir les cartes du `gameState` (mains, decks, défausses, rangs) avant de les envoyer aux composants d'affichage.
2.  **Routage des Clics** : La fonction `handleCardClick` délègue à `routeCardClick` pour décider si un clic doit ouvrir un menu, sélectionner une cible ou être ignoré selon le contexte.
3.  **Gestion des Modales** : Centralise les fenêtres surgissantes (choix d'attaque, déploiement, fin de partie, réactions) via le composant `GameModals`.
4.  **Rendu (Layout)** : Organise l'écran en 3 colonnes (Indicateurs / Plateau / Instructions & Preview) pour une lecture claire du jeu.

---

## Concepts Clés pour le Développement

### Séparation UI / Données

Aucun composant enfant (comme `RanksDisplay` ou `HandDisplay`) ne reçoit l'objet `GameState` complet. Ils ne reçoivent que des listes de cartes (`Card[]`) filtrées, ce qui les rend "bêtes" et faciles à maintenir.

### Calculs Dérivés (`useMemo`)

Le fichier calcule en temps réel les modificateurs de statistiques (effets passifs) via `resolvePassiveEffects` uniquement pour la carte survolée, optimisant ainsi les performances.

---

## Interaction avec les autres fichiers

- **`useActionDispatcher.ts`** : Fournit la méthode `dispatch` pour modifier l'état de manière sécurisée et synchronisée.
- **`createInitialGameState.ts`** : Génère l'état de départ au montage du composant.
- **`cardClickRouter.ts`** : Détermine l'intention derrière chaque interaction utilisateur.
- **`GameModals.tsx`** : Reçoit les états des hooks pour afficher les interfaces de décision.

---

## Résumé

Le `GameBoard` est responsable de :

- **Détenir l'état de référence** (`gameState`).
- **Connecter les hooks métier** aux données.
- **Distribuer les actions** via le dispatcher.
- **Gérer le layout global** et la navigation entre les phases de jeu.
