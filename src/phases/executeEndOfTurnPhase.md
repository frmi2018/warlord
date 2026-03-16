# Documentation de executeEndOfTurnPhase.ts

## Localisation

`src/engine/phases/executeEndOfTurnPhase.ts`

## Rôle du fichier

Ce fichier gère la **End of Turn Phase** (Phase de Fin de Tour). Son rôle principal est d'effectuer le "ménage" administratif sur les cartes avant de passer officiellement la main à l'adversaire ou de passer au tour suivant.

Elle assure que les restrictions de mouvement et les bonus éphémères sont levés, remettant les compteurs logiques à zéro.

---

## Logique de Nettoyage

La phase parcourt l'ensemble des cartes du joueur actif pour réinitialiser les drapeaux de statut (flags) qui ne doivent durer qu'un seul tour.

### 1. Réinitialisation du Mouvement

- **`movedThisTurn`** : Dans Warlord, le mouvement est souvent limité (ex: une unité ne peut se déplacer qu'une fois par tour). Ce flag est passé à `false` pour toutes les cartes, permettant à nouveau le mouvement au prochain tour.

### 2. Gestion des États Temporaires (Extensions futures)

Bien que le fichier soit actuellement focalisé sur le mouvement, il est la destination prévue pour :

- La suppression des bonus d'ATK ou de CA temporaires (ex: "+2 ATK jusqu'à la fin du tour").
- Le retrait des jetons ou effets visuels liés à des actions spécifiques du tour.

---

## Fonctionnement technique : `executeEndOfTurnPhase()`

La fonction est conçue pour être appelée à la toute fin de la séquence d'actions d'un camp.

1.  **Immuabilité** : Comme pour les autres phases, elle utilise `.map()` pour retourner une nouvelle instance du tableau de cartes, déclenchant ainsi la mise à jour propre de l'état dans React.
2.  **Paramètre `owner`** : Bien que pré-configuré, le paramètre `_owner` permet de cibler spécifiquement les cartes d'un joueur si des effets de fin de tour ne concernaient que le joueur actif (ex: capacités de type "At the end of your turn...").

---

## Pourquoi cette phase est-elle nécessaire ?

| Problème évité                  | Solution apportée                                                                                                                                     |
| :------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mouvement infini**            | Sans la réinitialisation de `movedThisTurn`, une unité ayant bougé une fois resterait bloquée pour le reste de la partie.                             |
| **Bonus cumulatifs infinis**    | Garantit que les sorts et capacités "jusqu'à la fin du tour" expirent effectivement, évitant que les unités ne deviennent surpuissantes par accident. |
| **Synchronisation Multijoueur** | En exécutant cette phase, les deux clients s'accordent sur le fait que le tour est "propre" avant de transférer le contrôle.                          |

---

## Résumé

Ce fichier garantit que :

- Le **droit de mouvement** est restauré pour le tour suivant.
- Les **états logiques temporaires** sont purgés.
- Le passage d'un tour à l'autre se fait sur une **base de données saine**.
