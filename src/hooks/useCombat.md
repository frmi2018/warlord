# Documentation de useCombat.ts

## Localisation

`src/hooks/useCombat.ts`

## Rôle du fichier

Le hook `useCombat` est le **chef d'orchestre de l'interface utilisateur** pour toute la séquence de combat. Il gère l'état local (UI) nécessaire pour guider le joueur à travers les étapes du combat : de la sélection de l'attaquant jusqu'à la résolution des frappes multiples.

C'est un composant de "présentation" : il ne calcule pas les dégâts lui-même, mais il synchronise les modals (dés, choix de cibles) avec le moteur de jeu via le dispatcher.

---

## Le Flux de Combat (Séquence UI)

Le combat suit un cheminement précis pour garantir que les actions envoyées au moteur sont complètes :

1.  **Phase de Préparation** (`startAttack`) : Le joueur clique sur "Attaquer". L'ID de l'attaquant est stocké localement.
2.  **Phase de Ciblage** (`selectTarget`) : Le joueur choisit une proie. C'est à ce moment précis que le premier `dispatch` a lieu (`spendAttacker`), car nous avons enfin le couple Attaquant/Cible.
3.  **Phase de Jet** (`isRollingDice`) : La modal des dés s'affiche (si aucune fenêtre de réaction ne l'interrompt).
4.  **Phase de Résolution** (`confirmCombat`) : Le résultat des dés est envoyé au moteur (`resolveAttack`).
5.  **Phase de Strikes Multiples** : Si le personnage a encore des frappes, le cycle reprend à l'étape 2 ou se termine via `stopAttack`.

---

## Sécurité et Anti-Exploit (Sprint R2-fix.2)

Une attention particulière a été portée à la triche potentielle liée aux capacités de réaction :

- **Verrouillage de l'annulation** (`canCancelAttack`) : Dans Warlord, certaines capacités (Feats) comme _Powerattack_ se déclenchent **avant** le jet de dés.
- **Le problème** : Sans verrou, un joueur pourrait tenter un _Powerattack_, rater son jet de compétence, puis annuler son attaque pour recommencer gratuitement.
- **La solution** : Dès qu'une fenêtre de réaction s'ouvre (`openedReactWindow`), le bouton "Annuler" est désactivé. Le joueur est engagé dans son action.

---

## Fonctions principales

### `startAttack(attacker)`

Initialise le nombre de frappes disponibles (Strikes). Elle vérifie également si des objets équipés imposent des malus limitant les attaques multiples.

### `selectTarget(target)`

Déclenche l'action `spendAttacker`. Cette étape est cruciale car elle peut être interceptée par une "React Window" (ex: une capacité de défense adverse).

### `confirmCombat(result)`

Applique les dégâts. Si c'est la dernière frappe, elle appelle `finishAttack` pour épuiser définitivement la carte.

### `stopAttack()`

Permet au joueur de renoncer à ses frappes optionnelles (2ème ou 3ème strike). Conformément à la 4e édition, seul le premier strike est obligatoire.

---

## Variables et États UI

| État                 | Description                                                                          |
| :------------------- | :----------------------------------------------------------------------------------- |
| `isSelectingTarget`  | Active l'overlay de sélection sur les cartes ennemies.                               |
| `isRollingDice`      | Conditionne l'affichage de la modal de lancer de dés (D20).                          |
| `currentStrikeIndex` | Suit la progression des attaques pour les unités "Multi-Strike".                     |
| `pendingRef`         | Bloque la fermeture de la séquence tant qu'un effet "After Attack" n'est pas résolu. |

---

## Interaction avec d'autres fichiers

- **`useReactSettled.ts`** : Surveille la fin des réactions pour fermer proprement les modals de combat.
- **`rollCombat.ts`** : Fournit le type `CombatRollResult` généré par la modal de dés.
- **`soundManager.ts`** : Déclenche les retours sonores lors des clics d'attaque.

---

## Résumé

Ce hook est responsable de :

- **Guider l'utilisateur** à travers les étapes du combat.
- **Empêcher les triches** liées à l'annulation d'actions après des jets de dés/compétences.
- **Gérer la complexité des frappes multiples** de manière transparente.
- **Synchroniser l'affichage des modals** avec l'état de réaction du moteur.
  """
