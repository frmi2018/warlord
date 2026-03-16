# Documentation de applyPass.ts

## Localisation

`src/engine/actions/handlers/applyPass.ts`

## Rôle du fichier

Ce fichier gère la logique de l'action **"Passer" (Pass)**. Dans le système de jeu, passer n'est pas une action neutre : c'est un mécanisme de contrôle qui permet de déterminer quand une phase ou un tour s'arrête.

Le rôle principal de ce handler est de tenir le compte des joueurs qui ont choisi de ne pas agir.

---

## Concept : Les Passes Consécutives

Le flux du jeu repose sur l'alternance entre le joueur et l'IA.

- Lorsqu'un joueur passe, il signale qu'il a terminé ses actions pour le moment.
- Si le joueur suivant passe immédiatement après (passes consécutives = 2), la phase actuelle se termine généralement (ex: fin de la Phase de Décret).

---

## Fonctionnement de l'Action

### `applyPass(state)`

La fonction est extrêmement simple et prédictive :

1.  **Incrémentation** : Elle prend la valeur actuelle de `consecutivePasses` dans le `state` et lui ajoute 1.
2.  **Mise à jour** : Elle retourne une copie de l'état avec cette nouvelle valeur.

_Note : Contrairement à d'autres actions, elle ne prend pas d'objet `action` complexe en paramètre (comme un ID de carte), car l'acte de passer ne nécessite aucune information supplémentaire._

---

## Variables importantes

| Variable            | Type     | Description                                                                                                |
| :------------------ | :------- | :--------------------------------------------------------------------------------------------------------- |
| `consecutivePasses` | `number` | Le compteur de passes. Remis à 0 dès qu'un joueur effectue une action réelle (attaque, déploiement, etc.). |

---

## Interaction avec le moteur de jeu

Bien que ce fichier ne fasse qu'incrémenter un compteur, c'est le **`gameReducer`** (ou le gestionnaire de phases) qui va lire cette valeur pour décider de la suite :

- **Si `consecutivePasses` < 2** : Le tour passe simplement au joueur suivant.
- **Si `consecutivePasses` >= 2** : Le moteur déclenche le changement de phase ou de tour.

---

## Résumé

Ce fichier garantit que :

- Le moteur de jeu garde une trace précise de **l'inactivité volontaire** des joueurs.
- La condition de **fin de phase** peut être calculée de manière fiable.
- L'action de passer est traitée comme une **transition d'état** immuable.
