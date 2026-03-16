# Documentation des Types de Cycle de Jeu

## Localisation

`src/types/game.ts` (ou votre fichier de types centralisé)

## Rôle du fichier

Ce fichier définit les **états fondamentaux du moteur de jeu**. Il structure le cycle de vie d'un tour de _Warlord_, la gestion de l'initiative entre les joueurs, et la forme que prennent les instructions contextuelles affichées à l'écran.

Ces types servent de contrat entre le moteur de règles (logique) et les composants d'interface (UI).

---

## Définitions des Types

### 1. GamePhase

Représente les cinq étapes chronologiques d'un tour de jeu complet.

- **`Ready Phase`** : Préparation des troupes (redressement des cartes).
- **`Draw Phase`** : Gestion de la main et pioche.
- **`Initiative Phase`** : Jet de dés pour déterminer l'ordre d'action.
- **`Decree Phase`** : La phase d'action principale (Attaque, Manœuvre, Capacité).
- **`End of Turn Phase`** : Nettoyage des effets temporaires et passage au tour suivant.

### 2. PhaseMessage

Structure les notifications envoyées au joueur pour le guider durant la partie.

- **`type`** : Définit la sémantique visuelle du message (ex: `success` en vert, `warning` en orange).
- **`text`** : Le contenu informatif du message.
- **`icon`** : (Optionnel) Un glyphe ou emoji pour renforcer la compréhension rapide de l'action.

### 3. InitiativeWinner

Définit l'issue du jet d'initiative au début de la `Decree Phase`.

- **`player` / `ai`** : Détermine qui a l'avantage de jouer en premier.
- **`tie`** : Indique une égalité (nécessite une relance).
- **`null`** : État initial avant que les dés ne soient lancés.

---

## Pourquoi cette architecture ?

| Concept             | Bénéfice                                                                                                                                                       |
| :------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| \*\*Union Types (`  | `)\*\*                                                                                                                                                         | En utilisant des chaînes de caractères littérales plutôt que des `string` génériques, TypeScript empêche toute erreur de frappe (ex: "DrawPhase" au lieu de "Draw Phase"). |
| **Guidage UI**      | Le type `PhaseMessage` permet de créer un composant "Console" ou "Banner" qui réagit dynamiquement au type de message sans logique complexe dans le composant. |
| **Logique de Flux** | Ces types permettent d'utiliser des structures `switch` exhaustives, garantissant que chaque phase du jeu est traitée par le moteur.                           |

---

## Exemple d'intégration

```typescript
// Exemple d'un message d'action lors de la Draw Phase
const hint: PhaseMessage = {
  type: "action",
  text: "Cliquez sur une carte pour la défausser avant de piocher.",
  icon: "🃏",
};
```
