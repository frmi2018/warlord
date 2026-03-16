# Documentation de useCardEffects.ts

## Localisation

`src/hooks/useCardEffects.ts`

## Rôle du fichier

Ce hook est le **fournisseur de données statistiques pour l'UI**. Il parcourt toutes les cartes présentes sur le plateau pour calculer leurs bonus et malus passifs en temps réel.

Alors que `resolvePassiveEffects` calcule les bonus pour _une_ carte, ce hook industrialise le processus pour un camp entier (Joueur ou IA) et stocke les résultats dans une `Map` optimisée. Cela permet aux composants React (comme les cartes ou les barres de vie) d'accéder instantanément à leurs statistiques modifiées sans recalculer la logique complexe à chaque rendu.

---

## Concept : La Mémoïsation Contextuelle

Le calcul des effets passifs dépend de la position de toutes les cartes. Si une seule unité bouge ou meurt, les auras de voisinage peuvent changer.

- **Optimisation `useMemo`** : Le hook ne relance les calculs que si la référence du tableau `ownCards` ou `enemyCards` change.
- **Structure de sortie** : Il retourne une `Map<string, StatModifiers>`. L'utilisation d'une Map permet une récupération en temps constant $O(1)$ via l'ID de la carte.

---

## Fonctionnement technique

### `useCardEffects(ownCards, enemyCards)`

1.  **Entrée** : Reçoit les cartes alliées et les cartes ennemies (pour les effets de debuff ou les synergies globales).
2.  **Traitement** : Pour chaque carte du camp allié :
    - Appelle `resolvePassiveEffects`.
    - Lui passe le contexte complet (qui sont mes alliés ? qui sont mes ennemis ?).
3.  **Sortie** : Une Map où chaque clé est un `cardId` et chaque valeur est un objet `StatModifiers` contenant les bonus d'attaque, d'armure, etc.

---

## Pourquoi utiliser ce hook ?

- **Clarté de l'UI** : Permet d'afficher les valeurs d'attaque en vert (bonus) ou en rouge (malus) dans les composants de vue.
- **Performance** : Évite de recalculer les auras 50 fois par seconde. Le calcul est fait une fois par changement d'état du plateau.
- **Découplage** : Les composants visuels n'ont pas besoin de connaître les règles de voisinage ; ils demandent juste "Donne-moi les modifiers de la carte X".

---

## Interaction avec d'autres fichiers

- **`resolvePassiveEffects.ts`** : Le moteur de calcul unitaire utilisé à l'intérieur de la boucle.
- **`Card.tsx` (Composant)** : Consomme la Map pour afficher les statistiques finales à l'écran.
- **`GameBoard.tsx`** : Instancie généralement ce hook pour passer les données aux sous-composants.

---

## Résumé

Ce fichier est responsable de :

- **Mapper les bonus** sur l'ensemble des unités en jeu.
- **Garantir la performance** via la mémoïsation React.
- **Préparer les données** pour une consommation simplifiée par les composants graphiques.
  """
