# aiReactDecider.test.ts

## Localisation

`src/__tests__/aiReactDecider.test.ts`

## Rôle du fichier

Ce fichier de test assure le bon fonctionnement du "cerveau de réaction" de l'IA. Dans _Warlord_, une **Réaction** est une action spéciale qui peut être jouée en réponse à une autre action (par exemple, utiliser un talent de défense juste avant de subir une attaque).

Le but de ces tests est de vérifier que l'IA détecte correctement quand elle doit répondre, si elle a le droit de le faire, et comment elle traduit sa décision en une action compréhensible par le moteur de jeu.

---

## Fonctionnalités testées

### 1. Disponibilité de la Réponse (`shouldAiRespond`)

L'IA doit savoir s'il est temps pour elle de parler. Les tests vérifient :

- **La présence d'une fenêtre** : Si aucune action n'est en cours, l'IA ne doit pas tenter de réagir.
- **Le tour de parole** : Même si une attaque a lieu, l'IA ne répond que si le `currentReactor` est bien positionné sur `"ai"`. Si c'est au tour du joueur humain de réagir, l'IA reste silencieuse.

### 2. Prise de Décision (`decideAiReact`)

C'est ici que l'IA choisit quoi faire.

- **Sécurité** : Les tests confirment que si les conditions ne sont pas réunies, l'IA choisit par défaut de "Passer" (`reactPass`).
- **Stratégie v1** : Pour l'instant, la stratégie implémentée est volontairement conservatrice. Les tests valident que même si l'IA possède des talents puissants comme _Powerattack_, elle choisit systématiquement de passer son tour de réaction (pour éviter les bugs complexes en phase de développement initiale).

### 3. Construction de l'Action (`buildAiReactAction`)

Une fois la décision prise (ex: "Je veux utiliser ce talent"), elle doit être emballée dans un format que le moteur de jeu peut exécuter.

- **Conversion** : Vérifie que `reactPass` devient `aiReactPass`.
- **Génération de dés** : Pour les réactions qui nécessitent un jet de dé (Talents ou Cartes), le test vérifie qu'un nombre aléatoire entre 1 et 20 est bien généré et inclus dans l'action finale.

---

## Concepts de Test Clés

- **Mocking de l'État** : Utilisation de `makeTestState` pour créer des situations de jeu artificielles (une attaque en cours, des personnages spécifiques en jeu).
- **Expectations d'objets partiels** : Emploi de `expect.objectContaining` pour vérifier que l'action générée contient les bonnes informations (ID du personnage, nom du talent) tout en ignorant la valeur exacte du jet de dé qui est aléatoire.

---

## Résumé Technique

- **Moteur de test** : Vitest.
- **Composants testés** : `shouldAiRespond`, `decideAiReact`, `buildAiReactAction`.
- **Garantie** : Empêche l'IA de bloquer le jeu en ne répondant pas ou en tentant de répondre alors que ce n'est pas son tour.
