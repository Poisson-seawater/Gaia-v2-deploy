# Règles & variables

Les principaux mécanismes du projet reposent sur la navigation à travers la hiérarchie fractale et le calcul des moyennes pondérées par la population pour faire remonter l'information locale vers le global.

La boucle en une phrase : L'interface interroge le référentiel géographique pour obtenir les formes des territoires, récupère leurs données de consommation en fonction du niveau fractal affiché, pondère les résultats en utilisant la population, puis affiche des indicateurs colorés pour chaque zone géographique.

## Commandes et actions

| Action | Effet | Exemple |
|--------|-------|---------|
| Clic sur un territoire | Charge les sous-territoires enfants et effectue le calcul d'agrégation. | Clic sur "Europe" (L2) affiche "France+CH" (L3). |
| Bascule d'année | Met à jour les calculs de [KGI & Indicateurs](#/kgi-et-indicateurs) avec les données de l'année sélectionnée. | Passer de 1993 à 2023. |
| Zoom arrière (Breadcrumb) | Remonte dans la [Hiérarchie fractale](#/hierarchie-fractale) vers les territoires parents. | Retourner au niveau mondial. |

## Variables et options

| Nom | Valeurs possibles | Rôle |
|-----|-------------------|------|
| Niveau fractal | `L1` (Terre) à `L9` (Proches) | Détermine la précision de la carte et le type de [Sources de données](#/sources-de-donnees) interrogées. |
| Année | `1993`, `2023`, `2050` en base, plus toute année `1900`–`2100` publiée par un [import CSV représentant](#/contribution) | Fixe le contexte temporel des métriques. La liste d'années proposée dans les filtres se recalcule dynamiquement à chaque nouvelle publication. |
| Catégorie de besoin | `Logement`, `Mobilité`, `Alimentation`, `Achats` | Cible le type de ressources consommé pour le calcul des indicateurs. |
| Empreinte | `Gaïa`, `Énergie`, `Carbone` | Choisit l'unité de lecture de la consommation. |

## Comment ça interagit

Les **données de consommation** brutes sont séparées conceptuellement de la **carte géographique**. Lors d'un clic, le système combine le GeoJSON statique (pour l'affichage) avec les données de [Sources de données](#/sources-de-donnees) (pour les calculs).

La remontée de données depuis le niveau le plus fin (`L9`) jusqu'au niveau le plus large (`L1`) se calcule par une moyenne pondérée par la population : `Σ(valeur × pop) / Σ(pop)`. Cela signifie que l'empreinte d'une région n'est pas qu'une simple moyenne géographique, mais reflète fidèlement le poids démographique des zones concernées.
