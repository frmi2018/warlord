# scoreCharge.ts

## Localisation

`src/ai/scoring/scoreCharge.ts`

## Rôle du fichier

Le fichier `scoreCharge` contient l'intelligence nécessaire pour utiliser l'une des cartes les plus tactiques du jeu : **Charge**. Puisqu'une carte Action est une ressource unique qui est défaussée après usage, l'IA ne doit pas l'utiliser au hasard.

Ce module évalue si avancer un combattant d'un rang vers l'avant (sans le dépenser) apporte un avantage stratégique suffisant pour justifier la perte de la carte.

---

## Philosophie du Scoring

L'IA calcule un score pour chaque combattant éligible sur le plateau. Le "désir" de jouer Charge est influencé par trois facteurs majeurs :

### 1. Le Gain de Temps (Économie d'Action)

Le plus gros bonus est accordé aux unités bloquées au **Rang 3**.

- **Rang 3 → Rang 2** : Énorme gain (**+80 points**). Cela permet à un combattant lourd de sauter deux tours de manœuvre normale.
- **Rang 2 → Rang 1** : Gain significatif (**+40 points**).

### 2. L'Opportunité Offensive

L'IA vérifie si le mouvement permet d'attaquer immédiatement.

- **Fighter "Ready"** : Si l'unité n'est pas épuisée, elle peut frapper dès la fin de la Charge (**+25 points**).
- **Menace sur le Warlord** : Si le mouvement amène le fighter à portée du Warlord adverse au Rang 1 (**+20 points**).

### 3. L'Analyse du Risque (Le "Front")

C'est ici que l'IA montre sa prudence. Arriver au premier rang est dangereux si le joueur a des unités prêtes à riposter.

- **Front Sûr** : Bonus si le rang 1 adverse est vide (**+30 points**).
- **Front Dangereux** : Malus sévère si le joueur a une forte pression d'attaque (**-40 à -60 points**). L'IA refuse de "charger dans la gueule du loup".

---

## Constantes et Seuils

Le système utilise des valeurs fixes pour équilibrer la prise de décision :

- **`MIN_SCORE_TO_PLAY` (35)** : L'IA ne jouera pas la carte si le score total est inférieur à ce seuil (défini dans la priorité appelante). Cela évite de gaspiller une Charge pour un gain marginal.
- **Pression Adverse** : Une pression est jugée "forte" à partir de **6** (risqué) et "dangereuse" à partir de **10** (suicidaire).

---

## Résumé Technique

- **`scoreCharge`** : Fonction principale qui filtre les cartes "Charge" en main et les "Fighters" éligibles sur le plateau.
- **`scoreChargeForFighter`** : Calcule le score individuel d'un candidat en additionnant bonus et pénalités.
- **`getEnemyFrontlinePressure`** : Somme les valeurs d'attaque des cartes "Ready" du joueur au Rang 1 pour évaluer le danger.
