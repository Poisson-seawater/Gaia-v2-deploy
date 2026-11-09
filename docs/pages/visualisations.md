# Visualisations

Le site propose plusieurs lectures complémentaires d'une même donnée d'empreinte. Toutes partagent
les mêmes filtres (empreinte, catégorie, année) et la même résolution de seuils, et toutes sont
**implémentées** dans le code.

## Modes disponibles

L'explorateur bascule sur l'état `visualMode` (`cartes` / `1d` / `2d` / `3d`). À l'intérieur du mode
`cartes`, un sous-mode `mapDisplayMode` (`geographic` / `treemap`) choisit entre la carte et le
treemap.

| Mode | Composant | Description |
|---|---|---|
| **Carte géographique** | `MapChart.tsx` (D3-Geo) | Carte choroplèthe SVG, clic pour sélectionner, double-clic pour descendre d'un niveau, fil d'Ariane pour remonter. |
| **Treemap** | `TreemapChart.tsx` (D3-Hierarchy) | Rectangles dimensionnés par population, même donnée que la carte (sous-mode de `cartes`). |
| **1D — Benchmark** | `Benchmark1DChart.tsx` | Barres dont la largeur ∝ population ; trajectoires 1993 → 2050 avec l'année active mise en avant. |
| **2D — Isocourbes** | `Isocurve2DChart.tsx` | Plan d'isocourbes (**cas 1 uniquement** aujourd'hui, voir ci-dessous). |
| **3D** | `ThreeDChart.tsx` | Vue 3D en SVG du territoire sélectionné (mesure son propre conteneur). |
| **Colonne 3D fractale** | `ActiveRegion3DColumn.tsx` | Colonne 3D compacte de la fractale active, dans le menu de gauche. |

> Le bouton « ouvrir un débat » est **désactivé en mode 3D** (`canCreate={visualMode !== '3d'}`).

## Filtres communs

- **Empreinte** : Gaïa, Énergie, Carbone.
- **Catégorie** : Logement, Mobilité, Alimentation, Achats.
- **Année** : 1993, 2023, 2050 en base, plus toute année entre 1900 et 2100 publiée par un
  [import CSV représentant](#/contribution). La liste se met à jour dès qu'une publication introduit
  une nouvelle année.

La seule combinaison structurellement bloquée est **carbone × mobilité** (cf.
[KGI & Indicateurs](#/kgi-et-indicateurs)).

## Mode Carte

Carte choroplèthe centrée sur l'Europe, avec descente fractale (pays → régions → départements →
arrondissements → quartiers, branche France de démonstration). Quand aucune géométrie réelle
n'existe à bas niveau, l'app génère des **polygones de grille synthétiques** pour permettre la
descente. La logique de couches vit dans `src/utils/fractalMap.ts`.

## Mode 1D — Benchmark

**Lecture** : un bloc par territoire du niveau affiché ; largeur ∝ population ; la valeur d'empreinte
est positionnée par rapport au seuil vital (trait) et à la plage des sous-fractales. Les années
1993 / 2023 / 2050 permettent de lire une trajectoire.

## Mode 2D — Isocourbes (cas 1)

Le mode 2D affiche un fond d'**isocourbes** (entières en traits pleins, demi-entières en pointillés)
avec l'isocourbe vitale en bleu et l'isocourbe limite en rouge.

- **Cas 1 (implémenté)** : ordonnée = empreinte ressource, isocourbe énergie, abscisse =
  performance (E/R). Disponible pour le type **énergie hors mobilité** (cf.
  `src/utils/isocurve2d.ts`).
- Les autres cas (intensité carbone C/E, facteur d'émission C/R) **ne sont pas encore construits** —
  point ouvert suivi dans `roadmap-status.md`.

## Palette de couleurs

La coloration des territoires suit `src/constants.ts` (détails dans
[Charte graphique](#/charte-graphique)) :

| État / empreinte | Couleur | Hex |
|---|---|---|
| Sous le vital | Bleu | `#3b82f6` |
| Au-dessus de la limite | Rouge | `#dc2626` |
| Gaïa (en plage normale) | Violet | `#800080` |
| Énergie (en plage normale) | Marron | `#a65e2e` |
| Carbone (en plage normale) | Gris | `#6c6960` |
