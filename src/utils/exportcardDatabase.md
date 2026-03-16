# Documentation de exportcardDatabase.ts

## Localisation

`src/utils/exportcardDatabase.ts`

## Rôle du fichier

Ce fichier est un utilitaire de méta-programmation. Son but est de transformer un tableau d'objets JavaScript (les cartes générées ou modifiées dans l'application) en un **code source TypeScript valide** et formaté.

Il permet de "boucler la boucle" du développement : après avoir utilisé le générateur de cartes pour créer de nouveaux personnages, cet utilitaire génère le fichier `cardDatabase.ts` que le développeur peut réintégrer directement dans le code source du projet.

---

## Fonctionnalités Principales

### 1. Sérialisation Intelligente (`serializeValue`)

Contrairement à un simple `JSON.stringify`, cette fonction produit un code lisible par l'humain ("pretty-print") :

- **Types de base** : Gère les chaînes, nombres et booléens.
- **Tableaux complexes** : Formate les listes d'actions sur plusieurs lignes pour faciliter la relecture du code généré.
- **Échappement** : Gère les guillemets dans les noms de cartes ou les textes d'ambiance.

### 2. Contrôle de l'Ordre des Clés (`KEY_ORDER`)

Pour maintenir une base de données propre, l'utilitaire impose un ordre logique aux propriétés de chaque carte (ID, puis Nom, puis Niveau, etc.). Cela garantit que toutes les cartes du fichier final ont la même structure visuelle, rendant la recherche manuelle beaucoup plus aisée.

### 3. Génération de Fichier (`generatecardDatabaseFile`)

Construit l'intégralité du fichier `.ts`, incluant :

- Les commentaires d'avertissement (ne pas éditer manuellement).
- Les imports de types nécessaires (`BaseCard`).
- L'export de la constante `cardDatabase`.

---

## Méthodes d'Exportation

### `downloadcardDatabase(cards)`

Cette fonction est destinée à l'interface utilisateur (UI).

1. Elle génère le contenu texte du fichier.
2. Elle crée un `Blob` (objet binaire) en mémoire.
3. Elle simule un clic sur un lien invisible pour déclencher le **téléchargement automatique** dans le navigateur du développeur.

---

## Pourquoi cet utilitaire est-il crucial ?

| Avantage                  | Description                                                                                                        |
| :------------------------ | :----------------------------------------------------------------------------------------------------------------- |
| **Zéro Erreur de Frappe** | Évite les erreurs de syntaxe (virgules manquantes, accolades mal fermées) lors de l'ajout massif de cartes.        |
| **Workflow Accéléré**     | Permet de passer de la conception d'une carte (via l'UI) à son intégration en jeu en quelques secondes.            |
| **Standardisation**       | Garantit que le schéma des données reste identique pour toutes les cartes, facilitant la maintenance à long terme. |

---

## Exemple de sortie générée

```typescript
export const cardDatabase: DeckCard[] = [
  {
    id: "sots-1",
    name: "Sir Kofir",
    level: 1,
    cardType: "character",
    // ... reste des propriétés
  },
];
```
