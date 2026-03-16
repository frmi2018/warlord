# Documentation de applySpendAttacker.ts

## Localisation

`src/engine/actions/handlers/applySpendAttacker.ts`

## Rôle du fichier

Ce fichier gère la **phase de déclaration d'attaque** pour le joueur. Sa mission principale est de déterminer si un personnage doit être "épuisé" (passer en état `spent`) au moment où il initie son offensive.

Il s'agit d'une étape de pré-combat cruciale qui applique les limitations de ressources physiques des unités avant que les dés ne soient lancés.

---

## Concept : La Consommation d'Action

Dans ce moteur, attaquer n'entraîne pas systématiquement l'épuisement immédiat de la carte. Trois facteurs influencent cette décision :

1.  **Multi-Strike** : Si l'unité possède plusieurs valeurs d'attaque (ex: `attackValues: [4, 5]`), elle peut frapper plusieurs fois avant de s'épuiser.
2.  **Riposte** : Une contre-attaque (réaction) ne consomme pas l'action principale du personnage.
3.  **Orientation Standard** : Par défaut, une attaque simple fait basculer la carte de `ready` à `spent`.

---

## Logique métier

### La règle du `shouldBecomeSpent`

Le handler calcule le changement d'état selon la logique suivante :

- **OUI** : Si l'unité a une seule frappe (`!isMultiStrike`) ET que ce n'est pas une riposte (`!isRiposte`).
- **NON** : Si l'unité est en train de riposter ou si elle dispose encore de frappes en réserve.

### Nettoyage de l'état

Lorsqu'une attaque est validée et dépensée, le handler réinitialise également `canceledActionId: null`. Cela garantit qu'aucune trace d'une action précédemment annulée (via _Stealth_ par exemple) ne vienne perturber le nouveau flux de combat.

---

## Fonctions principales

### `applySpendAttacker(state, action): GameState`

- **Validation** : Utilise le Type Guard `isCharacter` pour s'assurer que l'entité qui attaque possède bien des statistiques de combat.
- **Immuabilité** : Utilise `.map()` pour mettre à jour la collection `playerCards`. Seule l'unité attaquante voit sa référence d'objet modifiée.
- **Sécurité** : Si les conditions d'épuisement ne sont pas remplies, la fonction retourne l'état `state` original sans aucune allocation mémoire inutile.

---

## Variables et Types importants

| Élément         | Description                                                             |
| :-------------- | :---------------------------------------------------------------------- |
| `isMultiStrike` | Déterminé par la longueur du tableau `attackValues`.                    |
| `isRiposte`     | Extrait de l'action de combat pour protéger l'état de la carte.         |
| `spent`         | L'état final qui empêche la carte d'agir à nouveau (sauf redressement). |

---

## Interaction avec d'autres fichiers

Ce handler est le point de départ de la séquence de combat :

- `combatReducer.ts` : Appelle ce handler dès que le joueur confirme son intention d'attaquer.
- `cardGuards.ts` : Fournit la logique de vérification de type `isCharacter`.
- `applyResolveAttack.ts` : Prendra le relai une fois que l'attaquant aura été "dépensé" pour calculer les dégâts.

---

## Résumé

Ce fichier est responsable de :

- **Gérer le coût en action** du combat.
- **Respecter les capacités spéciales** de multi-frappe.
- **Protéger les unités** effectuant des ripostes contre l'épuisement involontaire.
- **Maintenir la fluidité** de l'état de jeu en nettoyant les anciennes actions annulées.
  """
