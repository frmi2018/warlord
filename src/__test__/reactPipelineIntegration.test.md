# reactPipelineIntegration.test.ts

## Localisation

`src/__tests__/reactPipelineIntegration.test.ts` (Ajout du Scénario E)

## Rôle du fichier

Ce test d'intégration simule un **flux de combat complet de l'IA**, de la déclaration de l'attaque jusqu'à l'application des dégâts finaux. Il valide spécifiquement la correction de bugs critiques liés à la symétrie entre le joueur humain et l'Intelligence Artificielle.

Il garantit que le pipeline de réactions fonctionne aussi bien pour les actions automatiques de l'ordinateur (`aiAttack`) que pour les actions manuelles du joueur.

---

## Scénario E : aiAttack + Powerattack IA

Le test suit le personnage **Torben** (IA) qui utilise son talent **Powerattack** contre un joueur. Ce scénario est crucial car il valide deux corrections majeures :

### 1. Ouverture de la fenêtre "Before" (Bug #4)

- **Problème précédent** : L'action `aiAttack` était considérée comme une action interne, ce qui empêchait l'ouverture des fenêtres de réaction. L'IA ne pouvait donc jamais utiliser de talents "avant" ses propres attaques.
- **Validation** : Le test vérifie que dès que l'IA lance `aiAttack`, le jeu se "gèle" et ouvre une fenêtre de réaction permettant l'usage de talents.

### 2. Gestion des cartes IA (Bug #3)

- **Problème précédent** : Le moteur cherchait systématiquement l'attaquant dans le deck du joueur (`playerCards`). Comme l'attaquant était une carte IA, il n'était jamais trouvé et restait donc prêt (`ready`) indéfiniment après avoir attaqué.
- **Validation** : Le test confirme qu'après la résolution, Torben passe bien en mode "épuisé" (`spent`) dans la liste des cartes de l'IA (`aiCards`).

---

## Déroulement technique du test

| Étape                | Action                  | État attendu                                                       |
| :------------------- | :---------------------- | :----------------------------------------------------------------- |
| **1. Déclenchement** | `aiAttack`              | Fenêtre "Before" ouverte. État gelé. Torben est encore `ready`.    |
| **2. Réaction IA**   | `aiReact` (Powerattack) | Succès du jet de dé. `pendingAttackBonus` stocké (+1 dégât).       |
| **3. Résolution**    | `resolvePending`        | L'attaque est exécutée. Torben devient `spent`.                    |
| **4. Application**   | `resolveAttack`         | La cible reçoit **2 blessures** (1 normale + 1 bonus Powerattack). |

---

## Concepts Clés vérifiés

- **Isolation des effets** : Le bonus de dégâts n'est appliqué que si l'attaque touche réellement.
- **Nettoyage du State** : À la fin de la chaîne, tous les bonus temporaires et les actions en attente sont purgés pour éviter les bugs au tour suivant.
- **Respect de l'Initiative** : Même lors d'une attaque IA, le joueur humain (s'il a l'initiative) a l'opportunité de passer ou de réagir en premier.

---
