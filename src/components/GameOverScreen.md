# Documentation de GameOverScreen.tsx

## Localisation

`src/components/GameOverScreen.tsx`

## Rôle du fichier

Ce composant est l'**écran de conclusion**. Il s'affiche en superposition (overlay) totale dès que la condition de fin de partie est détectée (mort d'un Warlord). Il a pour but de fournir un feedback émotionnel fort au joueur tout en lui offrant les options de navigation pour la suite.

---

## Anatomie du Composant

Le composant adapte dynamiquement son apparence selon le résultat (`GameResult`) :

### 1. Feedback Visuel Différencié

- **Mode Victoire** : Utilise une palette de couleurs dorées (`#ffd700`), un glyphe d'épées croisées et une lueur radiale animée.
- **Mode Défaite** : Bascule sur des tons rouges (`#cc3333`), une icône de crâne et une ambiance plus sombre.

### 2. Éléments de Design "Grim-Fantasy"

Pour coller à l'esthétique de _Warlord_, le panneau utilise :

- Des polices Serif (`Georgia`) pour un aspect médiéval/épique.
- Un séparateur ornemental avec un diamant central (`◆`).
- Un effet de flou d'arrière-plan (`backdropFilter: "blur(6px)"`) pour isoler l'issue du combat du plateau de jeu resté en dessous.

---

## Interactions et Navigation

Le composant propose deux chemins distincts au joueur :

1.  **Rejouer (`onNewGame`)** : Relance immédiatement une partie avec les mêmes paramètres de deck, permettant une revanche rapide.
2.  **Quitter (`onBackToHome`)** : Renvoie le joueur à l'écran d'accueil pour changer de faction ou modifier ses réglages.

### Gestion des États de Survol (Hover)

Le code utilise des gestionnaires d'événements JS (`onMouseEnter`/`onMouseLeave`) pour animer les boutons.

- **Bouton Primaire** : Transition de contour vers un fond plein doré, avec un léger agrandissement (`scale(1.05)`).
- **Bouton Secondaire** : Apparition d'un bord plus clair et d'un fond subtilement translucide.

---

## Pourquoi cette architecture ?

| Avantage                   | Description                                                                                                                                         |
| :------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Z-Index Maximum**        | Avec un `zIndex: 1000`, cet écran s'assure de couvrir toutes les modales de combat ou de phase restées ouvertes.                                    |
| **Styles Inline Typés**    | L'utilisation de `React.CSSProperties` permet de garder la logique de style proche du composant tout en bénéficiant de l'autocomplétion TypeScript. |
| **Immuabilité de l'Issue** | Le composant reçoit un `result` simple, ce qui évite de recalculer la logique de victoire/défaite à l'intérieur du rendu.                           |

---

## Résumé

`GameOverScreen.tsx` est le **point final du cycle de jeu**. Il transforme une donnée technique (mort d'une carte) en une expérience cinématographique simple, tout en assurant la transition propre vers une nouvelle session ou vers le menu principal.
