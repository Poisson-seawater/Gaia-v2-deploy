# Onboarding Guide — Gaïa Equation

> A step-by-step guide for new developers joining the project.
> **Time estimate**: 30–45 minutes

---

## 🎯 What You'll Learn

After completing this guide, you will be able to:
1. Run the application locally
2. Understand the fractal territory model
3. Navigate the codebase confidently
4. Make your first contribution

---

## Step 1: Environment Setup

### Prerequisites

| Tool | Version | Check command |
|------|---------|--------------|
| Node.js | ≥ 18 | `node --version` |
| npm | ≥ 9 | `npm --version` |
| Git | Any | `git --version` |

### Clone & Install

```bash
git clone https://github.com/DualFlowHQ/Gaia-siteweb.git
cd Gaia-siteweb
npm install
npm run dev
```

Open http://localhost:5173 — you should see the Gaïa Equation map interface.

### Optional: Supabase (for contributions)

Copy `.env.example` to `.env` and fill in your Supabase credentials:

```bash
cp .env.example .env
# Edit .env with your VITE_SUPABASE_URL and VITE_SUPABASE_ANON_KEY
```

Without Supabase, the app runs in **read-only explore mode** — all visualization features work.

---

## Step 2: Understand the Core Concept

### The Fractal Hierarchy

The Gaïa Equation divides the world into **self-similar territorial units** at 9 levels:

```
L1  Mother Earth (10B people)
 └─ L2  Subcontinents (11 territories)
     └─ L3  Macro Regions — e.g., "France & Swiss Romande"
         └─ L4  Regions — e.g., "Île-de-France"
             └─ L5  Departments — e.g., "Paris"
                 └─ L6  Arrondissements — e.g., "18th (Montmartre)"
                     └─ L7  Quartiers — e.g., "Clignancourt"
                         └─ L8  Blocks — e.g., "Block 3"
                             └─ L9  Proches (leaf) — ~25 people
```

**Key rule**: IDs use dot notation. `1.2.2.1.1.1` = Earth → Europe → France → IDF → Paris → 18th.

### Three Dimensions of Measurement

Each territory is measured across:

| Dimension | Options |
|-----------|---------|
| **Type** | Gaïa (Vital ratio), Energy (kWh), Carbon (tCO₂) |
| **Category** | Housing, Mobility, Food, Purchases |
| **Year** | 1993, 2023, 2050 baseline; any year 1900–2100 becomes available once a representative CSV import publishes it |

### Color System

- 🔵 **Blue** = Below vital threshold (under-consumption)
- 🟣/🟤/⚫ **Footprint color** = Normal range
- 🟠 **Orange** = Above limit (over-consumption)

---

## Step 3: Explore the Codebase

### Entry Points

| File | Purpose |
|------|---------|
| `src/main.tsx` | React DOM mount |
| `src/App.tsx` | **Thin shell** — wires the state spine + 3 app-level hooks, no state of its own beyond theme |
| `src/components/app/AppHeader.tsx` | Top nav, role-aware menu, pending-total badge, theme toggle |
| `src/components/app/AppViewRouter.tsx` | Routes every `appView` except `explore` to its panel component |
| `src/components/explorer/ExplorerView.tsx` | The default `explore` screen — filters, map/chart viewport, instructions, legend |

### Architecture Layers

Read files in this order to understand the data flow:

```
1. src/data/types.ts          → What does data look like?
2. src/data/fractalTree.ts    → What territories exist?
3. src/data/craftData.ts      → How is data generated?
4. src/utils/metrics.ts       → How does a type+category → value?
5. src/utils/kgi.ts           → How are KGIs calculated?
6. src/utils/fractalMap.ts    → How does the map know what to draw?
7. src/components/MapChart.tsx → How does the map render?
```

### Key Files Cheatsheet

