# Documentation de cardDefinition.ts

## Localisation

`src/types/cardDefinition.ts`

## Rôle du fichier

Ce fichier définit la **structure de vérité absolue** pour chaque carte du jeu. À partir du Sprint 1.3, il introduit une séparation nette entre les données statiques (nom, stats, faction) et la logique comportementale (effets, capacités spéciales).

### La Règle d'Or

Le système repose désormais sur une architecture orientée fichiers :

- **Une carte = Un fichier** unique (ex: `src/data/cards/dwarf/axe-fighter.ts`).
- Les fichiers globaux comme `cardDatabase.ts` deviennent des index **générés automatiquement** et ne doivent plus être édités manuellement.

---

## Structure : Fusion de la Forme et du Fond

L'interface `CardDefinition` étend l'interface de base pour y ajouter la couche mécanique :

### 1. Héritage de `DeckCard`

Elle conserve toutes les propriétés physiques et identitaires de la carte :

- Identifiants et visuels.
- Statistiques de combat (AC, ATK, HP).
- Type de carte et traits (Warlord, Undead, Elf, etc.).

### 2. Couche `effects` (Optionnelle)

C'est ici que réside l'intelligence de la carte.

- **Cartes "Vanilla"** : La majorité des troupes n'ont pas d'effets complexes. Le champ `effects` est simplement absent, ce qui optimise la mémoire et simplifie le traitement.
- **Cartes Spéciales** : Contient un tableau de `CardEffect`. Ces effets peuvent être **Passifs** (toujours actifs, ex: un bonus d'attaque aux voisins) ou **Actifs** (déclenchables par le joueur, ex: un Feat de soin).

---

## Flux de Données du Moteur

Le passage d'une définition de fichier à une carte jouable suit ce cycle :

1. **Définition** : Un développeur crée un fichier `.ts` respectant `CardDefinition`.
2. **Enregistrement** : Au démarrage, `effectRegistry.ts` scanne les définitions pour lier les fonctions logiques aux cartes.
3. **Instanciation** : En jeu, `createDeck` utilise ces définitions pour créer des objets `Card` (qui possèdent alors un état comme les PV restants ou l'orientation).

---

## Pourquoi cette architecture ?

| Avantage        | Description                                                                                                                                              |
| :-------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Modularité**  | Facilite le travail collaboratif : deux développeurs peuvent créer deux cartes différentes sans jamais causer de conflits dans un fichier central géant. |
| **Lisibilité**  | Tout ce qui concerne une carte (son texte, son image et son code de capacité) est regroupé au même endroit.                                              |
| **Performance** | Le moteur identifie immédiatement les cartes "simples" sans avoir à parser des tableaux d'effets vides.                                                  |

---

## Exemple de Structure

```typescript
// Exemple théorique dans src/data/cards/mercenary/healer.ts
export const HealerDefinition: CardDefinition = {
  id: "merc-01",
  name: "Prêtre Errant",
  cardType: "character",
  faction: "Mercenary",
  // ...stats...
  effects: [
    {
      id: "heal-action",
      type: "feat",
      name: "Soin Rapide",
      // Logique de l'effet...
    },
  ],
};
```
