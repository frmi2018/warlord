# Documentation de combat.ts

## Localisation

`src/engine/combat.ts`

## Rôle du fichier

Ce fichier est le **moteur de résolution physique du combat**. Il contient les fonctions pures chargées de traduire les résultats d'un duel (calculés par les dés) en modifications concrètes sur le `GameState`.

Sa particularité est d'orchestrer le **Bus d'Événements**, permettant aux cartes présentes sur le plateau de "réagir" aux différentes étapes d'une attaque (ex: une capacité qui s'active quand un allié meurt).

---

## Concept : Le Cycle de Vie d'une Attaque

Le combat ne se limite pas à retirer des points de vie ; il suit une séquence rigoureuse pour garantir que tous les passifs et réactions se déclenchent dans le bon ordre :

1.  **Déclaration** : `spendAttacker` marque l'unité comme ayant agi (orientation `spent`).
2.  **Pré-calcul** : L'événement `before_attack` est émis.
3.  **Application** : Les blessures sont ajoutées ou l'unité est transférée en défausse via `moveCard`.
4.  **Notifications spéciales** : Émission de `unit_died` ou `warlord_wounded` si nécessaire.
5.  **Clôture** : L'événement `after_attack` est émis (déclenchant potentiellement une Riposte).

---

## Fonctions principales

### `spendAttacker` / `cancelAttack`

Gèrent l'état de disponibilité de la carte.

- **Spend** : Passe l'orientation en `spent`.
- **Cancel** : Repasse l'orientation en `ready`. C'est la fonction de sécurité si le joueur annule son action avant la validation finale.

### `resolveCombatInState(state, attacker, target, result): GameState`

La fonction maîtresse utilisée durant le tour du joueur.

- Elle orchestre la modification de l'état global étape par étape.
- Elle gère la **mort des unités** : utilise `moveCard` pour un nettoyage complet de la carte (reset des positions, des blessures, etc.) avant de l'envoyer dans `aiDiscard`.
- Elle surveille les **Warlords** : si un Seigneur de Guerre est blessé, elle émet un signal spécifique pour les conditions de victoire.

---

## Événements de Combat Émis

| Événement         | Moment            | Utilité                                                   |
| :---------------- | :---------------- | :-------------------------------------------------------- |
| `before_attack`   | Avant les dégâts  | Boucliers temporaires, modificateurs de dernière seconde. |
| `unit_died`       | Si `wounds >= HP` | Effets de "Râle d'agonie" ou bonus de victoire.           |
| `warlord_wounded` | Cible = Warlord   | Déclenchement de phases de boss ou fin de partie.         |
| `after_attack`    | Fin de résolution | **Crucial pour le système de Reacts (Riposte).**          |

---

## Variables et Types importants

| Élément            | Description                                                                           |
| :----------------- | :------------------------------------------------------------------------------------ |
| `CombatRollResult` | Contient `hits` (bool), `damage` (nombre) et `attackerRoll`.                          |
| `moveCard`         | Garantit qu'une carte sortant du plateau est "nettoyée" (reset wounds/pos).           |
| `globalEventBus`   | Le médiateur qui découple la logique de combat de la logique individuelle des cartes. |

---

## Résumé

Ce fichier est responsable de :

- **Traduire le résultat des dés** en conséquences concrètes (dégâts, mort).
- **Gérer le cycle de vie** de l'attaquant (prêt/épuisé).
- **Notifier le système** de chaque micro-étape du combat.
- **Assurer l'intégrité du plateau** en déplaçant proprement les unités éliminées.
  """
