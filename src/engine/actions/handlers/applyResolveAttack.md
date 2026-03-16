# Documentation de applyResolveAttack.ts

## Localisation

`src/engine/actions/handlers/applyResolveAttack.ts`

## Rôle du fichier

Ce fichier contient le **handler de résolution finale du combat**. C'est le point culminant d'une attaque : après que les dés ont été lancés, ce code calcule les conséquences physiques sur le plateau (blessures, morts, événements).

Il gère également la consommation des bonus temporaires (comme le _Powerattack_) et signale au reste du moteur que l'attaque est terminée pour déclencher d'éventuelles réactions.

---

## Concept : Le Pipeline de Dégâts

La résolution suit une séquence logique rigoureuse pour garantir que tous les effets de cartes sont pris en compte :

1.  **Calcul du Bonus** : Vérifie si un bonus de dégâts (`pendingAttackBonus`) est en attente pour cet attaquant précis.
2.  **Gestion de l'Échec** : Si l'attaque a raté (`hits: false`), le code s'arrête prématurément après avoir émis l'événement de fin.
3.  **Application des Blessures** : Calcule le total des dégâts (Base + Bonus) et met à jour le compteur `wounds` de la cible.
4.  **Gestion de la Mort** : Si les blessures égalent ou dépassent les PV (`hitPoints`), l'unité est transférée dans la défausse (`Discard`) via `moveCard`.
5.  **Réorganisation (IA)** : Si une unité ennemie meurt, le moteur appelle immédiatement `aiResolveIllegalRanks` pour combler les trous.

---

## Fonctions principales

### `applyResolveAttack(state, action): GameState`

C'est la fonction maîtresse qui transforme l'état.

- **Chaînage de Mutations** : Elle utilise un `workingState` qui est mis à jour étape par étape à travers plusieurs émissions d'événements.
- **Événements critiques** :
  - `unit_died` : Déclenché si une unité est détruite.
  - `warlord_wounded` : Déclenché si la cible est un Seigneur de Guerre (Warlord), souvent utilisé pour vérifier les conditions de victoire/défaite.
  - `after_attack` : Le signal final. **C'est cet événement qui permet à la Riposte de s'activer.**

---

## Gestion du Powerattack

Le fichier implémente une logique de consommation de bonus :

- Il vérifie si `pendingAttackBonus.attackerId` correspond à l'attaquant actuel.
- À la toute fin de la fonction, il réinitialise `pendingAttackBonus: null`. Cela garantit qu'un bonus de "Puissance" ne s'applique qu'à une seule et unique attaque, même en cas de frappes multiples.

---

## Variables et Types importants

| Élément       | Description                                                                                                           |
| :------------ | :-------------------------------------------------------------------------------------------------------------------- |
| `extraWounds` | Dégâts supplémentaires provenant de capacités activées avant l'attaque.                                               |
| `isDefeated`  | Booléen calculé par `newWounds >= hitPoints`.                                                                         |
| `killedBy`    | Information transmise à l'événement de mort pour permettre des effets de type "Quand ce personnage tue une unité...". |

---

## Interaction avec d'autres fichiers

Ce handler est au centre de l'écosystème :

- `eventBus.ts` : Pour notifier les autres cartes des blessures et des morts.
- `moveCard.ts` : Utilitaire pour déplacer proprement une carte d'une zone (Ranks) à une autre (Discard).
- `aiResolveIllegalRanks.ts` : Applique les règles de mouvement automatique de l'IA après un décès.
- `riposteFeat.ts` : Ce Feat "écoute" l'événement `after_attack` produit ici.

---

## Résumé

Ce fichier est responsable de :

- **Trancher le sort des unités** (survie ou défausse).
- **Consommer les ressources** de combat temporaires.
- **Déclencher la phase de réaction** en notifiant le bus d'événements.
- **Garantir la propreté du plateau** après un décès.
  """