| You want to... | Edit this file |
|----------------|---------------|
| Add a new territory | `src/data/fractalTree.ts` |
| Change default threshold values | `src/constants.ts` (THRESHOLDS) — governed overrides live in Supabase `threshold_changes` |
| Add a new visualization mode | `src/hooks/useExplorerState.ts` (`visualMode` state) + `src/components/explorer/ExplorerViewport.tsx` + new chart component |
| Change map colors | `src/constants.ts` |
| Add a new metric type | `src/utils/metrics.ts` + `src/data/types.ts` |
| Add a new GeoJSON dataset | `public/geo/` + `src/data/geoSources.ts` + `src/utils/fractalMap.ts` |
| Modify contribution schema | `src/data/contributionSchema.ts` |
| Add app-level state (navigation, explorer, drafts) | the matching hook under `src/hooks/` (`useAppNavigation.ts`, `useExplorerState.ts`, `useLiveOverwriteDraft.ts`) — not `App.tsx` |
| Change one of the four Supabase data flows | the matching owner hook under `src/hooks/gaiaContributions/`, not `useGaiaContributions.ts` (the facade only composes them) |

---

## Step 4: Key Patterns to Know

### Pattern 1: Data → Metric → Color

```typescript
// 1. Get data for a territory
const data = getData('1.2.2', 2023);

// 2. Extract metric snapshot  
const snapshot = getMetricSnapshot(data, 'carbone', 'housing');
// → { value: 4.2, vital: 1.5, limit: 8.0, reason: null }

// 3. Determine color tier
const tier = colorTier(snapshot.value, snapshot.vital, snapshot.limit);
// → 'normal' (4.2 is between 1.5 and 8.0)
```

### Pattern 2: Navigation & Drill-Down

```typescript
// Get children of a territory
const children = getChildren('1.2.2');  // → French regions

// Navigate the breadcrumb
const path = getBreadcrumb('1.2.2.1.1');
// → [Earth, Europe, France, IDF, Paris]

// Drill down = change mapContextId + selectedFractalId
setMapContextId('1.2.2');
setSelectedFractalId('1.2.2.1');
```

### Pattern 3: Map Layer Resolution

```typescript
// fractalMap.ts resolves contextId → renderable features
const layer = getMapLayerData('1.2.2', datasets);
// → { features: [...background, ...fractal], focusGeo: ... }
```

---

## Step 5: Quality Gate

Before every PR, run:

```bash
npm run build && npm run lint
```

Both must pass. Automated tests exist for the threshold resolver (`npm run test:thresholds`) and the ingestion pipeline (`npm run test:ingestion`); beyond those, manual verification via `npm run dev` is the minimum quality gate.

---

## Step 6: Key Documents

| Document | Location | Purpose |
|----------|----------|---------|
| Architecture source of truth | `context-gaia-dev` | All architectural decisions |
| Sprint status | `roadmap-status.md` | Current progress & blockers |
| Architecture docs | `docs/architecture.md` | System design with diagrams |
| API reference | `docs/api-reference.md` | All exported modules |

---

## Common Questions

### Q: Why is all the data placeholder?
**A**: The `craftData.ts` generator uses a seeded PRNG, so all base values are **deterministic placeholders**, not real measurements (food/purchases come from a bundled CSV). Wiring a real-data source is an open item tracked in `roadmap-status.md`.

### Q: Why does the map show France-specific code?
**A**: The `fractalMap.ts` layer builder has France-specific `if` chains for Paris, Lyon, Marseille, and Toulouse. This is acknowledged tech debt — making it data-driven and fractal-agnostic is tracked in `roadmap-status.md`.

### Q: How do I add a new city demo path?
1. Add the territory hierarchy in `fractalTree.ts`
2. Add GeoJSON files to `public/geo/`
3. Register in `geoSources.ts`
4. Add a builder function in `fractalMap.ts`
5. Wire the builder in `getMapLayerData()`

### Q: Where does Supabase fit?
Supabase is the live backend — auth, profiles, roles, and the **four data flows**: representative CSV import (atomic, exact-fractal scoped, no admin review — before `contributionAdapter.ts` merges the published cells on craft data), live overwrites, **debates** (text channels with proof uploads, moderated *after* publication — they replaced the old comments system), and threshold governance. Data submission is restricted to **fractal responsables** (appointed by an admin, scoped to their **exact** assigned fractal(s) only — no descendant inheritance since spec 019) and admins; any signed-in user can open a debate. The map/chart visualization still renders without Supabase. See [Rôles & contributions](#/roles-et-contributions) and `supabase/README.md`.

---

*Welcome to the team! 🌍*
