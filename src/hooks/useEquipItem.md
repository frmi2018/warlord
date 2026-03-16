# Documentation de useEquipItem.ts

## Localisation

`src/hooks/useEquipItem.ts`

## Rôle du fichier

Le hook `useEquipItem` gère la logique métier et l'interface utilisateur pour **équiper un objet** (Item) sur un personnage depuis la main du joueur. Dans l'univers de Warlord, l'équipement est une action de phase de décret qui renforce une unité mais peut être soumise à des conditions strictes (classe, traits, emplacements disponibles).

Ce hook fait le lien entre la carte "Item" sélectionnée en main et les cibles valides sur le plateau, tout en gérant la synchronisation asynchrone du moteur.

---

## Flux UX : Équiper un objet

Le processus suit une séquence sécurisée pour éviter les erreurs de jeu :

1.  **Sélection** (`startEquip`) : Le joueur clique sur un item. Le hook passe en état "équipage" et informe le système qu'une action commence.
2.  **Filtrage** (`eligibleCharacters`) : Le hook scanne automatiquement `playerRanks` pour ne proposer que les personnages pouvant légalement porter cet item (via `canEquipItem`).
3.  **Validation** (`confirmEquip`) : Le joueur choisit sa cible. Le hook déclenche le son associé et envoie l'action au moteur.
4.  **Nettoyage** : L'état local est réinitialisé et le tour progresse.

---

## Gestion des Fenêtres de Réaction (Fix R2)

L'équipement d'un objet peut déclencher des capacités de réaction (ex: un bonus qui s'active à l'équipement, ou un adversaire qui détruit l'objet à la pose).

- **Le Problème** : Si l'on signale la fin de l'action immédiatement après le clic, l'IA pourrait agir alors qu'une fenêtre de réaction est encore ouverte pour le joueur.
- **La Solution** : Utilisation de `pendingRef` et `useReactSettled`.
  - Si `dispatch` retourne `openedReactWindow: true`, on "verrouille" la fin de l'action.
  - `notifyActionDone()` n'est appelé que lorsque la fenêtre de réaction se ferme, garantissant un flux de jeu séquentiel parfait.

---

## Fonctions et Propriétés principales

### `eligibleCharacters` (Calculé)

Une liste dynamique filtrée à chaque rendu. Elle s'assure que :

- La cible est un personnage.
- La cible est sur le plateau (`playerRanks`).
- Les règles métier de `canEquipItem` sont satisfaites (ex: pas deux épées si le perso n'a qu'une main, restriction de classe Mage/Guerrier, etc.).

### `startEquip(item)`

Démarre la procédure. C'est elle qui permet à la modal d'équipement de savoir quel item est en cours de traitement.

### `confirmEquip(characterId)`

C'est le point de non-retour. Elle consomme l'action et gère la logique de "wait-and-see" pour les fenêtres de réaction.

---

## Interaction avec le Système

- **`canEquipItem.ts`** : Le moteur de règles qui valide si un personnage X peut porter l'item Y.
- **`useReactSettled.ts`** : Le gardien du timing qui empêche le jeu de s'emballer si une réaction survient.
- **`soundManager.ts`** : Fournit le feedback auditif "Move/Equip" pour confirmer l'action au joueur.

---

## Résumé

Ce fichier est responsable de :

- **La mise en relation** des items en main avec les personnages sur le plateau.
- **Le respect des règles** d'équipement via un filtrage proactif.
- **La synchronisation temporelle** avec le système de phases et de réactions.
- **L'expérience utilisateur** (feedback sonore et gestion des états de modal).
  """
