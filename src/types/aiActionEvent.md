# Documentation de aiActionEvent.ts

## Localisation

`src/types/aiActionEvent.ts`

## Rôle du fichier

Ce fichier définit le **langage de communication de l'IA**. Contrairement aux actions du joueur qui sont impulsives, chaque décision prise par l'IA est encapsulée dans un objet `AiActionEvent` riche en métadonnées.

Ces événements servent deux objectifs :

1. **Exécution** : Dire au moteur de jeu quelle modification appliquer au plateau.
2. **Explicabilité (Debug)** : Expliquer au développeur (et parfois au joueur) _pourquoi_ l'IA a fait ce choix via des champs de priorité et des journaux de combat.

---

## Structure des Événements

Chaque événement hérite de `AiDebugMeta`, garantissant que chaque action de l'IA est traçable.

### 1. Métadonnées de Debug (`AiDebugMeta`)

- **`priorityNumber`** : Le score attribué à cette action par l'arbre de décision de l'IA.
- **`priorityReason`** : Une chaîne de caractères expliquant le raisonnement (ex: "Cible avec PV faibles détectée").

### 2. Actions de Combat et de Mouvement

- **`AiAttackEvent`** : Contient un `AiCombatLogEntry[]`. C'est un rapport détaillé de chaque jet de dé effectué par l'IA pendant son offensive.
- **`AiManeuverEvent`** : Détaille le déplacement d'une unité entre deux rangs (`fromRank` vers `toRank`).
- **`AiPlayActionCardEvent`** : Gère l'utilisation de cartes tactiques (ex: "Charge"). Il combine le nom de la carte et l'effet produit sur le combattant ciblé.

### 3. Gestion de Main et Équipement

- **`AiPlayCardEvent`** : Déploiement d'un nouveau personnage dans la formation.
- **`AiEquipItemEvent`** : Attachement d'un objet (arme, armure) à un personnage spécifique.

---

## Pourquoi cette architecture ?

| Avantage               | Description                                                                                                                                                    |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Transparence**       | Grâce au `log` dans l'attaque, le joueur peut voir exactement comment les dégâts ont été calculés, évitant le sentiment de "triche".                           |
| **Interface Réactive** | Ces types permettent de piloter des composants comme le `AiActionToast`, qui affiche en temps réel des messages clairs : _"L'IA joue Charge sur son Warlord"_. |
| **Optimisation IA**    | En stockant la `priorityReason`, les développeurs peuvent ajuster l'agressivité ou la prudence de l'IA en observant les scores de décision dans la console.    |

---

## Flux d'Exécution de l'IA

Le moteur de l'IA évalue toutes les possibilités, puis émet l'événement ayant la plus haute priorité :

1. **Analyse** : L'IA scanne le plateau et sa main.
2. **Notation** : Elle crée plusieurs `AiActionEvent` potentiels avec des scores de priorité.
3. **Émission** : L'événement gagnant est envoyé au `GameBoard`.
4. **Visualisation** : L'UI intercepte l'événement pour afficher l'animation ou le message correspondant avant d'appliquer les changements.

---

## Résumé

Ce fichier transforme les calculs mathématiques "froids" de l'IA en **intentions structurées**. Il permet de faire de l'IA un adversaire non seulement fonctionnel, mais aussi compréhensible et déboguable, facilitant l'équilibrage du jeu sur le long terme.
