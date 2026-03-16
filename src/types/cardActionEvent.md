# Documentation de cardActionEvent.ts

## Localisation

`src/types/cardActionEvent.ts`

## Rôle du fichier

Ce fichier définit le **système de messagerie** entre l'interface utilisateur (UI) et le moteur de jeu (Logic). Il utilise une **union discriminée** pour typer précisément chaque action possible lorsqu'un joueur interagit avec une carte via la `CardActionsModal`.

Plutôt que de passer des fonctions complexes, l'UI émet un "événement" structuré. Cela permet au `GameBoard` de traiter l'action de manière centralisée et sécurisée.

---

## Structure des Événements

Le système sépare les actions en deux catégories distinctes :

### 1. Les Actions Génériques (`GenericActionEvent`)

Ce sont les verbes de base du jeu, communs à la plupart des cartes selon leur contexte (en main ou sur le plateau).

- **`attack` / `maneuver`** : Actions standards pour les personnages déployés.
- **`deploy`** : Placer un personnage depuis la main vers les rangs.
- **`equip` / `play`** : Utiliser un objet ou une carte d'action depuis la main.

### 2. Les Actions Spécifiques (`UseCardActionEvent`)

Certaines cartes possèdent des capacités uniques listées dans leur définition (tableau `actions[]`).

- **`actionIndex`** : Ce champ est crucial. Il indique au moteur quel index précis du tableau d'actions de la carte doit être exécuté. Cela permet de supporter des cartes ayant plusieurs pouvoirs distincts.

---

## Fonctionnement du Flux "Action-Réaction"

1. **Ouverture** : Le joueur clique sur une carte. La `CardActionsModal` s'ouvre.
2. **Choix** : Le joueur clique sur le bouton "Attaquer" ou sur une capacité spéciale "Boule de feu".
3. **Émission** : La modale émet un objet `CardActionEvent` :
   - Ex: `{ type: "attack" }`
   - ou `{ type: "useCardAction", actionIndex: 0 }`.
4. **Traitement** : Le composant parent (`GameBoard`) reçoit cet objet et appelle la fonction logique correspondante dans le réducteur de jeu.

---

## Pourquoi cette architecture ?

| Avantage        | Description                                                                                                                                                    |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Type-Safety** | L'union discriminée garantit que si `type` est `"useCardAction"`, le champ `actionIndex` est obligatoirement présent et typé.                                  |
| **Découplage**  | La modale d'action n'a pas besoin de savoir _comment_ attaquer. Elle sait juste envoyer le signal "Le joueur veut attaquer".                                   |
| **Simplicité**  | Centraliser les types d'actions ici évite la prolifération de props de type "callback" (`onAttack`, `onManeuver`, `onDeploy`, etc.) dans vos composants React. |

---

## Exemple d'utilisation dans React

```typescript
const handleAction = (event: CardActionEvent) => {
  if (event.type === "useCardAction") {
    // Le moteur sait qu'il doit aller chercher la capacité n° X
    executeSpecialAbility(selectedCard, event.actionIndex);
  } else {
    // Traitement des actions standards
    performGenericAction(event.type);
  }
};
```
