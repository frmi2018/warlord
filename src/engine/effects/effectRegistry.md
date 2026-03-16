# Documentation de effectRegistry.ts

## Localisation

`src/engine/effects/effectRegistry.ts`

## Rôle du fichier

Le `effectRegistry` est le **dictionnaire central des capacités** du jeu. Il fait le lien entre une carte physique (identifiée par son ID) et sa logique technique (les effets qu'elle peut déclencher).

Depuis le Sprint 1.3, ce registre est généré automatiquement au démarrage de l'application en scannant la base de données des cartes (`allCards`). Cela garantit que chaque carte porte ses propres règles avec elle.

---

## Concept : ID de Base vs ID Runtime

Pour permettre d'avoir plusieurs exemplaires de la même carte en jeu (ex: deux "Soldats de l'Empire"), le moteur utilise deux types d'identifiants :

1.  **ID Runtime** (ex: `p-sots-35-ab12`) : Unique pour chaque instance sur le plateau.
    - `p-` ou `ai-` : Désigne le propriétaire (Player ou IA).
    - `-ab12` : Suffixe aléatoire pour distinguer les doublons.
2.  **ID de Base** (ex: `sots-35`) : L'identifiant du modèle de la carte dans la base de données.

Le registre travaille exclusivement avec l'**ID de Base** pour que tous les exemplaires d'une même carte partagent la même logique d'effet.

---

## Fonctions principales

### `getEffectsForCard(cardId): CardEffect[]`

La porte d'entrée pour récupérer les pouvoirs d'une unité.

- Elle nettoie l'ID reçu via `extractBaseId`.
- Elle interroge la `Map` interne pour retourner le tableau d'effets (ou un tableau vide si la carte n'a pas de pouvoirs).

### `extractBaseId(cardId): string`

Une utilitaire de traitement de chaîne de caractères utilisant des **Expressions Régulières (Regex)** :

- `replace(/^(p|ai)-/, "")` : Retire le préfixe d'appartenance.
- `replace(/-[a-z0-9]{4}$/, "")` : Retire le hash d'unicité à la fin.

---

## Structure du Registre

| Propriété  | Description                                                                  |
| :--------- | :--------------------------------------------------------------------------- |
| `registry` | Une `Map<string, CardEffect[]>` privée, alimentée une seule fois au runtime. |
| `allCards` | La source de vérité contenant les définitions statiques de chaque carte.     |

---

## Interaction avec d'autres fichiers

Ce fichier est le pilier du système d'effets :

- `resolveActiveEffects.ts` : Utilise ce registre pour filtrer les capacités jouables.
- `eventBus.ts` : Pourrait utiliser ce registre pour savoir quelles cartes doivent "écouter" un événement.
- `cards.ts` (Data) : Fournit les données brutes lors de l'initialisation.

---

## Résumé

Ce fichier est responsable de :

- **Indexer** tous les effets de jeu au lancement.
- **Traduire** les identifiants complexes de l'UI en identifiants simples pour la logique métier.
- **Centraliser** l'accès aux capacités pour éviter la duplication de code dans les composants.
  """
