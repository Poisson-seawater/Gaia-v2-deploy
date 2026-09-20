# Charte graphique & Identité visuelle

Cette page présente les règles graphiques, les polices, les palettes de couleurs et l'intégration
du **mode sombre / mode clair** de l'application Gaïa Equation. Les valeurs ci-dessous reflètent les
variables CSS réelles de `src/index.css` et les constantes de `src/constants.ts`.

---

## 1. Principes de Design

L'expérience visuelle se veut claire, moderne et lisible, avec des contrastes marqués et des
bordures subtiles.

*   **Palette neutre** : fonds clairs / ardoise (`Slate`), texte sombre en clair et clair en sombre.
*   **Transitions courtes** : le `body` utilise une transition de `200ms` (`ease`) sur la couleur de
    fond et le texte lors de la bascule de thème.
*   **Bascule de thème** : le mode sombre est activé en ajoutant la classe `.dark` sur la balise
    `<html>` (variante Tailwind `@custom-variant dark`). L'état est persisté dans `localStorage`
    sous la clé **`gaia-theme`** (`'light'` / `'dark'`), géré dans `src/App.tsx`.

---

## 2. Palette de Couleurs (Light & Dark)

Les couleurs d'interface s'appuient sur des variables CSS déclarées dans `src/index.css`.

### Couleurs fondamentales de l'interface

| Variable CSS | Mode Clair | Mode Sombre | Rôle |
| :--- | :--- | :--- | :--- |
| `--color-background` | <span style="display:inline-block;width:12px;height:12px;background:#f8fafc;border:1px solid #cbd5e1;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#f8fafc` | <span style="display:inline-block;width:12px;height:12px;background:#0f172a;border:1px solid #475569;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#0f172a` | Fond principal de l'application |
| `--color-surface` | <span style="display:inline-block;width:12px;height:12px;background:#ffffff;border:1px solid #cbd5e1;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#ffffff` | <span style="display:inline-block;width:12px;height:12px;background:#1e293b;border:1px solid #475569;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#1e293b` | Fond des cartes, panneaux, sidebars |
| `--color-border` | <span style="display:inline-block;width:12px;height:12px;background:#e2e8f0;border:1px solid #cbd5e1;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#e2e8f0` | <span style="display:inline-block;width:12px;height:12px;background:#334155;border:1px solid #475569;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#334155` | Bordures et contours |
| `--color-text` | <span style="display:inline-block;width:12px;height:12px;background:#334155;border:1px solid #cbd5e1;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#334155` | <span style="display:inline-block;width:12px;height:12px;background:#f1f5f9;border:1px solid #475569;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#f1f5f9` | Texte principal et titres |
| `--color-text-light` | <span style="display:inline-block;width:12px;height:12px;background:#64748b;border:1px solid #cbd5e1;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#64748b` | <span style="display:inline-block;width:12px;height:12px;background:#94a3b8;border:1px solid #475569;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#94a3b8` | Textes secondaires, légendes |

Couleurs de marque / accent (`--color-brand`, `--color-purple`) : vert `#166534` (clair) /
`#22c55e` (sombre) ; violet `#9333ea` (clair) / `#a855f7` (sombre).

### Couleurs d'empreinte et de seuils (identiques dans les deux modes)

Ces couleurs proviennent de `src/constants.ts` (`BLUE_COLOR`, `RED_COLOR`, `FOOTPRINT_COLORS`) et
pilotent la coloration des territoires selon leur position vis-à-vis des seuils :

*   **Sous le vital** : <span style="display:inline-block;width:12px;height:12px;background:#3b82f6;border:1px solid #1d4ed8;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#3b82f6` (bleu) — `BLUE_COLOR`
*   **Au-dessus de la limite** : <span style="display:inline-block;width:12px;height:12px;background:#dc2626;border:1px solid #b91c1c;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#dc2626` (rouge) — `RED_COLOR`
*   **Empreinte Gaïa** : <span style="display:inline-block;width:12px;height:12px;background:#8b5cf6;border:1px solid #6d28d9;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#8b5cf6` (violet)
*   **Empreinte Énergie** : <span style="display:inline-block;width:12px;height:12px;background:#A0522D;border:1px solid #7c3f22;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#A0522D` (marron)
*   **Empreinte Carbone** : <span style="display:inline-block;width:12px;height:12px;background:#6b7280;border:1px solid #4b5563;border-radius:3px;vertical-align:middle;margin-right:4px;"></span> `#6b7280` (gris)

> Dans la plage normale (entre vital et limite), un territoire prend la **couleur de son empreinte
> active**. Un dépassement de limite reste **rouge** même si un vital personnalisé est défini.

---

## 3. Typographie

*   **Application web** : `Inter` (sans-serif), importée dans `src/index.css` — titres, étiquettes,
    boutons, tableaux.
*   **Site de documentation** : `Public Sans` (sans-serif), importée dans `docs/index.html`.

---

## 4. Composants visuels (cartes SVG)

Les fonds de carte utilisent des variables `--map-*` dédiées (`src/index.css`) :

| Variable | Mode Clair | Mode Sombre | Usage |
| :--- | :--- | :--- | :--- |
| `--map-sphere-fill` | `#f0f8ff` | `#0b1329` | Mer / sphère du globe |
| `--map-land-empty` | `#dbe6f3` | `#334155` | Terres sans donnée |
| `--map-stroke-bg` | `#cbd5e1` | `#475569` | Contour des territoires de fond |
| `--map-stroke-selected` | `#0f172a` | `#22c55e` | Contour du territoire sélectionné |

Les grilles d'axes des graphiques utilisent `--color-border` pour les traits et `--color-text-light`
pour les étiquettes de graduation.
