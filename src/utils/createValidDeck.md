# Documentation de createValidDeck.ts

## Localisation

`src/utils/createValidDeck.ts`

## Rôle du fichier

Ce fichier sert de **garde-fou (Wrapper)** autour du processus de création de deck. La génération d'une armée peut parfois échouer si le tirage aléatoire épuise prématurément un pool de cartes restreint (par exemple, si trop de cartes uniques sont tirées d'un coup).

`createValidDeck` apporte une couche de résilience en implémentant une logique de **tentatives multiples (retry)** pour garantir que l'utilisateur ou l'IA reçoive toujours un deck complet et jouable.

---

## Logique de Fonctionnement

### 1. Normalisation des entrées

Le script commence par nettoyer les paramètres. Si un `warlordCardId` est passé sous forme de chaîne vide (`""`), il est converti en `undefined`. Cela permet une transition fluide entre les menus de sélection (où "Aléatoire" peut être une valeur vide) et le moteur de création.

### 2. Stratégies de Génération

#### A. Mode Déterministe (ID spécifié)

Si un ID de Warlord précis est fourni :

- Le moteur considère que le choix est intentionnel.
- Il n'effectue **aucun retry**. Si le deck ne peut pas être construit avec ce leader (pool insuffisant), il lève une erreur immédiatement (**Fail-Fast**).

#### B. Mode Aléatoire (ou par Faction)

Si le Warlord doit être tiré au hasard :

- Le script entre dans une boucle de tentatives (par défaut **10 essais**).
- En cas d'erreur de construction (conflit de cartes uniques ou manque de troupes de niveau 1), le script intercepte l'erreur (`catch`) et relance instantanément un nouveau tirage.
- Si après 10 tentatives aucun deck n'est valide, une erreur finale est levée.

---

## Pourquoi cette architecture ?

| Avantage          | Description                                                                                                                          |
| :---------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| **Robustesse UX** | Évite que l'application ne plante si un tirage aléatoire malchanceux rend la formation impossible.                                   |
| **Abstraction**   | L'interface utilisateur n'a pas à se soucier de la complexité du tirage ; elle appelle une fonction "garantie".                      |
| **Optimisation**  | Ne tente des retries que lorsque cela a un sens (en mode aléatoire), économisant des cycles de calcul inutiles en mode déterministe. |

---

## Paramètres de la fonction

- `cards` : La base de données complète des cartes.
- `owner` : "player" ou "ia" (détermine les préfixes d'ID).
- `warlordCardId` : (Optionnel) ID spécifique pour forcer un leader.
- `maxTries` : Nombre de tentatives avant abandon (défaut : 10).
- `faction` : (Optionnel) Restreint le tirage aléatoire à une faction précise.

---

## Résumé

Ce fichier assure la **stabilité du lancement de partie**. C'est le point d'entrée sécurisé pour s'assurer que chaque camp dispose d'une armée valide, quelles que soient les contraintes de rareté ou de niveau des cartes disponibles.
