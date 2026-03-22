# Documentation de cardDefinition.ts

## Localisation

`src/types/cardDefinition.ts`

## Rôle du fichier

Le `CardDefinition` est la **source de vérité absolue** pour chaque carte individuelle du jeu. C'est l'objet complet tel qu'il est défini dans les fichiers de données (fichiers JSON ou TypeScript par faction).

---

## RÈGLE D'OR : Source de Vérité

Ce fichier régit la manière dont les cartes sont créées :

1. Une carte = Un fichier dans `src/data/cards/`.
2. La `CardDefinition` fusionne les données statiques (`DeckCard`) et les effets complexes (`CardEffect[]`).

---

## Composants de la Définition

- **Effets (`effects`)** : Un tableau optionnel d'effets mécaniques. Si absent, la carte est considérée comme une "vanilla" (uniquement des statistiques).
- **Exécution d'Item (`execute`)** : Permet de définir le comportement actif des objets directement dans leur définition statique.

---

## Résumé

Le `cardDefinition` est responsable de :

- **Structurer la création** de nouvelles cartes.
- **Lier les données statiques aux scripts d'effets** dynamiques.
- **Servir de base** à la génération automatique de la base de données globale des cartes.
