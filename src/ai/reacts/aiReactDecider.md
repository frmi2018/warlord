# Documentation de aiReactDecider.ts

## Localisation

`src/ai/reacts/aiReactDecider.ts`

## Rôle du fichier

Le `aiReactDecider` est le module responsable de la **réflexion éclair** de l'IA. Dans Warlord, le flux de jeu peut être interrompu par des fenêtres de réaction (ex: "L'adversaire attaque, voulez-vous jouer une parade ?"). Ce fichier contient la logique pure qui permet à l'IA de décider si elle doit utiliser une capacité de réaction ou passer son tour.

Contrairement aux décisions de phase (gérées par `aiDecide`), ce module opère dans un contexte de "micro-décision" où le temps est suspendu par une `reactWindow`.

---

## Architecture de Décision

Le processus se divise en trois étapes distinctes pour garantir un code testable et sans effets de bord :

### 1. Détection (`shouldAiRespond`)

Une fonction utilitaire simple qui vérifie si une fenêtre de réaction est ouverte et si c'est explicitement le tour de l'IA de répondre. Cela évite au système de faire tourner des calculs inutiles quand c'est au joueur humain de réfléchir.

### 2. Réflexion (`decideAiReact`)

C'est le cœur du module.

- **Analyse du contexte** : Elle récupère l'action qui a déclenché la fenêtre (attaque, sort, etc.) et le timing actuel (_Before_, _After_).
- **Inventaire** : Elle appelle `getAvailableReacts` pour lister toutes les cartes de l'IA capables de réagir à cet instant précis.
- **Choix** : Actuellement, le code est en phase de transition. La version finale "scorera" chaque réaction pour choisir la meilleure, tandis que la version actuelle (temporaire) teste l'exécution automatique du premier React disponible pour valider l'UI.

### 3. Transformation (`buildAiReactAction`)

Une fois la décision prise (Passer ou Réagir), elle doit être transformée en une `GameAction` compréhensible par le moteur.

- **Court-circuit de validation** : Elle génère des actions de type `aiReact` ou `aiReactPass`. Ces actions ignorent les vérifications habituelles réservées aux humains (qui vérifient si `currentReactor === "player"`).
- **Déterminisme (Roll)** : Si l'IA décide de réagir, **le jet de dé (d20) est généré à cet instant précis** et inclus dans l'action. Cela garantit que si le jeu devient multijoueur, l'IA envoie le même résultat de dé à tous les participants, évitant toute désynchronisation.

---

## Pourquoi des actions spécifiques "AI" ?

Le fichier souligne une distinction technique cruciale :

- **`reactPass` (Joueur)** : Le moteur vérifie que c'est bien l'humain qui clique.
- **`aiReactPass` (IA)** : Le moteur sait que c'est une décision système et ne bloque pas l'action sous prétexte que "le curseur du joueur n'a pas bougé".

---

## Évolutions Futures

Le module est conçu pour accueillir une logique de "Scoring" :

1.  **Identifier** les Reacts (ex: _Power Attack_).
2.  **Évaluer** le gain (ex: "Est-ce que ces +2 dégâts vont tuer la cible ?").
3.  **Décider** : Si le gain est supérieur à un certain seuil, l'IA consomme sa réaction.

---

## Résumé

Ce fichier est responsable de :

- **Analyser les opportunités** de réaction pour l'IA.
- **Garantir l'intégrité du réseau** en incluant les jets de dés dans l'action dispatchée.
- **Fluidifier le flux de jeu** en répondant automatiquement aux interruptions du moteur.
- **Séparer la logique métier** (décision) de la logique technique (dispatch/validation).
