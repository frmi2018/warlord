# Documentation de GamePage.tsx

## Localisation

`src/pages/GamePage.tsx`

## Rôle du fichier

`GamePage` est le composant parent qui encapsule l'expérience de jeu. Sa fonction principale est de gérer le **cycle de vie des sessions de combat**.

Il sert de "conteneur de nettoyage" : il s'assure que chaque nouvelle partie commence sur une base totalement vierge, sans résidus de l'état ou des variables de la partie précédente.

---

## Le mécanisme de la `gameKey`

La particularité technique de ce composant réside dans l'utilisation de la prop `key` sur le composant `<GameBoard />`.

```tsx
<GameBoard key={gameKey} ... />
```
