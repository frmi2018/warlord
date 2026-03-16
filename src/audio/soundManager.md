# Documentation de soundManager.ts

## Localisation

`src/audio/soundManager.ts`

## Rôle du fichier

Ce fichier est le **moteur audio** de l'application. Il centralise le chargement des ressources sonores et fournit une interface simple pour déclencher des effets sonores (SFX) lors des interactions de jeu.

L'objectif est d'enrichir l'expérience utilisateur (UX) en fournissant des retours auditifs aux actions tactiques, renforçant ainsi le sentiment de manipulation physique des cartes et des dés.

---

## Ressources Sonores (`sounds`)

Le gestionnaire expose un dictionnaire d'objets `Audio` pré-chargés :

- **`move`** : Utilisé lors de l'inclinaison d'une carte (orientation `spent`) ou d'un changement de rang.
- **`dice`** : Déclenché pendant la phase d'Initiative ou lors des Skill Checks de React.
- **`dropCard`** : Joué lorsqu'une carte est déployée de la main vers le plateau ou envoyée en défausse.

---

## Logique de Lecture : `playSound()`

La fonction `playSound` est optimisée pour la réactivité du jeu :

1.  **Réinitialisation (`currentTime = 0`)** : Permet de rejouer le son instantanément même si la lecture précédente n'était pas terminée. C'est essentiel pour des actions rapides (ex: déplacer plusieurs cartes à la suite).
2.  **Gestion du Silence (`.catch`)** : Les navigateurs modernes bloquent souvent l'audio automatique sans interaction préalable de l'utilisateur. Le bloc `catch` vide empêche l'application de planter (erreurs de promesse) si le navigateur bloque le son.

---

## Pourquoi cette architecture ?

| Avantage                | Description                                                                                                                       |
| :---------------------- | :-------------------------------------------------------------------------------------------------------------------------------- |
| **Chargement Unique** : | Les fichiers audio sont instanciés une seule fois au démarrage, évitant des requêtes réseau répétées à chaque action.             |
| **Découplage** :        | Les composants UI n'ont pas besoin de connaître le chemin des fichiers `.mp3`. Ils appellent simplement `playSound(sounds.move)`. |
| **Maintenance** :       | Si vous souhaitez changer le bruitage des dés, vous n'avez qu'à modifier une seule ligne dans ce fichier.                         |

---

## Exemple d'intégration dans un composant

```typescript
import { playSound, sounds } from "../audio/soundManager";

const handleAttack = () => {
  // Logique d'attaque...
  playSound(sounds.dice); // Feedback sonore immédiat
};
```
