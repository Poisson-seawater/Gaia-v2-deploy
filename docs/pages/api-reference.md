# API & Module Reference — Gaïa Equation

> Inventaire des modules exportés du frontend, vérifié contre `src/`.
> **État au**: 2026-06-28.

---

## Data Layer (`src/data/`)

### `types.ts` — Domain Types
`FractalTerritory` (identité stable : `fractalId`, `name`, `level`, `parentId`, `children`,
`geoFeatureId`, `geoStrategy`, `population`, flags optionnels `isVirtual` / `isDeleted`),
`FractalData` (1 entrée par territoire × année —
**17 sous-indicateurs + 12 seuils** sur `housing` / `mobility` / `food` / `purchases`),
`DataProvenance`, et les types UI `EmpreinteType` (`'gaia' | 'energy' | 'carbon'`),
`Category` (`'housing' | 'mobility' | 'food' | 'purchases'`), `GeoSource`, `GeoSourceKey`.

### `fractalTree.ts` — Territory Hierarchy
Arbre fractal complet (tableau plat + index `Map` pour O(1)).
- `FRACTAL_TREE: FractalTerritory[]`
- `getTerritory(id)`, `getChildren(id)`, `getParent(id)`, `getBreadcrumb(id)`,
  `getTerritoriesByLevel(level)`, `getParisArrondissementFractalId(n)`
- Overlays runtime : `applyTerritoryMerges(...)`, `applyVirtualTopologyGroups(parentId, nodes)`,
  `purgeDeletedTerritories(ids)`, `revertTerritoryMerges()`

### `craftData.ts` — Data Generator
Données déterministes (PRNG seedé) pour chaque territoire × année, avec surcharges mobilité réelle
et données alimentation/achats.
- `CRAFT_DATA: FractalData[]`
- `getData(fractalId, year)`, `getDataForTerritory(fractalId)`, `getDataForLevel(level, year)`

### `geoSources.ts` — GeoJSON Registry
- `GEO_SOURCES` (clés → `{ url/path, idProperty, nameProperty }`)
- `GeoDatasetKey` (type), `REQUIRED_GEO_SOURCE_KEYS`

### `foodPurchasesData.ts` — Bundled CSV
Données alimentation/achats au niveau L9, importées de `templates/food-purchases-l9-data.csv?raw`
et compilées dans le build. Servent de surcharge par-dessus les données craft.

### `contributionAdapter.ts` — Overlay
- `getDataWithApprovedContributions(fractalId, year, approvedContributions)`
- `getDataWithResolvedThresholds(...)` — applique les seuils gouvernés résolus

### `contributionSchema.ts` — Types, validation, scope
Hub des types de domaine côté front :
- **Profils / rôles** : `PROFILE_ROLES` (`citizen`/`entity`/`admin`), `ENTITY_TYPES`, `GaiaProfile`,
  `ProfileInput`
- **Contributions** : `CONTRIBUTION_METRICS`, `GaiaContribution`, `GaiaContributionDraft`,
  `CONTRIBUTION_STATUSES`, `getMetricsForCategory`, `getContributionMetric`
- **Overwrites** : `GaiaOverwrite`, `GaiaOverwriteDraft`
- **Débats** : `GaiaDebate`, `GaiaMessage`, `GaiaProof`, `GaiaDebateDraft`,
  `DEBATE_LIFECYCLE_STATUSES`, `DEBATE_MODERATION_STATUSES`
- **Preuves** : `PROOF_MAX_FILES` (5), `PROOF_MAX_SIZE_BYTES` (10 Mo),
  `PROOF_ALLOWED_EXTENSIONS` (pdf/png/jpg/jpeg/webp/csv/xlsx), `getProofMimeType`, `validateProofFile`
- **Scope / chaîne** : `isFractalWithinScope(fractalId, responsibilities)` (miroir exact du SQL
  `can_manage_fractal()`), `getResponsableTerritories`, `getRepresentativeChainState`,
  `RepresentativeChainState`, `AdminDirectoryEntry`, `formatTerritoryPath`

