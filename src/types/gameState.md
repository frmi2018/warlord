# Documentation de gameState.ts

## Localisation

`src/types/gameState.ts`

## Rôle du fichier

Ce fichier définit le **GameState canonique**, c'est-à-dire l'unique source de vérité pour l'état d'une partie de _Warlord_. Il agit comme un "instantané" (snapshot) complet permettant de décrire la situation précise de la partie à n'importe quel moment $T$.

### Principes Fondamentaux

- **Sérialisation Totale** : L'objet est conçu pour être transformé en JSON (`JSON.stringify`) sans perte. Cela permet la sauvegarde, le mode multijoueur et le "Time Travel Debugging".
- **Pureté** : Aucune fonction, classe ou référence React n'est stockée ici. Seules des données brutes (primitives, objets plats, tableaux) sont autorisées.

---

## Architecture de l'État

### 1. Structure du Jeu (Cycle de Vie)

Le `GameState` suit la progression temporelle via :

- `phase` : La phase actuelle (Ready, Draw, etc.).
- `turnNumber` : Le compteur de tours.
- `activePlayer` : Qui possède la main.
- `initiativeWinner` : L'avantage tactique du tour.

### 2. Système de "Reacts" (Sprint R.2)

C'est la partie la plus complexe du moteur. Elle gère les interruptions (capacités jouées en réponse à une action adverse).

- **`reactWindow`** : Lorsqu'une fenêtre est ouverte (non-null), le flux normal du jeu est "mis en pause" jusqu'à ce que les joueurs décident de réagir ou de passer.
- **`pendingTriggerAction`** : Stocke l'action initiale qui a été interrompue (ex: une attaque) pour l'exécuter une fois les réactions résolues.
- **`reactUsedThisTurn`** : Un registre qui garantit le respect de la règle "Une fois par tour" pour les capacités spéciales (Feats).

### 3. Gestion des Actions en Attente

Le moteur utilise plusieurs "buffers" pour stocker des bonus ou des choix qui ne peuvent pas être résolus instantanément :

- `pendingReroll` : Utilisé pour les capacités comme _Charisma_.
- `pendingAttackBonus` : Stocke les dégâts supplémentaires (ex: _Powerattack_).
- `pendingMarksmanshipChoice` : Met le jeu en attente d'un choix utilisateur spécifique.

---

## Le Résultat des Dés (`ReactCheckResult`)

Cette interface structure le résultat des jets de compétence (Skill Checks).

- **Calcul** : $\text{Total} = \text{Roll (d20)} + \text{Skill}$.
- **DC (Difficulty Class)** : Fixé à **20** selon les règles officielles (4e édition).
- **Transience** : Ces données sont purement informatives pour l'UI (`lastReactResult`) et sont réinitialisées régulièrement pour ne pas encombrer l'état.

---

## Pourquoi cette architecture ?

| Avantage            | Description                                                                                                   |
| :------------------ | :------------------------------------------------------------------------------------------------------------ |
| **Prédictibilité**  | Étant donné un `GameState` et une action, le réducteur (`gameReducer`) produira toujours le même nouvel état. |
| **Persistence**     | Facilite la mise en place d'un bouton "Reprendre la partie" puisque tout l'état est dans un seul objet JSON.  |
| **Synchronisation** | En multijoueur, envoyer cet objet via le réseau suffit à synchroniser parfaitement deux clients.              |

---

## Résumé

Le `GameState` est le **système nerveux** du jeu. Il ne se contente pas de lister les cartes, il gère la pile d'exécution des actions complexes, les fenêtres de réaction et la mémoire des capacités utilisées, garantissant ainsi le respect total des règles de _Warlord_.
