# Documentation de getPhaseMessages.ts

## Localisation

`src/engine/phases/getPhaseMessages.ts`

## Rôle du fichier

Ce fichier est le **moteur narratif et pédagogique** de l'interface utilisateur. Il transforme l'état brut du moteur de jeu (variables numériques, phases, résultats de dés) en messages textuels clairs et actionnables pour le joueur.

Il garantit que le joueur ne se sente jamais perdu, en fournissant un contexte précis sur ce qui vient de se passer et sur ce qu'il doit faire ensuite.

---

## Fonctionnement des Messages par Phase

Le script analyse la phase actuelle (`currentPhase`) et adapte dynamiquement le contenu :

### 1. Draw Phase (Pioche)

Le service calcule la différence entre la main actuelle et la taille standard (5 cartes).

- **Feedback** : Indique combien de cartes seront piochées.
- **Alertes** : Prévient si la main est pleine et incite à la défausse.
- **Actions** : Guide le joueur sur la sélection des cartes à jeter.

### 2. Initiative Phase (Dés)

Gère l'affichage des résultats du jet d'initiative.

- **Gestion des Critiques** : Affiche un message spécial en cas de `20` naturel (Critical Success).
- **Gestion des Égalités** : Identifie les cas de "Tie" et demande une relance.
- **Verdict** : Annonce clairement qui a gagné le droit de jouer en premier.

### 3. Decree Phase (Action)

C'est la phase la plus interactive. Les messages rappellent :

- **Le tour de jeu** : Qui de l'humain ou de l'IA doit agir.
- **Les instructions** : Rappelle qu'il faut cliquer sur une carte pour voir les actions (attaquer, manœuvrer).

---

## Structure de Données de Sortie

La fonction retourne un objet structuré pour alimenter un composant d'interface (souvent une bannière ou une modale latérale) :

| Propriété  | Type             | Description                                                                                |
| :--------- | :--------------- | :----------------------------------------------------------------------------------------- |
| `title`    | `string`         | Le nom de la phase actuelle.                                                               |
| `icon`     | `string`         | Un emoji représentatif (ex: ⚔️ pour le combat).                                            |
| `messages` | `PhaseMessage[]` | Une liste d'objets contenant le texte et le type (`success`, `warning`, `info`, `action`). |

---

## Pourquoi cette architecture ?

- **Séparation des préoccupations** : Le code qui calcule les règles de jeu reste séparé du code qui rédige les textes. Cela permet de traduire le jeu plus facilement à l'avenir.
- **UX Dynamique** : En utilisant des types de messages (`warning`, `success`), l'UI peut appliquer des couleurs différentes (rouge, vert, bleu) pour hiérarchiser l'information.
- **Robustesse** : L'utilisation d'une interface `GetPhaseMessagesParams` garantit que toutes les données nécessaires (scores de dés, taille de main) sont présentes avant de générer les textes.

---

## Résumé

Ce fichier garantit que :

- Le joueur bénéficie d'une **assistance contextuelle** permanente.
- Les **règles complexes** (comme l'initiative critique) sont explicitées.
- L'interface utilisateur reste **vivante et réactive** aux événements du jeu.
