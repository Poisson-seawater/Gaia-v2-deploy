# KGI & Indicateurs

Les Key Gaïa Indicators (KGI) évaluent la consommation des ressources et le positionnement des territoires par rapport aux limites planétaires et aux besoins vitaux humains.

Pour que la carte ait du sens, l'utilisateur a besoin de repères universels. Du coup, plutôt que de n'afficher que des valeurs brutes (comme des mètres carrés ou des kWh), le système normalise ces valeurs pour chaque catégorie de besoin.

## Catégories couvertes

Le projet couvre **quatre catégories**, chacune sur les trois empreintes (Gaïa, énergie, carbone) :
1. **Logement (Housing)** : surface (m²), énergie, carbone.
2. **Mobilité (Mobility)** : kilomètres, énergie, carbone.
3. **Alimentation (Food)** : kcal, énergie, carbone.
4. **Achats (Purchases)** : unités, énergie, carbone.

La seule combinaison structurellement indisponible dans le code est **carbone × mobilité**
(donnée source non validée) ; les autres dépendent de la présence de la donnée pour l'année choisie.

## Le mécanisme de calcul

Le calcul de l'indicateur Gaïa repose sur la notion de "seuil vital" (le minimum requis pour une vie digne) et de "limite" (le maximum soutenable par la planète).
Concrètement, le score Gaïa s'obtient en divisant la consommation brute par la valeur de référence.

Par exemple : `Gaïa housing = surface_m2 / vital_surface`

Ces ratios alimentent un système de couleurs :
- **Bleu** : La consommation est en dessous du seuil vital.
- **Couleur de l'empreinte (normal)** : La consommation se situe entre le seuil vital et la limite soutenable.
- **Orange** : La consommation dépasse la limite maximale autorisée.

> [!note] Mobilité
> Le seuil vital de mobilité (`vital_mobility_km`) **est défini** : le calcul `km / vital_mobility_km`
> est opérationnel pour les empreintes Gaïa et énergie. Seule l'empreinte **carbone × mobilité**
> reste indisponible (donnée source non validée).

## Qui fixe les seuils et limites

Le **seuil vital** d'une fractale se fixe depuis `Espace Représentant`, uniquement sur les fractales
exactement attribuées au représentant. La **limite** planétaire (le maximum soutenable) se fixe soit
au niveau mondial par un admin (`Limites mondiales`), soit par fractale exacte via l'
[import CSV représentant](#/contribution), qui peut modifier une limite de fractale au même titre
qu'une donnée ou un seuil vital.

Un descendant sans valeur propre de seuil vital ou de limite hérite de celle du parent le plus proche
qui en a une. Cette lecture par héritage reste automatique ; elle ne donne aucun droit d'édition sur le
descendant — voir [Rôles & contributions](#/roles-et-contributions).
