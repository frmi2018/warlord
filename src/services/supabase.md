# Documentation de supabase.ts

## Localisation

`src/services/supabase.ts`

## Rôle du fichier

Ce fichier est le **cœur de l'infrastructure de données** de l'application. Il initialise et exporte l'instance unique du client Supabase, permettant à tous les autres services (`deckService`, `authService`, etc.) de communiquer avec la base de données, l'authentification et les fonctions temps réel.

En centralisant l'instanciation, on garantit que l'application utilise une seule connexion persistante, optimisant ainsi les performances et la cohérence de l'état de session.

---

## Configuration et Sécurité

Le client est configuré via des variables d'environnement pour respecter les bonnes pratiques de sécurité :

- **`VITE_SUPABASE_URL`** : L'URL de l'API de votre projet Supabase.
- **`VITE_SUPABASE_ANON_KEY`** : La clé "anon" publique. Elle permet d'interagir avec la base de données tout en respectant les politiques de sécurité (RLS) définies côté serveur.

> [!CAUTION]
> Ces variables doivent être définies dans un fichier `.env.local` à la racine du projet. Ce fichier ne doit **jamais** être commité sur un dépôt public (GitHub/GitLab) pour éviter l'exposition de votre infrastructure.

---

## Mécanisme de Protection

Le script inclut une barrière de sécurité à l'initialisation :

```typescript
if (!supabaseUrl || !supabaseAnonKey) {
  throw new Error("[Supabase] Variables manquantes...");
}
```
