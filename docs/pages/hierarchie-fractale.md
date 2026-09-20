# Hiérarchie fractale

L'organisation des territoires est basée sur un système de poupées russes comptant 9 niveaux d'échelle, allant de l'échelle planétaire à l'échelle du voisinage immédiat.

Chaque territoire de ce système est un concept fondamental pour permettre la navigation dans la carte géographique de Gaïa Equation. Ce mécanisme permet de "zoomer" progressivement et de structurer le monde en entités logiques et gérables. Du coup, chaque territoire contient entre 6 et 18 sous-territoires contigus, représentant environ 1% à 3% de la population de leur parent.

## Structure et identifiants

L'arbre complet se découpe ainsi :
- L1 : Terre (1 entité)
- L2 : Sous-continent (12)
- L3 : Macro-régions (≈ 150 pays ou regroupements)
- L4 : Méso-régions
- ...
- L9 : Proches (environ 25 personnes)

Un système d'identifiants numériques à points maintient la hiérarchie. Par exemple, `1.2.2` correspond à la zone "France+CH" (un territoire L3), et ses enfants sont préfixés par cette base, comme `1.2.2.1` pour l'Île-de-France.

## Traitement des frontières

Le modèle fractal s'affranchit parfois des frontières politiques classiques s'il le juge nécessaire. Concrètement, le territoire `1.2.2` unit la France et la Suisse Romande. Résultat : l'application doit agréger des cartes indépendantes en un seul territoire continu via la technique du `union` GeoJSON.

## Table de référence des niveaux fractals

Le découpage théorique vise un dernier niveau de quelques dizaines d'individus. Un **niveau 10** est à prévoir pour prendre en considération le cumul des écarts afin que le dernier niveau soit de quelques dizaines d'individus. Total humain de **10 milliards à prévoir pour 2050**.

| Niveau fractale N | Dénomination        | Échelle de la fractale       | Population théorique moyenne 25 × 12^(3-N) | Écart mondial si écart population entre territoires 1 à 3 (voir 4) au sein de N-1 | Nombre moyen de territoires de fractales inférieures | Nombre théorique de fractales de ce niveau |
| ----------------- | ------------------- | ---------------------------- | ------------------------------------------ | --------------------------------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------ |
| **9**             | Proches             | Voisins immédiats            | 25                                         | 1 à 21                                                                            | 0                                                    | 500 millions                               |
| **8**             | Neighborg           | Quartier Voisinage           | 300                                        | 1 à 19                                                                            | 12                                                   | 55 millions                                |
| **7**             | Under local         | Voisinage - petit village    | 4 000                                      | 1 à 17                                                                            | 12                                                   | 3 millions                                 |
| **6**             | **Local community** | Quartier - campagne - Villes | 43 000                                     | 1 à 15                                                                            | 12                                                   | 250 mille                                  |
| **5**             | Micro regions       | Agglomérations - canton      | 500 000                                    | 1 à 13                                                                            | 12                                                   | 20 mille                                   |
| **4**             | Meso regions        | Métropole - département      | 6 millions                                 | 1 à 10                                                                            | 12                                                   | 2 mille                                    |
| **3**             | Macro **Regions**   | ± Pays                       | 75 millions                                | 1 à 7                                                                             | 12                                                   | 150                                        |
| **2**             | Subcontinent        | Région continentale          | 100 millions                               | 1 à 3 *(voir 4)*                                                                  | 12                                                   | 12                                         |
| **1**             | Mother Earth        | Monde                        | 10 milliard                                |                                                                                   | 12                                                   | 1                                          |

Un écart de **1 à 3** signifie ± 50 % à la moyenne / **1 à 4** signifie ± 60 % / **1 à 7** signifie ± 75 % / **1 à 13** signifie ± 85,7 % / **1 à 19** signifie ± 90 %.

## Règles fractales

- Chaque fractal représente entre **1 % et 3 %** de la population de son parent
- Chaque fractal contient entre **6 et 18 sous-fractales** (cible : 12)
- Les sous-fractales doivent être **contiguës géographiquement**
- Une fractal peut être **trans-frontalière** (ex. : Pays Basque = France + Espagne)
- Code de fractale : notation décimale (ex. : Europe = `1.2`, France = `1.2.2`, Paris = `1.2.2.4`)

## Où vit l'arbre dans le code

Aujourd'hui, l'arbre fractal et les sources géographiques sont définis **dans des modules
TypeScript**, pas dans des fichiers JSON chargés au runtime :

- `src/data/fractalTree.ts` — l'arbre complet (tableau plat de `FractalTerritory`), avec un index
  `Map<string, FractalTerritory>` pour les recherches O(1) et les helpers `getTerritory`,
  `getChildren`, `getParent`, `getBreadcrumb`, `getTerritoriesByLevel`.
- `src/data/geoSources.ts` — le registre des fichiers GeoJSON (`/public/geo/*.geojson`).
- `src/utils/fractalMap.ts` — la construction des couches cartographiques par niveau.

L'arbre réel couvre L1 (Terre) → L2 sous-continents → L3 macro-régions (Europe pour le MVP) → une
branche France de démonstration (Paris, Lyon, Marseille, Toulouse) jusqu'à des niveaux L8/L9
synthétiques pour Clignancourt.

> [!note] Rendre le modèle « malléable » (territoires définis en JSON externe chargé au runtime,
> `fractalMap.ts` rendu agnostique du niveau) est une cible de conception **non implémentée** —
> suivie dans `roadmap-status.md`.
