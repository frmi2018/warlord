# Documentation de IllegalRanksBanner.tsx

## Localisation

`src/components/IllegalRanksBanner.tsx`

## Rôle du fichier

Dans _Warlord_, une formation est "illégale" si un rang contient plus de personnages que le rang situé immédiatement devant lui. Ce composant est une **interruption forcée** (Modal blocking) qui empêche toute progression tant que la structure de l'armée n'est pas corrigée.

Il guide l'utilisateur à travers la procédure de "Fall Forward" (chute en avant), une mécanique de jeu où les unités avancent pour combler les vides, mais au prix d'une perte d'efficacité (changement d'orientation).

---

## Logique de Fonctionnement

Le composant s'active uniquement si `illegalRanksInfo.hasIllegalRanks` est vrai.

### 1. Filtrage Sélectif

Le banner ne permet pas d'interagir avec n'importe quelle carte. Il calcule une liste de `selectableCards` basée uniquement sur les numéros de rangs identifiés comme problématiques par le moteur de règles.

### 2. Mécanique du "Fall Forward"

Le composant rappelle visuellement les conséquences de l'avance forcée :

- **Ready → Spent** : L'unité s'épuise en avançant.
- **Spent → Stunned** : L'unité est désorientée par la précipitation.
- **Stunned → Avance sans changement** : L'unité était déjà hors de combat, elle ne fait qu'avancer.

---

## Anatomie de l'Interface

Le design utilise des codes visuels d'urgence pour signaler au joueur qu'une action corrective est requise :

| Élément             | Style / Effet                     | Intention                                                          |
| :------------------ | :-------------------------------- | :----------------------------------------------------------------- |
| **Overlay**         | Fond sombre (`rgba(0,0,0,0.75)`)  | Isole le joueur du reste du plateau pour focaliser l'attention.    |
| **Bordure**         | Rouge vif (`#c0392b`) avec lueur  | Indique une erreur critique de règles.                             |
| **Liste de Cartes** | Boutons avec flèches vertes (`⬆`) | Rend l'action corrective (avancer) intuitive et simple à exécuter. |

---

## Pourquoi cette architecture ?

| Avantage                      | Description                                                                                                                                                                              |
| :---------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Prévention des Soft-locks** | En bloquant le jeu via un `zIndex: 1000`, on s'assure que le joueur ne peut pas attaquer ou piocher tant que sa formation est invalide, respectant strictement les règles du jeu papier. |
| **Pédagogie intégrée**        | Le banner explique _pourquoi_ le rang est illégal (ex: "Le Rang 2 a 3 unités alors que le Rang 1 n'en a que 2"), évitant la frustration du joueur.                                       |
| **Compatibilité IA**          | Bien que principalement destiné au joueur, le prop `owner` permet d'utiliser ce même composant pour surveiller ou debugger les erreurs de placement de l'IA.                             |

---

## Résumé

`IllegalRanksBanner.tsx` agit comme un **arbitre digital**. Il transforme une règle de placement complexe en une interface de résolution simple et assistée, garantissant que la partie se déroule toujours dans un cadre légal selon les mécanismes de _Warlord: Saga of the Storm_.
