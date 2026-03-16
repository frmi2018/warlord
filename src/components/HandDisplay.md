# Documentation de HandDisplay.tsx

## Localisation

`src/components/HandDisplay.tsx`

## Rôle du fichier

Ce composant est responsable de l'affichage de la **main du joueur** (ou de l'IA). Il ne se contente pas de lister les cartes ; il gère des états complexes comme la sélection multiple pour la défausse, le masquage des cartes adverses et les outils de débogage pour le développement.

L'affichage est optimisé pour occuper un minimum d'espace vertical tout en restant lisible grâce à un système de "recouvrement partiel".

---

## Logique d'Affichage et Dimensions

Pour maintenir un plateau de jeu compact, les cartes en main ne sont pas affichées en entier :

- **`CARD_HEIGHT`** (146px) : La hauteur réelle de la carte réduite.
- **`VISIBLE_HEIGHT`** (~102px) : La zone visible de la carte. Le bas est masqué par un dégradé (`linear-gradient`) pour suggérer que les cartes sont tenues en main.
- **Débordement** : Un `overflow: hidden` sur le conteneur de chaque carte permet de ne montrer que le haut (nom, niveau, icône de classe).

---

## Fonctionnalités Clés

### 1. Mode Défausse (`Draw Phase`)

Pendant la phase de pioche, le comportement du clic change :

- Au lieu d'ouvrir les actions de la carte, le clic bascule l'état **sélectionné**.
- Les cartes sélectionnées subissent une translation verticale (`translateY(-8px)`) et affichent un badge "✕" rouge.
- La liste des IDs sélectionnés est transmise au parent via `onSelectionChange`.

### 2. Masquage et Sécurité (Brouillard de guerre)

Le composant gère la confidentialité des informations :

- **`hideCards`** : Si `true`, le contenu des cartes est remplacé par un indicateur textuel (ex: "🃏 5 carte(s) cachée(s)").
- **Mode Debug** : Un bouton discret permet au développeur de révéler la main de l'IA sans changer l'état global du jeu.

### 3. Cycle de Vie et Nettoyage

Le composant utilise des `useEffect` pour garantir la cohérence des données :

- Si la phase change et n'est plus la `Draw Phase`, la sélection est automatiquement vidée.
- Toute modification de la sélection locale est immédiatement synchronisée avec le moteur de jeu parent.

---

## Pourquoi cette architecture ?

| Avantage                   | Description                                                                                                |
| :------------------------- | :--------------------------------------------------------------------------------------------------------- |
| **Économie d'Espace**      | Le rendu partiel permet d'afficher une main de 8-10 cartes sans étouffer le plateau central.               |
| **UX Intuitive**           | Le levage de la carte (`translateY`) imite le geste réel de sortir une carte de son jeu pour la défausser. |
| **Outils de Dev intégrés** | Le bouton de Debug permet de tester l'IA en temps réel sans avoir à manipuler la console ou le code.       |

---

## Exemple d'Intégration

```tsx
<HandDisplay
  cards={playerHand}
  hideCards={false}
  currentPhase={gameState.currentPhase}
  onSelectionChange={(count, ids) =>
    console.log(`${count} cartes prêtes à être jetées`)
  }
/>
```