### `thresholdSchema.ts` — Threshold Types
- `THRESHOLD_FOOTPRINT_TYPES` (`gaia`/`energie`/`carbone`), `THRESHOLD_KINDS` (`vital`/`limit`),
  `THRESHOLD_ACTIONS` (`set`/`reset`)
- `ThresholdChange`, `SetThresholdDraft`, `SetFractalVitalDraft`, `ResetFractalVitalDraft`
- `THRESHOLD_COMBINATIONS`, `getThresholdCombination(...)`

> `src/data/mockData.ts` existe encore mais est **déprécié/inutilisé** (remplacé par `craftData.ts`).

---

## Utilities (`src/utils/`)

### `metrics.ts`
- `MetricSnapshot` (`{ value, vital, limit, reason }`), `MetricAvailability`
- `getMetricAvailability(type, category)` — bloque **uniquement** `carbone × mobility`
- `getMetricSnapshot(data, type, category)` — résout les 12 chemins de métrique (ratio pour Gaïa)

### `mapMetrics.ts`
- `getFractalMetricSnapshot(...)`, `getFractalMetricColor(...)` — utilisent `BLUE_COLOR` /
  `RED_COLOR` / `FOOTPRINT_COLORS`

### `thresholds.ts` — Threshold resolver (testé)
- `resolveEffectiveThreshold`, `resolveThresholdPair`, `applyEffectiveThresholds`,
  `getSystemThresholdYear` — résolution non-rétroactive, remontée d'ancêtres, repli sur défaut système.
- Tests : `src/utils/thresholds.test.ts` (`npm run test:thresholds`).

### `kgi.ts` — KGI Calculations
- `gaiaHousing(surface, vitalSurface)`, `gaiaMobility(km, vitalKm)`, `kgiEnergy(e)`, `kgiCarbon(c)`
- `colorTier(value, vital, limit) → 'below' | 'normal' | 'above'`

### `contributionCsv.ts` — Import/export CSV représentant (spec 019)
- `buildRepresentativeExportRows(...)`, `parseCsv(...)` — construit et relit le CSV large (data /
  vital / limite, 3 colonnes source par cellule éditable).
- `buildRepresentativeCurrentValueMap(...)` — valeurs actuelles servant de référence au diff import.
- `buildRepresentativePublishCells(report)` — traduit un rapport validé en cellules à publier via le
  RPC `publish_representative_import` (`supabase/migrations/20260708190000_representative_data_import.sql`).
- `serializeRepresentativeValidationReport(report)` — rapport texte téléchargeable (succès ou échec).
- Accepte en lecture les anciens fichiers à métadonnées **de ligne** (rétro-compat, une seule
  source/URL/justification pour toute la ligne) en plus du format actuel **par cellule**.
- Tests : `src/utils/contributionCsv.test.ts`.

### `fractalMap.ts` — Map Layer Builder
- `ROOT_MAP_CONTEXT_ID = '1'`
- `getMapLayerData(contextId, datasets): MapLayerData`
- Types : `RenderedMapFeature`, `MapLayerData` (inclut `parentOutlineGeo`), `MapDatasets`,
  `MapGeoFeature`, `MapGeoCollection`

### `topology.ts` — Topological Editing Helpers
- `buildAdjacencyGraph(siblings, geometrySource)` — graphe de contiguïté par frontières partagées.
- `clusterContiguousTerritories(...)` — clustering déterministe contiguity-first avec fallback île/exclave.
- `groupChildrenGeographically(parentId, siblings, geometrySource)` — génère les groupes virtuels `G*`
  pour garantir au maximum 18 enfants directs.
- `calculateSurfaceRatios(...)`, `redistributeDataBySurface(...)` — redistribution numérique
  proportionnelle à la surface, avec conservation des totaux.
