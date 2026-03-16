# Documentation de gameEvents.ts

content = """# gameEvents.ts

## Localisation

`src/engine/events/gameEvents.ts`

## Rôle du fichier

Ce fichier définit la **liste exhaustive et la structure de tous les événements** qui peuvent survenir durant une partie.

Il utilise une "union discriminée", une technique TypeScript qui permet au moteur de jeu de savoir exactement quelles données sont disponibles pour chaque type d'événement (ex: un événement de mort contient la carte qui meurt, alors qu'un changement de phase contient les phases de départ et d'arrivée).

---

## Concept : L'Union Discriminée

Le type `GameEvent` regroupe plusieurs structures différentes partageant un champ commun : `type`.

- **Sérialisation** : Tous les événements sont composés de données "plates" (strings, nombres, objets Card). Cela permet de les enregistrer ou de les transmettre facilement.
- **Réactivité** : Ces événements sont envoyés au `EventBus` pour déclencher les capacités spéciales des cartes (ex: "Quand un personnage meurt, piochez une carte").

---

## Événements principaux

| Type d'événement          | Description                                | Données incluses                             |
| :------------------------ | :----------------------------------------- | :------------------------------------------- |
| `before_attack`           | Juste avant qu'une attaque ne soit lancée. | Attaquant, Cible.                            |
| `after_attack`            | Une fois l'attaque résolue.                | Attaquant, Cible, score du dé, succès/échec. |
| `unit_died`               | Un personnage est retiré du jeu (0 PV).    | Carte, propriétaire, tueur (optionnel).      |
| `turn_start` / `turn_end` | Début ou fin de tour d'un joueur.          | Propriétaire (joueur/IA), numéro du tour.    |
| `card_played`             | Une carte action a été jouée.              | Carte jouée, lanceur, propriétaire.          |
| `warlord_wounded`         | Le seigneur de guerre a subi des dégâts.   | Carte, dégâts subis, propriétaire.           |

---

## Variables et Types importants

| Élément     | Description                                                         |
| :---------- | :------------------------------------------------------------------ |
| `GameEvent` | Le type principal regroupant tous les événements possibles.         |
| `GamePhase` | Type importé définissant les étapes du tour (Ready, Actions, etc.). |
| `performer` | La carte qui effectue l'action (différente de la carte jouée).      |

---

## Interaction avec d'autres fichiers

Ce fichier est la **définition de référence** pour :

- `eventBus.ts` → Qui utilise ce type pour distribuer les événements aux abonnés.
- **Effets de Cartes** → Chaque effet de carte (ex: un bonus de force lors d'une attaque) se base sur ces définitions pour savoir à quoi il réagit.

---

## Résumé

Ce fichier est le **dictionnaire des actions du jeu**. Il :

- Standardise la communication entre le moteur central et les extensions (effets de cartes).
- Garantit la sécurité du code en définissant précisément quelles données sont liées à quelle situation.
- Permet une maintenance facile : pour ajouter un nouvel événement dans le jeu, on l'ajoute simplement à cette liste.
  """

with open("/mnt/data/gameEvents.md", "w") as f:
f.write(content)
