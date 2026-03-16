# Documentation de aiResolveIllegalRanks.ts

## Localisation

`src/engine/aiResolveIllegalRanks.ts`

## Rôle du fichier

Ce fichier contient la **logique décisionnelle de l'IA** lorsqu'elle doit corriger sa formation de combat. Contrairement au joueur humain qui choisit manuellement quelle unité faire avancer pour combler un trou, l'IA utilise un algorithme de "score de sacrifice" pour minimiser l'impact tactique de ses mouvements obligatoires.

Il assure que l'armée de l'IA respecte toujours les règles de cohésion (pas de rangs vides devant des rangs occupés).

---

## Stratégie de "Sacrifice"

L'IA évalue le coût opérationnel de chaque mouvement. Avancer une unité (Fall Forward) change son état d'orientation selon la hiérarchie de fatigue du jeu.

### Barème des scores (`getCardSacrificeScore`)

L'IA calcule un score pour chaque candidat potentiel. Plus le score est élevé, plus l'IA considère la carte comme "prioritaire" pour avancer :

| État actuel | Score de base | Raisonnement Tactique                                                                        |
| :---------- | :------------ | :------------------------------------------------------------------------------------------- |
| **Stunned** | **100**       | La carte est déjà neutralisée. Avancer ne lui coûte rien de plus.                            |
| **Spent**   | **50**        | La carte a déjà agi. Devenir `Stunned` est acceptable pour boucher un trou.                  |
| **Ready**   | **0-10**      | L'IA protège ses unités prêtes. Si elle doit choisir, elle sacrifie l'unité la plus blessée. |

---

## Fonctionnement de l'Algorithme

La fonction `aiResolveIllegalRanks` traite les erreurs de formation en cascade :

1.  **Diagnostic** : Elle appelle `checkIllegalRanks` pour identifier les positions illégales.
2.  **Sélection** : Elle identifie toutes les cartes situées derrière les vides et utilise `chooseBestCardToFall` pour trouver le "meilleur" candidat selon le barème ci-dessus.
3.  **Action** : Elle applique `fallForward` sur la carte choisie (calcul des nouvelles coordonnées et mise à jour de l'orientation).
4.  **Répétition** : Elle recommence le processus jusqu'à ce que la formation soit parfaitement légale.

---

## Sécurité et Robustesse

- **Prévention des boucles** : Un compteur de sécurité (`MAX_ITERATIONS`) empêche le moteur de boucler indéfiniment en cas de configuration de plateau imprévue.
- **Gestion des PV** : En cas d'égalité d'orientation entre deux cartes `Ready`, l'IA utilise le ratio de blessures (`woundsRatio`) pour envoyer l'unité la plus affaiblie au front et préserver les unités saines.

---

## Résumé

Ce fichier garantit que :

- L'IA **s'auto-corrige** sans intervention du joueur ou du moteur principal.
- Les mouvements forcés sont **tactiquement intelligents** (protection des unités prêtes).
- Le plateau reste **conforme aux règles** de Warlord après chaque phase de combat ou de déploiement.
