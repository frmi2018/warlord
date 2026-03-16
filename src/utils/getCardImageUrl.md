# Documentation de getCardImageUrl.ts

## Localisation

`src/utils/getCardImageUrl.ts`

## Rôle du fichier

Ce fichier utilitaire est responsable de la **résolution des médias**. Son rôle est de transformer un identifiant de carte dynamique (utilisé par le moteur de jeu pour différencier les instances) en un chemin d'accès vers une ressource statique (le fichier image `.jpg`).

Il assure le pont entre la logique du moteur (qui manipule des IDs uniques) et le dossier des assets publics.

---

## Logique de Transformation

Le processus repose sur la distinction entre l'**ID d'instance** et l'**ID de base** :

1.  **ID d'instance** (ex: `p-sots-116-ab3f`) : Contient des préfixes (`p-` pour player) et des suffixes aléatoires pour permettre d'avoir plusieurs exemplaires de la même carte en jeu.
2.  **ID de base** (ex: `sots-116`) : L'identifiant unique du modèle de la carte dans la base de données.
3.  **Chemin final** : `/images/sots/sots-116.jpg`

### Fonctionnement technique

Le service utilise `extractBaseId()` issu du registre d'effets pour nettoyer l'identifiant et ne conserver que la racine technique.

---

## Gestion des Assets

### Convention de nommage

Pour que les images s'affichent correctement, les fichiers doivent être placés dans :
`public/images/sots/[baseId].jpg`

### Optimisation et Erreurs

- **Côté Client** : Le navigateur ne peut pas vérifier si un fichier existe sur le serveur sans tenter de le charger. La fonction retourne donc systématiquement un "chemin candidat".
- **Fallback** : Si l'image est manquante sur le serveur, le composant React qui appelle cette fonction doit implémenter une propriété `onError` pour afficher un visuel par défaut (dos de carte ou image de remplacement).

---

## Pourquoi cette approche ?

| Avantage         | Description                                                                                                       |
| :--------------- | :---------------------------------------------------------------------------------------------------------------- |
| **Légèreté**     | Évite de stocker l'URL complète dans chaque objet `Card` de la base de données, réduisant l'empreinte mémoire.    |
| **Maintenance**  | Si l'arborescence des images change (ex: passage au format `.webp`), il suffit de modifier cette unique fonction. |
| **Découplage** : | Le moteur de jeu n'a pas besoin de savoir où sont stockées les images ; il se contente de gérer la logique.       |

---

## Résumé

Ce fichier garantit que :

- Les **assets visuels** sont correctement liés aux données logiques.
- Le **nettoyage des identifiants** est centralisé et cohérent.
- L'application peut charger des **centaines d'illustrations** différentes via une règle de nommage simple.
