# Documentation de setupStartingCards.ts

## Localisation

`src/utils/setupStartingCards.ts`

## Rôle du fichier

Ce fichier est le **cerveau de l'initialisation** d'une partie. Il automatise la mise en place de la "Formation de départ" (Starting Formation) pour un joueur ou pour l'IA.

Dans _Warlord_, une partie ne commence pas avec un plateau vide : une armée est déjà déployée en forme de pyramide inversée selon des règles de deck-building strictes. Cet utilitaire extrait ces cartes spécifiques du deck, les positionne sur le plateau, et prépare le reste du deck pour la pioche.

---

## La Formation de Départ (4e Édition)

Le script applique la structure légale standard pour un début de partie :

### 1. Structure des Rangs

- **Rang 3** : Le **Warlord** (Commandant). Il est seul au sommet de sa formation.
- **Rang 2** : Deux personnages de **Niveau 2**.
- **Rang 1** : Trois personnages de **Niveau 1** (Ligne de front).

### 2. Validation de l'Armée

Avant de commencer, la fonction effectue des tests de sécurité ("Fail-Fast") :

- Elle vérifie la présence obligatoire d'un **Warlord**.
- Elle s'assure que le deck contient assez de personnages de Niveau 1 et 2 pour remplir la formation. Si les conditions ne sont pas remplies, une erreur explicite est levée pour interrompre le processus avant que le moteur ne plante.

---

## Logique de Traitement

### Étape A : Positionnement Spatial

Le script utilise une `Map` pour assigner des coordonnées précises (`posX`, `posY`) à chaque carte de départ. Les unités sont marquées comme `ready` (prêtes au combat) et leurs compteurs d'ordre (main, défausse) sont réinitialisés.

### Étape B : Préparation du Deck

Une fois la formation déployée, le reste des cartes subit un traitement de "mise en pile" :

1.  **Changement de zone** : Les cartes restantes passent en `playerDeck` ou `aiDeck`.
2.  **Mélange (Fisher-Yates)** : Un algorithme de mélange robuste garantit que l'ordre du deck est totalement aléatoire.
3.  **Indexation** : Chaque carte reçoit un `deckOrder` (de 0 à N) définissant précisément l'ordre futur de la pioche.

---

## Pourquoi cette architecture ?

| Avantage        | Description                                                                                                               |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------ |
| **Immuabilité** | La fonction retourne un nouveau tableau, évitant les effets de bord sur l'état global pendant la phase critique du setup. |
| **Générique**   | Le paramètre `owner` permet d'utiliser exactement la même logique pour le joueur humain et l'ordinateur.                  |
| **Découplage**  | Le mélange est intégré directement au setup, assurant qu'aucune partie ne commence avec un deck non ordonné.              |

---

## Exemple de sortie

Une carte de Niveau 1 sélectionnée pour le front se verra transformée ainsi :

```typescript
{
  id: "sots-10",
  zone: "playerRanks",
  posX: 0,
  posY: 1,
  orientation: "ready"
}
```
