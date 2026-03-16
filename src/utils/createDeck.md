# Documentation de createDeck.ts

## Localisation

`src/utils/createDeck.ts`

## Rôle du fichier

Ce fichier est le **moteur de construction automatique** de decks. Il permet de générer instantanément une armée complète et jouable à partir d'une base de données de cartes, tout en respectant scrupuleusement les contraintes de formation et de faction de la 4ème édition de _Warlord_.

Il est capable de fonctionner de trois manières :

1. **Déterministe** : On impose un Warlord spécifique via son ID.
2. **Thématique** : On impose une faction (ex: "Deverenian") et le moteur choisit un leader aléatoire.
3. **Aléatoire (Legacy)** : Le moteur pioche un leader et une armée au hasard dans tout le pool.

---

## Logique de Construction

La construction suit une hiérarchie stricte pour garantir la viabilité du deck :

### 1. Sélection du Warlord

Le Warlord est la pièce centrale. Sa faction détermine la majorité des cartes qui seront ajoutées par la suite (cohérence thématique).

### 2. Composition de la Formation

Le moteur utilise `pickExactly` pour remplir les rangs de départ requis :

- **Rang 1** : 3 personnages de Niveau 1 de la même faction.
- **Rang 2** : 2 personnages de Niveau 2 de la même faction.

### 3. Complément et Diversité

- **Troupes de faction** : 6 personnages supplémentaires pour renforcer le thème.
- **Mercenaires** : 2 personnages neutres pour apporter de la variété.
- **Équipement & Actions** : 3 Objets (Items) et 3 Actions pour permettre des interactions tactiques.

---

## Gestion des Limites (`expandPoolResidual`)

Pour éviter de générer des decks illégaux, l'utilitaire intègre une logique de "pool résiduel" :

- **Vérification des doublons** : Avant chaque tirage, il calcule combien de copies d'une carte sont déjà présentes.
- **Respect des plafonds** : Si une carte est `unique` ou si elle a déjà atteint la limite de `MAX_COPIES_PER_CARD`, elle est temporairement retirée du pool de tirage.

---

## Instanciation des Cartes

À l'étape finale, chaque `DeckCard` (donnée statique) est transformée en `Card` (objet dynamique de jeu) :

- **ID Unique** : Un préfixe (`p-` ou `ai-`) et un suffixe aléatoire sont ajoutés pour différencier deux copies de la même carte sur le plateau.
- **Initialisation** : Les blessures sont mises à 0 et l'orientation est définie sur `ready`.

---

## Pourquoi cette architecture ?

| Avantage                  | Description                                                                                                 |
| :------------------------ | :---------------------------------------------------------------------------------------------------------- |
| **Équilibre Automatique** | Garantit que l'IA et le joueur commencent toujours avec un nombre identique de cartes et un ratio cohérent. |
| **Extensibilité**         | Facile d'ajouter de nouvelles étapes (ex: "Sorts") sans casser la logique de faction existante.             |
| **Robustesse**            | Lève des erreurs explicites si le pool de cartes est insuffisant pour créer un deck légal.                  |

---

## Résumé

Ce fichier transforme une simple liste de données en une **armée structurée**. Il garantit la légalité du deck, le respect des factions et prépare chaque carte pour les interactions en temps réel dans le moteur de combat.
