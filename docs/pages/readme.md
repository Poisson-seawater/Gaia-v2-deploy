# 🌍 Gaïa Equation — Environmental Footprint Visualization

[![React 19](https://img.shields.io/badge/React-19-61DAFB?logo=react)](https://react.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?logo=typescript)](https://typescriptlang.org)
[![Vite 8](https://img.shields.io/badge/Vite-8-646CFF?logo=vite)](https://vite.dev)
[![Tailwind 4](https://img.shields.io/badge/Tailwind-4-06B6D4?logo=tailwindcss)](https://tailwindcss.com)

A web application to **visualize resource consumption** (Gaïa index, energy, carbon) across fractal territories — from Earth down to neighbourhood level. Data comes from local craft generators, a bundled food/purchases dataset, and a live **Supabase** layer (no WikiData / SPARQL today). The application supports **3 display units × 4 need categories**, over the baseline years (1993, 2023, 2050) plus any year from 1900 to 2100 published through a representative CSV import.

---

## Table of Contents

- [Features](#features)
- [Architecture Overview](#architecture-overview)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Data Model](#data-model)
- [Visualization Modes](#visualization-modes)
- [Environment Variables](#environment-variables)
- [Scripts Reference](#scripts-reference)
- [Contributing](#contributing)
- [License](#license)

---

## Features

- **Fractal territory hierarchy** — 9-level drill-down from Mother Earth (L1) to neighbourhood "Proches" (L9)
- **Interactive D3-Geo map** — click to select, double-click to drill down, breadcrumb navigation
- **3 footprint types** — Gaïa (Vital ratio), Énergétique (kWh/pp/year), Carbone (tCO₂/pp/year)
- **4 need categories** — Housing, Mobility, Food, Purchases
- **Visualization modes** — geographic Map + Treemap, 1D Benchmark (population-width bars), 2D Isocurves (case 1), 3D, and a sidebar 3D column
- **Color tier system** — Blue (below vital) / Footprint color (normal) / Red (above limit)
- **Supabase governance** — representative CSV import/export (atomic, no admin review), live overwrites, data debates with proof uploads, and append-only threshold governance (global limits + fractal vitals)
- **Multi-city support** — Paris (full L4–L9), Lyon, Marseille, Toulouse demo branches
- **Trans-border territories** — France + Swiss Romande as a single L3 territory

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   React Frontend                     │
│  App.tsx (thin shell) → AppHeader / AppViewRouter /  │
│  ExplorerView → Components → D3-Geo/-Hierarchy       │
│  useAppNavigation · useExplorerState ·               │
│  useLiveOverwriteDraft (app-level hooks)             │
│  useGaiaContributions.ts facade → domain owner hooks │
├─────────────────────────────────────────────────────┤
│             Data Layer (TypeScript)                  │
│  fractalTree.ts → craftData.ts → metrics.ts          │
│  foodPurchasesData.ts   contributionAdapter.ts       │
│  thresholds.ts   mapMetrics.ts                       │
├─────────────────────────────────────────────────────┤
│          GeoJSON (static files in /public/geo/)      │
│  world-countries · france-regions · paris-quartiers  │
├─────────────────────────────────────────────────────┤
│   Supabase (Auth · Profiles · Roles · Contributions  │
│   · Overwrites · Debates+proofs · Threshold gov.)    │
│   anon key + Row-Level Security                      │
└─────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Framework | React | 19.2 |
| Language | TypeScript | 5.9 |
| Build | Vite | 8.0 |
| Styling | Tailwind CSS | 4.2 |
| Map rendering | D3-Geo | 3.1 |
| Icons | Lucide React | 0.577 |
| Backend (contributions) | Supabase | 2.105 |
| Linting | ESLint | 9.39 |

---

## Getting Started

### Prerequisites

- **Node.js** ≥ 18
- **npm** ≥ 9

### Installation

```bash
git clone https://github.com/DualFlowHQ/Gaia-siteweb.git
cd Gaia-siteweb
npm install
```

### Development

```bash
npm run dev
# → http://localhost:5173
```

### Production Build

```bash
npm run build
npm run preview
```

### Quality Check

```bash
npm run build && npm run lint
```

---

## Project Structure

```
Gaia-siteweb/
├── public/
│   └── geo/                    # Static GeoJSON files
│       ├── world-countries.geojson
│       ├── france-regions.geojson
│       ├── france-departments.geojson
│       ├── france-cities.geojson
│       ├── paris-arrondissements.geojson
│       ├── paris-quartiers.geojson
│       ├── lyon-arrondissements.geojson
│       └── marseille-quartiers.geojson
├── src/
│   ├── main.tsx                # App entry point
│   ├── App.tsx                 # Thin shell — wires state, no routing library
│   ├── constants.ts            # Color palette & global thresholds
│   ├── index.css               # Global styles (Tailwind)
│   ├── components/
│   │   ├── app/                 # AppHeader.tsx, AppViewRouter.tsx (shell composition)
│   │   ├── explorer/            # ExplorerView.tsx + ExplorerFilters/Viewport/Instructions/Legend,
│   │   │                        # SelectedTerritoryDetails.tsx, LiveOverwriteForm.tsx
│   │   ├── MapChart.tsx · TreemapChart.tsx · Benchmark1DChart.tsx
│   │   ├── Isocurve2DChart.tsx · ThreeDChart.tsx · ActiveRegion3DColumn.tsx
│   │   ├── ContributionPanel.tsx · RepresentativePanel.tsx · AdminPanel.tsx
│   │   ├── GlobalLimitsManager.tsx · VitalThresholdsManager.tsx · ResponsablesManager.tsx
│   │   ├── DebateList.tsx · DebateChannel.tsx · DebateCreateForm.tsx · DebateStatusCard.tsx
│   │   └── AccountPanel.tsx · AuthForm.tsx · AnchorPicker.tsx
│   ├── data/
│   │   ├── types.ts             # FractalTerritory, FractalData, GeoSource
│   │   ├── fractalTree.ts       # Full L1–L9 territory hierarchy
│   │   ├── craftData.ts         # Seeded PRNG data generator
│   │   ├── foodPurchasesData.ts # CSV-imported food & purchases data (bundled)
│   │   ├── geoSources.ts       # GeoJSON source registry
│   │   ├── contributionAdapter.ts # Overlay contributions + resolve thresholds
│   │   ├── contributionSchema.ts  # Domain types: contributions, overwrites, debates, scope
│   │   ├── thresholdSchema.ts     # Threshold governance types
│   │   └── mockData.ts         # [DEPRECATED] Legacy mock data (unused)
│   ├── utils/
│   │   ├── fractalMap.ts        # Map layer construction & feature matching
│   │   ├── metrics.ts           # Metric snapshot extraction
│   │   ├── mapMetrics.ts        # Color tiers for map / treemap
│   │   ├── kgi.ts               # KGI calculation formulas
│   │   ├── thresholds.ts        # Governed threshold resolver (+ thresholds.test.ts)
│   │   └── isocurve2d.ts        # 2D isocurve math (case 1)
│   ├── hooks/
│   │   ├── useGaiaContributions.ts # Stable Supabase state spine facade
│   │   ├── gaiaContributions/      # Domain owner hooks + shared contract helpers
│   │   ├── useAppNavigation.ts     # appView + debate view transitions
│   │   ├── useExplorerState.ts     # explorer filters, map context, drill-down
│   │   └── useLiveOverwriteDraft.ts # live-overwrite form draft + submit
│   └── lib/
│       └── supabaseClient.ts    # Supabase client initialization
├── scripts/
│   ├── ingest_gaia_ods.py       # ODS → SQLite ingestion pipeline
│   ├── test_ingest_gaia_ods.py  # Ingestion tests
│   └── gaia_ingestion_mapping.csv
├── supabase/
│   └── migrations/              # Database migration SQL
├── data/                        # Ingestion output directory
├── context-gaia-dev             # Architecture source of truth
├── roadmap-status.md            # Sprint status & revised plan
└── package.json
```

## Data Acquisition & Ingestion

The application aggregates data from three primary pipelines:
1. **Spreadsheet Ingestion**: A Python script (`scripts/ingest_gaia_ods.py`) converts human-editable OpenDocument Spreadsheets (`.ods` files) into relational JSON/CSV data.
2. **Representative CSV Import**: Directly assigned representatives export a wide CSV for their exact fractals, edit it offline, and re-import it; the validated batch publishes atomically and overlays static values (no central admin review).
3. **Seeded PRNG Mocking**: Procedural placeholders fill in remaining territories deterministically.

> For a detailed walkthrough of the spreadsheet ingestion workflow, the mapping configuration, and database constraints, see the [Architecture Documentation](architecture.md#46-data-acquisition--ingestion-pipeline).

---

## Data Model

### Fractal Territory (Identity)

Each territory has a **stable identity** that doesn't change with data updates:

```typescript
interface FractalTerritory {
  fractalId: string;          // "1.2.2" (dot notation)
  name: string;               // "France & Swiss Romande"
  level: number;              // 1–9
  parentId: string | null;    // "1.2" (null for Earth)
  children: string[];         // child fractalIds
  geoFeatureId: string | string[];  // GeoJSON match key(s)
  geoStrategy: 'single' | 'union'; // polygon merge strategy
  population: number;
}
```

### Fractal Hierarchy — 9 Levels

| Level | Name | Avg Pop | Count |
|-------|------|---------|-------|
| L1 | Mother Earth | 10B | 1 |
| L2 | Subcontinent | 100M | 12 |
| L3 | Macro Region | 75M | 150 |
| L4 | Meso Region | 6M | 2,000 |
| L5 | Micro Region | 500K | 20,000 |
| L6 | Local Community | 43K | 250,000 |
| L7 | Under Local | 4K | 3,000,000 |
| L8 | Neighbourhood | 300 | 55,000,000 |
| L9 | Proches (leaf) | 25 | 500,000,000 |

### Fractal Data (Measurements)

One entry per territory per year — 17 sub-indicators + 12 thresholds across 4 categories:

| Category | Indicators |
|----------|-----------|
| **Housing** | surface_m² (total/primary), energy_intensity, energy_kwh, carbon_tco2 |
| **Mobility** | km, energy_kwh, carbon_tco2 |
| **Food** | kcal, energy_kwh, carbon_tco2 |
| **Purchases** | units, energy_kwh, carbon_tco2 |

### KGI Calculations

```
Gaïa Housing  = surface_m² / vital_surface
Gaïa Mobility = km / vital_km
Energy        = raw kWh/person (color vs vital/limit thresholds)
Carbon        = raw tCO₂/person (color vs vital/limit thresholds)
```

### Color Tier System

| Tier | Color | Meaning |
|------|-------|---------|
| `below` | Blue `#3b82f6` | Below vital threshold |
| `normal` | Footprint color | Within normal range |
| `above` | Red `#dc2626` | Above limit threshold |

Footprint colors: Gaïa = Violet `#8b5cf6`, Energy = Brown `#A0522D`, Carbon = Grey `#6b7280`

---

## Visualization Modes

### 🗺️ Map (Cartes)
Interactive D3-Geo choropleth with click-to-select and double-click drill-down. Territories are color-coded by the active metric's tier. Supports fractal navigation with breadcrumb. A **Treemap** sub-mode (`mapDisplayMode`) renders the same data as population-sized rectangles (D3-Hierarchy).

### 📊 1D Benchmark (One D)
Population-proportional bar chart. Each territory's bar width represents its population. Shows temporal evolution from 1993 → 2050 with the selected year highlighted.

### 📐 2D Isocurves (Two D)
Isocurve plane — **case 1 only** today (resource vs energy performance, available for energy outside mobility). Other cases are tracked in `roadmap-status.md`.

### 📦 3D (Three D)
SVG 3D view of the selected territory (`ThreeDChart.tsx`). A compact 3D column of the active fractal (`ActiveRegion3DColumn.tsx`) also appears in the left sidebar.

---

## Environment Variables

Create a `.env` file (see `.env.example`):

| Variable | Required | Description |
|----------|----------|-------------|
| `VITE_SUPABASE_URL` | For contributions | Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | For contributions | Supabase anon API key |

> Without Supabase credentials, the app runs in read-only explore mode. Contribution and admin panels will show a configuration warning.

---

## Scripts Reference

| Command | Description |
|---------|-------------|
| `npm run dev` | Start Vite dev server with HMR |
| `npm run build` | TypeScript build + Vite production bundle |
| `npm run preview` | Serve production build locally |
| `npm run lint` | Run ESLint across the codebase |
| `npm run test:thresholds` | Node test runner over `src/utils/thresholds.test.ts` |
| `npm run ingest:gaia` | Python: ODS → normalized CSV/JSON ingestion |
| `npm run test:ingestion` | Python: Run ingestion tests |

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Make small, targeted changes
4. Ensure `npm run build && npm run lint` passes
5. Include screenshots for UI changes
6. Open a Pull Request with a summary and linked issue

### Commit Convention

Use short, imperative messages:
```
Add KGI tooltip formatting
Fix MapChart drill-down at L7
Refactor fractalMap to use config
```

---

## License

This project is part of the Gaïa Equation initiative.
