# Documentation de cardValidator.ts

## Localisation

`src/engine/rules/cardValidator.ts`

## Rôle du fichier

Ce fichier sert de **garde-fou** pour l'intégrité des données du jeu. Il effectue une validation structurelle de la base de données des cartes (`cardDatabase`) au démarrage de l'application.

Son but est de détecter les erreurs de saisie (fichiers JSON mal remplis, propriétés oubliées) avant que celles-ci ne provoquent des plantages silencieux ou des comportements erratiques dans le moteur de jeu.

---

## Fonctionnement technique

### `validateCardDatabase(cards)`

La fonction parcourt l'intégralité du catalogue de cartes et vérifie la présence des données critiques selon le type de carte :

1.  **Champs communs** : Vérifie que chaque carte possède un `id` et un `name` valides et non vides.
2.  **Pour les Personnages (`character`)** :
    - Présence obligatoire des valeurs d'attaque (`attackValues`).
    - Présence de la classe d'armure (`armorClass`).
    - Vérification des points de vie (`hitPoints`) : ils doivent être supérieurs à 0.
3.  **Pour les Actions (`action`)** :
    - Vérifie que le tableau `actions[]` contient au moins une définition d'effet.

---

## Optimisation des performances

> [!NOTE]
> **Zéro coût en production** : Ce validateur est conçu pour être appelé uniquement en environnement de développement (**DEV**). Une fois le jeu compilé pour les joueurs, cette vérification est ignorée pour garantir un chargement instantané.

---

## Structure du Rapport de Validation

La fonction retourne un objet `ValidationReport` :

| Propriété | Type       | Description                                                                                           |
| :-------- | :--------- | :---------------------------------------------------------------------------------------------------- |
| `valid`   | `boolean`  | `true` si aucune erreur n'a été détectée.                                                             |
| `errors`  | `string[]` | Liste de messages détaillés incluant l'ID et le nom de la carte fautive pour faciliter la correction. |

---

## Pourquoi cette validation est-elle nécessaire ?

Dans un jeu de cartes, les données sont souvent nombreuses et complexes. Une simple faute de frappe dans un JSON (ex: `hp` au lieu de `hitPoints`) pourrait :

- Empêcher une unité de mourir.
- Faire planter le calcul des dégâts.
- Rendre une carte invisible sur le plateau.

Le `cardValidator` permet de **déboguer les données** aussi rigoureusement que le code.

---

## Résumé

Ce fichier garantit que :

- La **base de données des cartes** est saine et complète.
- Les développeurs reçoivent des **alertes claires** en cas d'oubli lors de la création d'une nouvelle carte.
- Le moteur de jeu peut manipuler les objets `Card` en toute confiance, sans vérifier la présence des propriétés à chaque étape.
