# Documentation de checkIllegalRanks.ts

## Localisation

`src/utils/checkIllegalRanks.ts`

## Rôle du fichier

Ce fichier implémente l'une des règles de placement les plus emblématiques de _Warlord_ : la **Contrainte de Formation en Pyramide**.

Il sert à valider que l'armée d'un joueur respecte une structure cohérente sur le champ de bataille. Un rang (ligne) ne peut pas être plus fourni en soldats que le rang situé directement devant lui. Cette règle simule le fait que les troupes de soutien doivent être protégées ou "couvertes" par une ligne de front adéquate.

---

## La Règle d'Or de la Formation

La règle stipule qu'un rang $N$ est considéré comme **illégal** si :
$$\text{Nombre de personnages au Rang } N > \text{Nombre de personnages au Rang } (N-1)$$

### Exemples de validité :

| Formation                       | État           | Raison                                                     |
| :------------------------------ | :------------- | :--------------------------------------------------------- |
| **R1: [3] / R2: [2] / R3: [1]** | ✅ Valide      | 3 ≥ 2 et 2 ≥ 1. Structure pyramidale respectée.            |
| **R1: [2] / R2: [2] / R3: [0]** | ✅ Valide      | Les rangs peuvent être de taille égale.                    |
| **R1: [1] / R2: [2]**           | ❌ **Illégal** | Le Rang 2 (2) dépasse le Rang 1 (1).                       |
| **R1: [0] / R2: [1]**           | ❌ **Illégal** | Le front est vide, le Rang 2 n'a aucun soutien devant lui. |

---

## Fonctionnement de l'Analyseur

La fonction `checkIllegalRanks` procède en trois étapes :

1.  **Filtrage** : Elle isole uniquement les cartes situées dans la zone de combat spécifiée (`playerRanks` ou `aiRanks`).
2.  **Groupement** : Elle regroupe les cartes par leur position verticale (`posY`). Chaque groupe représente un rang.
3.  **Comparaison en cascade** : Elle itère sur les rangs à partir du Rang 2. Pour chaque rang, elle compte les unités et compare ce total au nombre d'unités du rang immédiatement inférieur ($posY - 1$).

---

## Interface de Retour

### `IllegalRanksResult`

Le moteur ne se contente pas de dire "Vrai" ou "Faux", il fournit des détails précis pour aider l'interface à mettre en évidence les zones problématiques.

| Propriété         | Description                                                                                           |
| :---------------- | :---------------------------------------------------------------------------------------------------- |
| `hasIllegalRanks` | Booléen indiquant la présence d'au moins une erreur.                                                  |
| `illegalRanks`    | Un tableau d'objets `IllegalRank` contenant le numéro du rang fautif et la comparaison des effectifs. |

---

## Pourquoi cette architecture ?

- **Détection Multiple** : La fonction peut identifier plusieurs rangs illégaux en un seul passage (ex: si le Rang 2 est trop large pour le Rang 1, ET que le Rang 3 est trop large pour le Rang 2).
- **Flexibilité** : Elle gère dynamiquement n'importe quel nombre de rangs, même si le jeu standard se limite généralement à 3 ou 4.
- **Support IA** : La même logique est appliquée à l'ordinateur, permettant à l'IA de vérifier la validité de ses propres déplacements avant de les confirmer.

---

## Résumé

Ce fichier garantit que :

- L'**intégrité tactique** des formations est maintenue.
- Le joueur reçoit des **données précises** pour corriger son placement.
- Le moteur de jeu peut **bloquer la fin d'un tour** tant que les rangs ne sont pas légaux.
