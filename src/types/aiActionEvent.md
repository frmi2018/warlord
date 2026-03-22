# Documentation de aiActionEvent.ts

## Localisation

`src/types/aiActionEvent.ts`

## Rôle du fichier

Ce fichier définit les types d'événements utilisés pour **communiquer les actions de l'IA à l'interface utilisateur**. Ces événements alimentent notamment le "Log de combat" et les notifications visuelles (Toasts).

---

## Structure des Événements

Chaque événement de l'IA inclut des métadonnées de debug (`AiDebugMeta`) pour aider les développeurs à comprendre pourquoi l'IA a pris une décision :

- **`priorityNumber`** : Le score attribué à l'action.
- **`priorityReason`** : L'explication textuelle du choix.

---

## Types d'Événements Gérés

- **`playCard`** : Quand l'IA déploie une unité.
- **`attack`** : Détaille qui attaque qui, avec le log complet du combat.
- **`maneuver`** : Déplacement d'un rang à un autre.
- **`playActionCard`** : Utilisation d'une carte d'action tactique (ex: Charge).

---

## Résumé

Le `aiActionEvent` est responsable de :

- **Traduire les décisions mathématiques** de l'IA en messages lisibles par l'humain.
- **Fournir les données nécessaires** à l'affichage des animations et des logs.
- **Faciliter le débogage** du comportement de l'IA via les raisons de priorité.
