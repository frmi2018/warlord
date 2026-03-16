# Documentation de aiDeck.ts

## Localisation

`src/types/aiDeck.ts`

## Rôle du fichier

Ce fichier est le **centre de configuration des sélections de decks**. Il définit comment les factions et les options de génération apparaissent dans l'interface utilisateur (Home Page), aussi bien pour le joueur que pour l'adversaire (IA).

Il assure une cohérence totale entre les deux carrousels en utilisant une source de données unique pour les factions, tout en distinguant leurs identifiants techniques.

---

## Architecture des Identifiants (IDs)

Le fichier définit des IDs réservés qui servent de "clés magiques" pour déclencher des comportements spécifiques dans l'application :

### 1. Modes Spéciaux

- **`AI_RANDOM_DECK_ID` / `PLAYER_RANDOM_DECK_ID`** : Déclenche une génération 100% aléatoire (Faction + Warlord au hasard).
- **`AI_SAVED_DECK_ID`** : Indique à l'interface d'ouvrir la modale de sélection des decks sauvegardés pour l'IA.

### 2. Slots de Faction

Chaque faction possède un ID préfixé (`ai-faction-` ou `player-faction-`).

- Exemple pour les Nains côté IA : `ai-faction-dwarf`.
- Cela permet d'identifier immédiatement si le choix de l'utilisateur concerne une faction spécifique ou un mode spécial.

---

## Configuration des Factions (`FactionDeckConfig`)

La structure `FACTION_BASES` est la source de vérité. Chaque entrée contient :

- **`faction`** : Le nom technique (utilisé par le moteur de règles).
- **`label`** : Le nom affiché à l'écran.
- **`coverImage`** : Le chemin vers le blason/armoirie de la faction.
- **`enabled`** : Un booléen permettant d'activer ou désactiver une faction dans le carrousel sans supprimer son code.

### État de déploiement (Sprint actuel)

Actuellement, seules les factions **Deverenian** et **Dwarf** sont activées (`enabled: true`). Les autres apparaissent dans la configuration mais sont filtrées dans l'interface de sélection.

---

## Génération Dynamique des Listes

Le fichier exporte quatre listes clés générées par transformation (map/filter) :

| Liste                         | Description                                                                   |
| :---------------------------- | :---------------------------------------------------------------------------- |
| **`AI_FACTION_DECKS`**        | Tous les slots disponibles pour le carrousel de l'IA.                         |
| **`PLAYER_FACTION_DECKS`**    | Tous les slots disponibles pour le carrousel du Joueur.                       |
| **`ENABLED_AI_FACTIONS`**     | Uniquement les factions jouables par l'IA (utilisé pour le tirage aléatoire). |
| **`ENABLED_PLAYER_FACTIONS`** | Uniquement les factions jouables par le Joueur.                               |

---

## Pourquoi cette architecture ?

- **DRY (Don't Repeat Yourself)** : Modifier une image ou un label dans `FACTION_BASES` met à jour instantanément les deux carrousels.
- **Normalisation des IDs** : L'utilisation de `.toLowerCase().replace(/ /g, "-")` garantit que les IDs sont propres et utilisables dans des URLs ou des sélecteurs CSS.
- **Découplage UI/Moteur** : L'interface utilise les `label` et `coverImage`, tandis que le moteur de génération utilise le champ `faction`, assurant que les changements visuels n'impactent pas la logique de jeu.

---

## Résumé

Ce fichier orchestre la **phase de préparation de la partie**. Il définit les options disponibles dans le lobby et fournit au moteur de création de deck les paramètres nécessaires (faction choisie ou mode aléatoire) pour lancer le combat.
