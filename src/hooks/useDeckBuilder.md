# Documentation de useDeckBuilder.ts

## Localisation

`src/hooks/useDeckBuilder.ts`

## Rôle du fichier

Ce hook React contient toute la **logique métier du constructeur de deck**. Il gère l'état complexe de la création d'une armée, en respectant scrupuleusement les règles de formation de la 4ème édition de Warlord.

Il est conçu pour être totalement indépendant de l'interface utilisateur : il fournit des données et des fonctions, mais ne sait pas comment elles sont affichées (grille, liste, drag & drop, etc.).

---

## Les Étapes de Construction (`BuilderStep`)

Le processus est découpé en 4 étapes séquentielles pour guider l'utilisateur :

1.  **`warlord`** : Choix du commandant (définit la faction de la formation).
2.  **`formation`** : Sélection des troupes de départ (Rangs 1 et 2).
3.  **`cards`** : Ajout des cartes restantes pour atteindre le total requis.
4.  **`name`** : Finalisation et nommage du deck.

---

## Règles de Formation implémentées

Le hook valide automatiquement les contraintes suivantes :

### La Formation Initiale

- **Warlord** : Obligatoire, positionné au Rang 3.
- **Rang 1** : Exactement **3 personnages de niveau 1** de la même faction que le Warlord.
- **Rang 2** : Exactement **2 personnages de niveau 2** de la même faction que le Warlord.

### Structure du Deck

- **Taille totale** : Exactement **20 cartes** (incluant la formation et le Warlord).
- **Limites de copies** :
  - Cartes Uniques / Warlords : **1 seule copie**.
  - Autres cartes : **4 copies maximum** (via `getMaxCopies`).
- **Liberté Tactique** : Les cartes ajoutées après la formation (Extra Cards) n'ont pas de restriction de faction.

---

## Fonctions et État exposés

### État et Dérivés

- **`allSelectedCards`** : Le tableau complet des 20 objets cartes.
- **`cardCountMap`** : Un dictionnaire (`Map`) qui suit le nombre d'exemplaires de chaque carte pour bloquer l'ajout si la limite est atteinte.
- **`validation`** : Un objet contenant un booléen `isValid` et la liste des erreurs textuelles (ex: "Deck incomplet").

### Actions Clés

- **`addFormationCard` / `removeFormationCard`** : Gère l'ajout/retrait spécifique aux deux premiers rangs.
- **`addExtraCard` / `removeExtraCard`** : Gère le remplissage du reste du deck.
- **`getStartingFormation`** : Extrait uniquement les IDs nécessaires pour la sauvegarde en base de données.

---

## Sécurité et Intégrité

- **Réinitialisation Intelligente** : Si le joueur change de Warlord en cours de route, le hook réinitialise automatiquement la formation et les cartes extra, car la faction autorisée pour les premiers rangs a probablement changé.
- **Validation en temps réel** : La propriété `canGoNext` permet de griser ou d'activer les boutons de navigation de l'interface utilisateur instantanément selon le respect des règles.

---

## Résumé

Ce hook garantit que :

- Aucun deck **illégal** ne peut être sauvegardé ou utilisé.
- L'utilisateur est **guidé pas à pas** dans la création de son armée.
- Les **limites de copies** et les **niveaux de personnages** sont vérifiés à chaque clic.