- `snapLineStringToBoundaryVertices(...)`, `snapTopoJsonArcs(...)` — snapping simple des lignes/arcs.
- `validateTopologyProposal(draft, known?, geometryContext?)`, `createMixedChangeReport(...)` — validation et rapport avant publish. `geometryContext` (optionnel) déclenche les codes `ZERO_AREA` (aire enfant `< MIN_TOPOLOGY_AREA_M2`), `INVALID_RATIO_SUM` (ratios ne sommant pas à 1.0) et `UNKNOWN_PARENT`.

#### Batch 2 — split & redistribution surfacique (Spec 13)
- `splitPolygonGeometry(geometry, splitLine): (Polygon | MultiPolygon)[]` — coupe un territoire avec
  une ligne tracée (lame Turf `buffer` + `difference`). Les sous-polygones non traversés (îlots,
  exclaves) sont assignés à la pièce résultante **la plus proche par centroïde** ; un enfant qui
  absorbe un îlot devient un `MultiPolygon`.
- `calculateIntersectionRatios(originalGeometry, splitGeometries): number[]` — ratios de surface des
  pièces vs l'original (réutilise `calculateSurfaceRatios`), **sommant à 1.0**.
- `distributeValueByRatios(value, ratios): number[]` — répartit une valeur par ratio en affectant le
  reste d'arrondi au **plus grand enfant** ⇒ somme des enfants = parent **exactement** (Val-201 / SC-002).
- `buildRedistributionOverwrites(parentId, childIds, ratios, metrics): TopologyDataOverwrite[]` — un
  overwrite par enfant × métrique, avec `sourceTerritoryId = parentId` et la valeur redistribuée.
- `checkSplitGeometryIntegrity(originalGeometry, pieces): GeometryIntegrityCheck[]` — journal vert/rouge
  des contrôles `no-overlap` / `no-gap` / `contiguity` (réutilise `buildAdjacencyGraph`, SC-001).
- Types : `RedistributionMetricInput`, `GeometryIntegrityCheck`, `TopologyGeometryContext`.

**Flux de publication Batch 2** : l'éditeur `TopologySplitEditor.tsx` (onglet Admin → Redécoupage)
construit un `PublishTopologyChangeDraft` (`deletedIds` = parent coupé, `newTerritories` = pièces,
`dataOverwrites` = redistribution) puis appelle `publishTopologyChange` (hook) → RPC
`public.publish_topology_change`. La RPC purge les responsabilités des IDs supprimés, insère les
overwrites recalculés et ouvre un débat « Estimation par redécoupage » par nouvelle zone.
⚠️ Migration `20260627164000_topological_redistricting.sql` **non encore déployée en prod** (déploiement
manuel admin requis).

### `isocurve2d.ts` — 2D Math (cas 1)
- `getIsocurve2DCase1Availability(...)`, `getIsocurve2DCase1Point(...)`
- Types : `Isocurve2DAvailability`, `Isocurve2DCase1Point`

---

## Hooks (`src/hooks/`)

### `useGaiaContributions.ts` — State spine facade
Façade publique stable du state spine Supabase. `App.tsx` l'appelle une fois et passe le résultat
comme `contributionState`; les noms de champs/actions restent le contrat public. L'implémentation
vit dans `src/hooks/gaiaContributions/`, par owner de domaine :

- **Auth / profil** : `useAuthProfileFlow` — session, profil, sign-in / sign-up / reset, mise à jour de profil.
- **Rôles / directory** : `useDirectoryResponsibilityFlow` — `responsibilities`, `isResponsable`, représentants, recherche utilisateurs.
- **Contributions / overwrites** : `useContributionOverwriteFlow` — `exportRepresentativeCsv`,
  `validateRepresentativeImport`, `publishRepresentativeImport` (import/export CSV représentant,
  `src/utils/contributionCsv.ts`), `submitOverwrite` (+ rejet admin). Plus de `submitContribution` /
  `reviewContribution` : la file de validation admin a été retirée (spec 019).
