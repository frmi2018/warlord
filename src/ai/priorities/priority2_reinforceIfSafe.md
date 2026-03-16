# priority2_reinforceIfSafe.ts

## Localisation

`src/ai/priorities/priority2_reinforceIfSafe.ts`

## Rôle du fichier

La priorité **"Reinforce If Safe"** représente la phase de développement opportuniste de l'IA. Elle intervient avant le combat standard (P3) mais après les actions critiques (P0/P1). Son rôle est de permettre à l'IA de poser ses meilleures cartes ou d'équiper ses unités **uniquement si elle ne court aucun risque immédiat** et qu'aucune cible prioritaire ne mérite d'être attaquée d'abord.

C'est une stratégie de type "Profiter de l'accalmie" pour construire une armée imbattable.

---

## Conditions de Blocage (L'IA refuse de renforcer)

Avant de jouer une carte, l'IA vérifie si elle ne ferait pas mieux de garder ses unités prêtes pour le combat. Elle passe son tour sur cette règle (retourne `null`) si :

1.  **Menace directe** : Un personnage du joueur est "Ready" au premier rang. L'IA doit rester sur ses gardes pour l'étape d'attaque.
2.  **Cible juteuse** : Un personnage adverse est "Spent" mais possède plusieurs frappes (**multi-strike**) au rang 1, ET sa mort est sans danger tactique. L'IA préfère attaquer cette menace pour l'éliminer du plateau plutôt que de poser une nouvelle carte.

---

## Logique de Renforcement (La voie est libre)

Si aucune des menaces ci-dessus n'est détectée, l'IA consolide sa position selon trois étapes :

### 2a. Occupation du Front

Elle cherche en priorité des personnages de **niveau 1** (ceux qui peuvent légalement occuper le rang 1 vide). Elle choisit celui avec le plus haut niveau parmi les éligibles pour maximiser la solidité de sa première ligne.

### 2b. Puissance brute

Si le rang 1 est déjà occupé ou qu'elle n'a pas de niveau 1 en main, elle pose le personnage au **plus haut niveau** disponible dans sa main, quel que soit le rang de destination légal.

### 2c. Optimisation de l'équipement

En dernier lieu, si elle ne peut pas poser de personnage, elle cherche à équiper un objet sur une unité déjà présente au **rang 1**. Elle privilégie le rang 1 car c'est là que les bonus d'attaque et de classe d'armure (AC) ont le plus d'impact immédiat.

---

## Concepts Clés

### La menace "Multi-Strike Spent"

Une unité avec 2 ou 3 frappes est dangereuse même si elle est épuisée, car elle se redressera au tour suivant. L'IA considère que si elle peut tuer une telle unité "gratuitement" (sans créer de rang illégal dangereux), l'attaque est plus rentable que le déploiement.

### Sécurité du Déploiement

Contrairement à la P4 (_Reinforce Always_), cette règle est très sélective. Elle garantit que l'IA ne va pas bêtement poser une carte de faible niveau si elle a l'opportunité de détruire une pièce maîtresse du joueur.

---

## Résumé

Ce fichier est responsable de :

- **Détecter les fenêtres de tir** pour un développement sécurisé.
- **Prioriser l'attaque** contre les unités multi-strike adverses.
- **Maximiser le niveau** des troupes déployées.
- **Renforcer l'équipement** du premier rang quand aucune nouvelle troupe ne peut être posée.
