# Sources de données

Cette page décrit **d'où viennent réellement les chiffres affichés** sur la carte et les
graphiques aujourd'hui, et comment un admin ou un responsable les met à jour.

> [!note] Les données affichées **ne viennent pas de WikiData**. Aucune requête SPARQL n'est faite
> par le site. Les pistes d'alimentation communautaire (WikiData / SPARQL, branchement du pipeline
> d'ingestion) sont des sujets ouverts, suivis dans `roadmap-status.md` — pas dans le code actuel.

## Les 12 métriques, concrètement

Chaque territoire, pour une année donnée, porte **12 chiffres** : **4 catégories × 3 empreintes**.

| | Empreinte **Gaïa** (unité propre) | Empreinte **Énergie** (kWh/pers/an) | Empreinte **Carbone** (tCO₂/pers/an) |
|---|---|---|---|
| **Logement** | surface m²/pers | énergie kWh | carbone tCO₂ |
| **Mobilité** | km/pers/an | énergie kWh | carbone tCO₂ *(non disponible)* |
| **Alimentation** | kcal/pers/an | énergie kWh | carbone tCO₂ |
| **Achats** | unités/pers/an | énergie kWh | carbone tCO₂ |

L'empreinte Gaïa d'une catégorie est un **ratio** : la consommation divisée par son seuil vital
(voir [KGI & Indicateurs](#/kgi-et-indicateurs)). La seule combinaison structurellement bloquée
dans le code est **carbone × mobilité** (donnée source non validée) ; les autres dépendent de la
présence de la donnée pour l'année choisie.

Le « total » affiché n'est **pas une 13ᵉ donnée stockée** : il est recalculé à la volée. De même,
les niveaux supérieurs (L1–L8) ne sont pas saisis — ils sont **agrégés** depuis leurs enfants.

## L'agrégation ascendante

Les niveaux supérieurs ne sont pas saisis directement : l'application calcule leur empreinte par
**moyenne pondérée par la population** des enfants : `Σ(valeur × pop) / Σ(pop)`. Ainsi, les
indicateurs d'un territoire de la [Hiérarchie fractale](#/hierarchie-fractale) reflètent la
composition démographique locale, et non une simple moyenne géographique.

## Où vit réellement chaque chiffre

La donnée provient de **quatre endroits distincts**, fusionnés au moment de l'affichage :

| Source réelle | Quelle donnée | Comment elle entre | Modifiable sans redéployer ? |
|---|---|---|---|
| **`src/data/craftData.ts`** (code) | Base L1–L8 : logement + mobilité (chiffres « craft » déterministes) | Générée dans le navigateur par une formule déterministe — même territoire + même année ⇒ mêmes valeurs | ❌ non — éditer le code puis redéployer |
| **`templates/food-purchases-l9-data.csv`** → `src/data/foodPurchasesData.ts` | Alimentation + achats au niveau quartier (L9) | CSV importé (`?raw`) **dans** le build : figé au moment de la compilation | ❌ non — régénérer le CSV puis redéployer |
| **Supabase (PostgreSQL hébergé)** | Couche vivante : imports CSV représentant, overwrites, gouvernance des seuils et limites, profils, rôles, débats — plus les anciennes contributions historiques conservées pour compat | Lue en direct à chaque visite et **superposée** par-dessus les données craft | ✅ **oui** — c'est le seul canal qui change à chaud |
| **Pipeline `.ods` → `data/ingestion/`** | Classeur source admin → CSV / JSON normalisés | `npm run ingest:gaia` | ⚠️ existe, mais **pas encore branché** sur la carte |

La superposition Supabase est appliquée par `src/data/contributionAdapter.ts`
(`getDataWithApprovedContributions`, `getDataWithResolvedThresholds`) et le hook
`src/hooks/useGaiaContributions.ts`, façade publique qui compose les owners dans
`src/hooks/gaiaContributions/`.

**À retenir, en une phrase :** ce que tu vois = *données craft du code* (+ *CSV
alimentation/achats figé au build*), puis **Supabase écrase par-dessus** les valeurs publiées par
import CSV représentant ou par overwrite. Le détail du circuit de modification est dans
[Contribution](#/contribution).

## Le pipeline d'ingestion `.ods` (outil admin)

C'est le « tapis roulant » prévu pour faire entrer des données en masse depuis un tableur, sans
toucher au code. Il **existe déjà** (`scripts/ingest_gaia_ods.py`, commande `npm run ingest:gaia`,
tests `npm run test:ingestion`), mais **sa sortie n'alimente pas encore la carte** : c'est
aujourd'hui un étage de **préparation et de vérification**.

**Principe** : le classeur `.ods` reste la **source éditable par un humain** ; le script le
transforme en fichiers techniques stables.

1. **Déposer** le classeur source (ou pointer la variable d'environnement `GAIA_ODS` dessus).
2. **Lancer** `npm run ingest:gaia` depuis la racine du projet.
3. Le script **lit, mappe et valide** chaque ligne, puis écrit dans `data/ingestion/` :
   `territories.csv`, `territory_populations.csv`, `metric_observations.csv`,
   `fractal_data_api.json` (format « API ») et `ingestion_report.json` (rapport d'anomalies).
4. **Vérifier le rapport** : les lignes que le script ne sait pas mapper de façon sûre sont
   **rejetées dans le rapport, jamais devinées**.

**Garde-fous pour l'admin :**
- Le **mapping** (code du classeur ↔ identifiant Gaïa) est explicite, feuille par feuille
  (`scripts/gaia_ingestion_mapping.csv`). Les codes du tableur ne sont **pas** les identifiants
  internes.
- `--strict` : le run échoue dès qu'une ligne est rejetée (import « tout ou rien »).
- `--dry-run` : teste l'import puis annule tout.
- La logique de parsing/validation vit **dans le code** (`scripts/ingest_gaia_ods.py`).

> [!note] Brancher la sortie du pipeline (`fractal_data_api.json`) sur la couche de lecture de
> l'app reste à faire — c'est un point ouvert listé dans `roadmap-status.md`. Tant que ce n'est pas
> fait, le seul canal vivant est **Supabase**.