- **Débats** : `useDebateFlow` — `createDebate`, `sendDebateMessage`, `flagDebate`, `rejectDebate`,
  `adminNullifyDebate`, `representativeDecideDebate`, `submitDebateCreatorFeedback`,
  `addDebateParticipant`, `loadDebates`, `loadDebateThread`, `getProofUrl`, `clearActiveDebate` ;
  état `debates`, `activeDebate`, `activeMessages`, `activeProofs`, `activeCanWrite`,
  `adminDebateEscalations`.
- **Seuils** : `useThresholdFlow` — `setGlobalLimit`, `resetGlobalLimit`, `setFractalVital`, `resetFractalVital`,
  `loadThresholdChanges` ; état `thresholdChanges`.
- **Topologie** : `useTopologyFlow` — `publishTopologyChange(draft)` appelle le RPC Supabase
  `public.publish_topology_change(...)` pour publier les overwrites, purger les responsabilités et
  créer les débats admin d'estimation.

---

## Components (`src/components/`)

19 composants. Les vues sont câblées sur `appView` dans `App.tsx`.

| Composant | Rôle |
|-----------|------|
| `MapChart` | Carte D3-Geo (sélection / drill-down) |
| `TreemapChart` | Treemap D3-Hierarchy (sous-mode `cartes`) |
| `Benchmark1DChart` | Barres 1D (largeur ∝ population) |
| `Isocurve2DChart` | Isocourbes 2D (cas 1) |
| `ThreeDChart` | Vue 3D SVG du territoire sélectionné |
| `ActiveRegion3DColumn` | Colonne 3D de la fractale active (sidebar) |
| `ContributionPanel` | Export/import CSV représentant (responsable) |
| `RepresentativePanel` | Overwrites + seuils vitaux (responsable) |
| `AdminPanel` | Limites mondiales, modération débats, annuaire des représentants — plus de revue de contributions |
| `GlobalLimitsManager` | UI admin des limites mondiales |
| `VitalThresholdsManager` | UI seuils vitaux / limites |
| `ResponsablesManager` | « Constructeur de chaîne de représentant » (admin) |
| `TopologyProposalManager` | Proposition reviewable de redécoupage topologique + publish Supabase |
| `DebateList` | Index public des débats |
| `DebateChannel` | Fil d'un débat (messages, preuves, résolution) |
| `DebateCreateForm` | Création de débat + upload de preuves |
| `DebateStatusCard` | Carte d'état du débat sur la donnée sélectionnée |
| `AccountPanel` | Authentification / profil |
| `AuthForm` | Formulaire d'authentification partagé |
| `AnchorPicker` | Sélecteur d'ancrage (chaîne de représentant) |

---

## Lib & Constants

### `src/lib/supabaseClient.ts`
- `supabase` (client), `isSupabaseConfigured` (booléen — l'app tourne en lecture seule sans config).

### `src/constants.ts`
| Constant | Valeur | Description |
|----------|--------|-------------|
| `BLUE_COLOR` | `#3b82f6` | Sous le vital |
| `RED_COLOR` | `#dc2626` | Au-dessus de la limite |
| `FOOTPRINT_COLORS.gaia` | `#800080` | Violet — empreinte Gaïa |
| `FOOTPRINT_COLORS.energie` | `#a65e2e` | Marron — empreinte énergie |
| `FOOTPRINT_COLORS.carbone` | `#6c6960` | Gris — empreinte carbone |
| `THRESHOLDS.carbone` | `{ vital: 1.5, limite: 4.0 }` | Seuils carbone par défaut |
| `THRESHOLDS.energie` | `{ vital: 2000, limite: 6000 }` | Seuils énergie par défaut |
| `THRESHOLDS.gaia` | `{ vital: 1.0, limite: 2.0 }` | Seuils Gaïa par défaut |

### `src/App.tsx`
Composant racine unique (~1200 lignes), **sans librairie de routing**. Bascule l'écran sur
`appView` (`explore | contribute | admin | representative | account | debate | create-debate | debates`)
et l'explorateur sur `visualMode` (`cartes | 1d | 2d | 3d`) + `mapDisplayMode`
(`geographic | treemap`).
