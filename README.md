# OCHA Anticipatory Action Interactive Map

Interactive choropleth map showing OCHA-facilitated anticipatory action frameworks worldwide. Built with [Datawrapper](https://www.datawrapper.de/) and powered by a Google Sheet for easy updates by non-technical users.

![Map Preview](preview_sample.jpg)

**Live map:** [datawrapper.dwcdn.net/8Kssm](https://datawrapper.dwcdn.net/8Kssm/)
**Destination page:** [unocha.org/anticipatory-action](https://www.unocha.org/anticipatory-action)

---

## How It Works

```
User edits Google Sheet (dropdowns only — no manual data entry)
        |
        v  (twice daily via GitHub Actions)
CSV is fetched and committed to this repo
        |
        v
Datawrapper auto-refreshes from the raw GitHub CSV
```

- **Country colors** show the framework status via `Map_Color` column (Activated, Endorsed, In development, or Mixed statuses)
- **Tooltips** show each crisis with its own status badge and [OCHA humanitarian icon](https://github.com/UN-OCHA/humanitarian-icons-2026)
- **No manual republishing needed** — the pipeline handles everything

---

## Quick Links

| Resource | Link |
|----------|------|
| Google Sheet (data source) | [Open Sheet](https://docs.google.com/spreadsheets/d/13cRZ8XGvLTqLVytn3B24FOoKyWy7p3_tUNeVwZ86bx8/edit) |
| Datawrapper chart (editor) | [Open in Datawrapper](https://app.datawrapper.de/ocha/edit/8Kssm) |
| Raw CSV (Datawrapper reads this) | [aa_map_data.csv](https://raw.githubusercontent.com/UN-OCHA/aa_map_interactive_version_BDU/refs/heads/main/aa_map_data.csv) |
| GitHub Actions (automation) | [View runs](https://github.com/UN-OCHA/aa_map_interactive_version_BDU/actions) |
| OCHA Humanitarian Icons | [UN-OCHA/humanitarian-icons-2026](https://github.com/UN-OCHA/humanitarian-icons-2026) |

---

## For Users: How to Update the Map

Just edit the Google Sheet — everything else is automatic.

1. Open the [Google Sheet](https://docs.google.com/spreadsheets/d/13cRZ8XGvLTqLVytn3B24FOoKyWy7p3_tUNeVwZ86bx8/edit)
2. Go to the **Map Data** tab
3. Use the **dropdowns** to add/edit countries, crisis types, and their framework statuses
4. The map updates automatically within 12 hours

**To force an immediate update:**
1. Go to [GitHub Actions](https://github.com/UN-OCHA/aa_map_interactive_version_BDU/actions) → **"Sync Google Sheet to CSV"** → **Run workflow**
2. Or go to the Datawrapper chart and click **Republish**

> See the **Instructions** tab in the Google Sheet for a visual step-by-step guide.

---

## Technical Setup Guide

### 1. Google Sheet Structure

**Sheet URL:** https://docs.google.com/spreadsheets/d/13cRZ8XGvLTqLVytn3B24FOoKyWy7p3_tUNeVwZ86bx8/edit

| Column | Header | Purpose | Example |
|--------|--------|---------|---------|
| A | `ISO` | ISO 3166-1 Alpha-3 code (auto-generated, hidden) | `ETH` |
| B | `Country` | Country name — **dropdown** from reference list | `Ethiopia` |
| C | `Crisis_1` | Primary crisis type — **dropdown** | `Drought` |
| D | `Status_1` | Framework status for Crisis_1 — **dropdown** | `Activated` |
| E | `Crisis_2` | Secondary crisis — **dropdown** (leave empty if none) | `Floods` |
| F | `Status_2` | Framework status for Crisis_2 — **dropdown** | `Endorsed` |
| G | `Crisis_3` | Third crisis — **dropdown** (leave empty if none) | |
| H | `Status_3` | Framework status for Crisis_3 — **dropdown** | |
| I | `Map_Color` | Auto-calculated — drives choropleth color (hidden) | `Activated` or `Mixed statuses` |

> **Note:** Column A (ISO) and Column I (Map_Color) are hidden. Column A is auto-populated via VLOOKUP from a hidden "Ref" sheet. Column I uses a formula to determine the choropleth color: if all crises share one status, it shows that status; if a country has crises with different statuses, it shows "Mixed statuses". Users only need to select from dropdowns in columns B-H.

#### Status values (dropdown, must be exact):
- `Activated`
- `Endorsed`
- `In development`

#### Crisis type values (dropdown):
- `Cholera` → uses Bacteria icon
- `Cold wave`
- `Conflict`
- `Cyclone`
- `Drought`
- `Dry spells` → uses Drought icon
- `Earthquake`
- `Epidemic`
- `Famine`
- `Fire`
- `Flash flood`
- `Floods`
- `Heatwave`
- `Heavy rain`
- `Insect infestation`
- `Landslide mudslide`
- `Locust infestation`
- `Plague` → uses Locust infestation icon
- `Snow avalanche`
- `Snowfall`
- `Storm`
- `Storm surge`
- `Technological disaster`
- `Tornado`
- `Tropical cyclones` → uses Cyclone icon
- `Tsunami`
- `Violent wind`
- `Volcano`

#### Publish the Sheet
The Google Sheet must be published to web for the GitHub Action to fetch it:
1. In Google Sheets: **File → Share → Publish to web**
2. Select **Map Data** and **Comma-separated values (.csv)**
3. Click **Publish**

---

### 2. Datawrapper Configuration

#### Step 1: Create New Visualization
1. Go to https://app.datawrapper.de
2. Click **New Map** → **Choropleth Map**
3. Select basemap: **World**

#### Step 2: Add Your Data
1. Link the external CSV URL: `https://raw.githubusercontent.com/UN-OCHA/aa_map_interactive_version_BDU/refs/heads/main/aa_map_data.csv`
2. In the **Match** tab:
   - Select column `ISO` as the match key
   - Datawrapper should match it to **ISO-Codes**
   - Verify all 26 countries match (green checkmarks)
3. Select `Map_Color` as the **values** column (this drives the choropleth color)

#### Step 3: Visualize — Colors

In the **Appearance** tab:

1. **Color scale:** Select **Categories** (not continuous)
2. You should see 4 categories auto-detected:
   - `Activated` → **#004987** (dark blue — UN Blue ramp step 2)
   - `Endorsed` → **#0074B7** (medium blue — UN Blue ramp step 3)
   - `In development` → **#64BDEA** (light blue — UN Blue ramp step 5)
   - `Mixed statuses` → assign a color or pattern for countries with multiple different statuses
3. Set countries without data to **#E6E6E6** (light gray)

These 3 blues are from the official OCHA UN Blue colour ramp: https://brand.unocha.org/document/281801#/colours/colours/colour-ramps

#### Step 4: Visualize — Tooltips

1. Scroll to **Tooltips** section in the Annotate tab
2. Enable **Show tooltips**
3. Click **Customize tooltips**

**Tooltip TITLE field:**
```
<b style="font-size:15px">{{ country }}</b>
```

**Tooltip BODY field:**

Each crisis line shows a **color-tinted** OCHA humanitarian icon (matching the status color) + the crisis name + a color-coded status badge. Crisis_2 and Crisis_3 only appear if they have a value.

The icons use the **CSS mask technique** — a `<div>` with `background-color` set to the status color and `-webkit-mask-image`/`mask-image` pointing to the SVG URL. This renders the SVG shape filled with the status color. (Note: the CSS `filter:` approach does NOT work in Datawrapper because the colon triggers the ternary parser.)

The full template is ~6800 characters. Due to its length, it's best to copy it directly from the Datawrapper tooltip editor (chart `8Kssm`). The structure is:

- **Line 1** (always shown): `<div>` with mask-image icon (URL from crisis_1 ternary) + background-color (from status_1 ternary) + crisis name + status pill
- **Line 2** (conditional): `{{ crisis_2 ? CONCAT(...) : '' }}` — same pattern using CONCAT with inline ternaries for icon URL and color
- **Line 3** (conditional): `{{ crisis_3 ? CONCAT(...) : '' }}` — same pattern

**Icon URL ternary pattern** (used inside mask-image):
```
{{ crisis_1 == 'Cholera' ? '.../Bacteria.svg' : crisis_1 == 'Drought' ? '.../Drought.svg' : ... }}
```

**Color ternary pattern** (used in background-color and pill background):
```
{{ status_1 == 'Activated' ? '#004987' : status_1 == 'Endorsed' ? '#0074B7' : '#64BDEA' }}
```

> **Note:** Each crisis line shows a color-tinted icon + name + a color-coded status pill. The tooltip uses `status_1`, `status_2`, `status_3` (per-crisis) — not a single framework status. For crisis types not in the ternary, the icon div will be empty but the name and badge still display.

#### Step 5: Annotate

1. **Title:** `OCHA-facilitated anticipatory action portfolio`
2. **Description:** (leave empty or add a subtitle)
3. **Legend:** Make sure the color legend shows all 4 categories: Activated, Endorsed, In development, Mixed statuses
4. **Map labels:** Optionally add labels for orientation (e.g., major oceans/regions)
5. **Source:** `OCHA | Updated from Google Sheet`

#### Step 6: Publish & Embed

1. Click **Publish** to generate the embed code
2. Copy the **iframe embed code** or the **Datawrapper URL**
3. In your Drupal site (unocha.org/anticipatory-action):
   - Use the Datawrapper embed module
   - Paste the embed code/URL
4. The map auto-updates from the GitHub CSV — no manual republish needed

---

### 3. Icon Mapping Reference

| Crisis Type | Icon File | Note |
|---|---|---|
| Cholera | `Bacteria.svg` | Maps to bacteria icon |
| Cold wave | `Cold-wave.svg` | |
| Conflict | `Conflict.svg` | |
| Cyclone | `Cyclone.svg` | |
| Drought | `Drought.svg` | |
| Dry spells | `Drought.svg` | Maps to drought icon |
| Earthquake | `Earthquake.svg` | |
| Epidemic | `Epidemic.svg` | |
| Famine | `Famine.svg` | |
| Fire | `Fire.svg` | |
| Flash flood | `Flash-flood.svg` | |
| Floods | `Flood.svg` | Icon file is singular, crisis name is plural |
| Heatwave | `Heatwave.svg` | |
| Heavy rain | `Heavy-rain.svg` | |
| Plague | `Locust-infestation.svg` | Maps to locust infestation icon |
| Storm | `Storm.svg` | |
| Tropical cyclones | `Cyclone.svg` | Maps to cyclone icon |
| Tsunami | `Tsunami.svg` | |
| Volcano | `Volcano.svg` | |

Base URL pattern: `https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/{IconFile}`

All icons are from the "Disasters, hazards and crises" family: https://github.com/UN-OCHA/humanitarian-icons-2026

---

### 4. Color Scheme (OCHA Brand — UN Blue Colour Ramp)

Source: https://brand.unocha.org/document/281801#/colours/colours/colour-ramps

| Framework Status | Hex Color | Ramp Position | Visual |
|---|---|---|---|
| Activated | `#004987` | Step 2 (dark) | Dark blue |
| Endorsed | `#0074B7` | Step 3 (medium) | Medium blue |
| In development | `#64BDEA` | Step 5 (light) | Light blue |
| Mixed statuses | *(user-assigned)* | -- | Countries with multiple different statuses |
| No data | `#E6E6E6` | -- | Light gray |


---

### 5. Automated Pipeline (GitHub Actions)

A GitHub Actions workflow keeps the CSV in sync with the Google Sheet. Datawrapper reads from the raw GitHub CSV URL and auto-refreshes. Same approach as the [INSARAG map](https://github.com/UN-OCHA/insarag_exercises_datawrapper_BDU).

**Schedule:** Twice daily (6:00 AM and 6:00 PM UTC) + manual trigger

**Manual trigger:**
1. Go to https://github.com/UN-OCHA/aa_map_interactive_version_BDU/actions
2. Click **"Sync Google Sheet to CSV"** in the left sidebar
3. Click **"Run workflow"** → Select `main` → Click **"Run workflow"**

**Workflow file:** `.github/workflows/sync-sheet-to-datawrapper.yml`

---

### 6. Simpler Tooltip Alternative

If the CSS mask icon approach above is too complex, here's a simpler version that shows crisis names with status badges but without icons:

```
<div style="margin-top:4px">{{ crisis_1 }} <span style="padding:2px 6px;border-radius:100px;font-size:10px;color:#fff;background:{{ status_1 == 'Activated' ? '#004987' : status_1 == 'Endorsed' ? '#0074B7' : '#64BDEA' }}">{{ status_1 }}</span></div>
{{ crisis_2 ? CONCAT('<div style="margin-top:4px">', crisis_2, ' <span style="padding:2px 6px;border-radius:100px;font-size:10px;color:#fff;background:', status_2 == 'Activated' ? '#004987' : status_2 == 'Endorsed' ? '#0074B7' : '#64BDEA', '">', status_2, '</span></div>') : '' }}
{{ crisis_3 ? CONCAT('<div style="margin-top:4px">', crisis_3, ' <span style="padding:2px 6px;border-radius:100px;font-size:10px;color:#fff;background:', status_3 == 'Activated' ? '#004987' : status_3 == 'Endorsed' ? '#0074B7' : '#64BDEA', '">', status_3, '</span></div>') : '' }}
```

---

### 7. Troubleshooting

- **Countries not matching:** Check that the country name in the Ref sheet matches what Datawrapper expects. The ISO code is auto-generated from the country name via VLOOKUP.
- **Icons not showing in tooltip:** The crisis name must match one of the values in the tooltip template (case-sensitive). Use the dropdown to ensure correct values.
- **Colors wrong:** Ensure status values are exactly `Activated`, `Endorsed`, or `In development` (use dropdown). The map color is driven by the `Map_Color` column (auto-calculated, hidden column I).
- **Sheet not updating:** Make sure the sheet is published to web (File → Share → Publish to web).
- **ISO column or Ref sheet missing:** These are hidden by design. Right-click on column headers or sheet tabs to unhide if needed for maintenance.
- **New crisis type needs icon:** Add the crisis name to the dropdown, then add a matching condition to the tooltip template in Datawrapper.
- **GitHub Actions failing:** Check the [Actions tab](https://github.com/UN-OCHA/aa_map_interactive_version_BDU/actions) for error logs. Common issues: Google Sheet not published to web, or repository permissions.
- **Map not updating after Sheet edit:** Wait up to 12 hours for the next scheduled sync, or trigger a manual run from the Actions tab.

---

## Project Owner

**Sofia Fiammenghi** — Associate Humanitarian Affairs Officer | Climate Innovation Section

## Maintained by

**OCHA Brand and Design Unit (BDU)**
- Team: ochavisual@un.org
- Focal point: Javier Cueto (cuetoj@un.org)
