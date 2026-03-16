# Documentation de priorityChain.ts

## Localisation

`src/ai/priorities/priorityChain.ts`

## Rôle du fichier

`priorityChain.ts` définit la **colonne vertébrale stratégique** de l'IA. C'est ici que l'ordre des priorités est établi. Le système parcourt cette liste et s'arrête dès qu'une règle renvoie une action valide.

L'ordre est crucial car il définit la "personnalité" tactique de l'IA : privilégie-t-elle l'agression immédiate, le placement à long terme ou la préparation de combos ?

---

## Analyse de la Hiérarchie (6 Priorités)

L'IA suit cet entonnoir de décision pour déterminer son action :

### 0. Cartes Action (`playActionCard`)

**Pourquoi en premier ?** Une carte comme _Charge_ nécessite des conditions spécifiques (un combattant en rang 3, de l'espace libre devant lui). Si ces conditions sont réunies, c'est l'action la plus rentable car elle prépare une menace dévastatrice que le joueur ne pourra peut-être pas bloquer au tour suivant.

### 1. Assassinat (`attackWarlord`)

Si le Warlord adverse est vulnérable au premier rang, l'IA tente de finir la partie. C'est l'objectif de victoire direct.

### 2. Développement Sûr (`reinforceIfSafe`)

Si l'IA possède des cartes en main et que le joueur n'a aucune unité prête ("Ready") au rang 1 pour riposter, l'IA déploie ses troupes ou équipe ses objets. Elle profite de "l'accalmie" pour construire sa puissance.

### 3. Combat Standard (`attack`)

Si le plateau est stable, l'IA cherche à affaiblir les forces du joueur. Elle utilise son moteur de calcul pour trouver la cible la plus facile à toucher ou la plus gênante.

### 4. Développement sous Pression (`reinforceAlways`)

Même si le joueur menace ses lignes, l'IA finit par poser ses cartes en dernier recours. Il vaut mieux avoir un bloqueur sur le terrain, même vulnérable, que de laisser un rang vide.

### 5. Fin de Tour (`pass`)

La soupape de sécurité. Si aucune attaque n'est rentable et qu'aucune carte ne peut être jouée, l'IA passe la main.

---

## Architecture de la Chaîne

Chaque entrée de la chaîne est un objet `PriorityChainEntry` :

- **`rule`** : La fonction logique importée (ex: `attackPriority`) qui contient l'algorithme de décision spécifique.
- **`meta`** : Les informations descriptives. Le `priorityReason` est particulièrement important car il est affiché dans les logs ou les bulles d'aide pour que le développeur et le joueur comprennent la "pensée" de l'IA.

---

## Modularité

Cette structure permet une grande flexibilité :

- **Ajustement facile** : Pour rendre l'IA plus agressive, on pourrait déplacer `attackPriority` avant `reinforceIfSafe`.
- **Débogage** : On peut facilement isoler une règle ou en ajouter une nouvelle (ex: une priorité spécifique pour la gestion du deck) sans casser le reste du système.

---

## Résumé

Ce fichier est responsable de :

- **Définir l'ordre logique** des opérations de l'IA.
- **Équilibrer l'agression et le développement**.
- **Fournir le contexte textuel** des décisions (métadonnées).
- **Centraliser les imports** des différentes règles de priorité.
  """
