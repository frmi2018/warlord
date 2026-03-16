# Documentation de buildDeckFromSaved.ts

## Localisation

`src/utils/buildDeckFromSaved.ts`

## Rôle du fichier

Ce fichier est le complément indispensable du système de sauvegarde. Alors que `createDeck` génère une armée aléatoirement, `buildDeckFromSaved` reconstruit une instance de jeu (`Card[]`) à partir d'un deck persisté par le joueur (`SavedDeck`).

Il assure deux missions critiques :

1. **Instanciation** : Transformer des IDs de base (ex: `sots-116`) en objets dynamiques avec des IDs de session uniques.
2. **Déploiement Tactique** : Respecter scrupuleusement la formation personnalisée choisie par le joueur (Warlord et rangs de départ).

---

## Logique de Reconstitution

La fonction suit un flux de données rigoureux pour garantir l'intégrité de la partie :

### 1. Indexation du Pool

Pour des raisons de performance, le moteur crée d'abord une `Map` de la base de données de cartes. Cela permet de retrouver instantanément les caractéristiques (stats, traits, images) de chaque ID listé dans le deck sauvegardé.

### 2. Génération des IDs Runtime

Pour permettre d'avoir des doublons (ex: 3 fois la même troupe), chaque carte reçoit un identifiant unique pour la partie sous la forme :  
`{préfixe}-{baseId}-{token_aléatoire}` (ex: `p-sots-2-ab12`).

### 3. Affectation à la Formation

Le script analyse l'objet `startingFormation` du deck sauvegardé :

- Il cherche dans les cartes instanciées celles qui correspondent aux IDs requis pour le **Warlord**, le **Rang 1** et le **Rang 2**.
- Une fois une carte assignée à une position $(posX, posY)$, elle est marquée comme "utilisée" pour ne pas être placée deux fois.

### 4. Finalisation du Deck

Les cartes qui ne font pas partie de la formation de départ sont envoyées dans la zone `playerDeck` :

- **Mélange** : Elles subissent un mélange de Fisher-Yates pour garantir l'aléa.
- **Ordonnancement** : Elles reçoivent un `deckOrder` pour définir l'ordre de pioche.

---

## Comparaison : Génération vs Reconstruction

| Fonctionnalité  | `createDeck()`        | `buildDeckFromSaved()`      |
| :-------------- | :-------------------- | :-------------------------- |
| **Origine**     | Algorithme aléatoire  | Choix utilisateur persistés |
| **Formation**   | Standard (Pyramide)   | Personnalisée par le joueur |
| **Utilisation** | Mode Escarmouche / IA | Mode Campagne / Multijoueur |

---

## Pourquoi cette architecture ?

- **Fonction Pure** : La fonction ne dépend pas de l'état global ou de React. Elle peut être testée unitairement avec des données fictives.
- **Robustesse** : Si une carte sauvegardée a été supprimée de la base de données entre-temps, le script émet un `console.warn` et continue la construction sans faire planter le jeu.
- **Flexibilité des IDs** : Le système de `makeRuntimeId` permet de distinguer facilement les cartes du joueur (`p-`) de celles de l'IA (`ai-`) tout au long du cycle de vie du moteur.

---

## Résumé

Ce fichier permet au joueur de **retrouver son armée** exactement comme il l'a configurée. Il assure le passage d'un stockage statique (JSON/Base de données) à un état de jeu vivant, prêt pour la première phase du tour.
