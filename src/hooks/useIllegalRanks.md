# Documentation de useIllegalRanks.ts

## Localisation

`src/hooks/useIllegalRanks.ts`

## Rôle du fichier

Ce hook est le **gardien de l'intégrité structurelle** du plateau. Dans Warlord, la formation des armées obéit à une règle de physique stricte : un personnage ne peut pas "flotter" dans un rang arrière s'il y a un trou devant lui. S'il n'est pas soutenu par un personnage au rang inférieur (plus proche de la ligne de front), le rang devient **illégal**.

Le hook surveille en permanence cette condition pour le joueur et l'IA, bloque le déroulement du jeu en cas d'anomalie, et expose la mécanique de **"Fall Forward"** (Chute vers l'avant) pour corriger la situation.

---

## La Règle du Soutien (Fall Forward)

Le concept de "Rangs Illégaux" survient généralement après un combat ou un effet de sort qui détruit une unité en première ou deuxième ligne.

- **Condition d'illégalité** : Un rang $N$ est illégal s'il contient des personnages alors que le rang $N-1$ possède des emplacements (slots) vides qui pourraient les accueillir.
- **Conséquence** : Tant que le plateau est illégal, aucune action de phase (attaque, sort, manœuvre) n'est autorisée. Le joueur doit d'abord "réparer" sa formation.

---

## Fonctionnement du Hook

### 1. Surveillance Passive (`useMemo`)

Le hook utilise `checkIllegalRanks` pour analyser les tableaux `playerCards` et `aiCards`. Grâce à `useMemo`, cette analyse lourde n'est relancée que si le nombre ou la position des cartes change.
Il retourne un objet contenant :

- `hasIllegalRanks` : Un booléen (le signal d'alerte).
- `illegalRanks` : La liste des indices de rangs problématiques.

### 2. Blocage du Gameplay (`isActionBlocked`)

Cette propriété est cruciale pour l'UI. Elle centralise l'état d'urgence :

- Si le joueur a un rang vide devant une unité : `isActionBlocked = true`.
- Si l'IA a un rang vide devant une unité : `isActionBlocked = true`.
  L'interface utilise cette valeur pour désactiver les boutons "Attaquer" ou "Finir le tour".

### 3. Résolution (`fallForwardPlayer` / `fallForwardAi`)

Ces fonctions permettent de corriger le problème.

- Le joueur clique sur une unité "flottante" via la `IllegalRanksBanner`.
- Le hook dispatch une action `fallForward`.
- L'unité avance d'un rang, le son de mouvement est joué, et le hook réévalue immédiatement la légalité.

---

## Interaction avec l'Interface

Le hook est conçu pour alimenter un composant spécifique : **`IllegalRanksBanner`**.
Cette bannière n'apparaît que lorsque `playerIllegalRanks.hasIllegalRanks` est vrai. Elle guide le joueur en lui indiquant visuellement quelles unités doivent être avancées pour débloquer la partie.

---

## Résumé

Ce fichier est responsable de :

- **Détecter les trous** dans la formation de combat.
- **Forcer la résolution** des problèmes de placement avant de continuer le jeu.
- **Exposer la logique de mouvement correctif** (Fall Forward).
- **Garantir la stabilité du moteur** en empêchant des actions illégales sur un plateau fragmenté.
  """
