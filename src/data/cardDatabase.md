# Documentation de cardDatabase.ts

## Localisation

`src/data/cardDatabase.ts`

## Rôle du fichier

Ce fichier agit comme le **point d'entrée unique (Facade)** de toutes les cartes du jeu. Bien qu'il ait été le centre de saisie au début du projet, il sert désormais uniquement de **relais de compatibilité** (Bridge) vers la nouvelle architecture modulaire introduite au Sprint 1.3.

Il permet au reste de l'application (moteur de combat, deck builder, UI) d'importer la liste globale des cartes sans avoir à connaître la structure interne complexe des dossiers par faction.

---

## Pourquoi cette transition ?

Avant le Sprint 1.3, toutes les cartes étaient dans un seul fichier géant. Cette nouvelle structure apporte :

- **Maintenabilité** : Fini les conflits Git incessants sur un seul fichier. Chaque carte possède son propre espace.
- **Clarté** : Les développeurs peuvent travailler sur une faction spécifique (ex: les Nains) sans toucher au reste.
- **Robustesse** : Le typage strict `DeckCard` est appliqué à chaque fichier individuel, facilitant la détection d'erreurs à la source.

---

## Guide de mise à jour (Workflow)

L'ajout d'une carte ne se fait plus ici, mais suit ce flux de travail précis :

1. **Création** : Créer un fichier TS dédié dans `src/data/cards/<faction>/`.
2. **Indexation de Faction** : Ajouter l'export de cette carte dans le fichier `index.ts` de son dossier faction.
3. **Agrégation Globale** : Le fichier `src/data/cards/index.ts` regroupe toutes les factions pour former la `cardDatabase` finale.

---

## Règle de Compatibilité

Le maintien de ce fichier est essentiel pour ne pas "briser" le code existant :

- **Exports préservés** : `export { cardDatabase }` garantit que tous les composants UI qui utilisaient déjà cet import continuent de fonctionner.
- **Transparence** : Pour le moteur de jeu, rien n'a changé. Pour le développeur, tout est mieux organisé.

---

## Résumé

`cardDatabase.ts` est passé d'un rôle de "conteneur" à un rôle de **"standardiste"**. Il redirige les demandes vers le nouveau système de fichiers modulaire, assurant une transition fluide vers un projet capable d'accueillir des centaines de cartes sans devenir illisible.
