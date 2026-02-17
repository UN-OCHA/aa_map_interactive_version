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

- **Country colors** show framework status (3 levels)
- **Tooltips** show crisis types with [OCHA humanitarian icons](https://github.com/UN-OCHA/humanitarian-icons-2026)
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
3. Use the **dropdowns** to add/edit countries, framework status, and crisis types
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
| C | `Framework_Status` | One of 3 values — **dropdown**, drives choropleth color | `Activated` |
| D | `Crisis_1` | Primary crisis type — **dropdown** | `Flood` |
| E | `Crisis_2` | Secondary crisis — **dropdown** (leave empty if none) | `Drought` |
| F | `Crisis_3` | Third crisis — **dropdown** (leave empty if none) | |

> **Note:** Column A (ISO) is hidden and auto-populated via VLOOKUP from a hidden "Ref" sheet. Users only need to select from dropdowns in columns B-F.

#### Framework_Status values (dropdown, must be exact):
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
- `Flood`
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
3. Select `framework_status` as the **values** column

#### Step 3: Visualize — Colors

In the **Appearance** tab:

1. **Color scale:** Select **Categories** (not continuous)
2. You should see 3 categories auto-detected:
   - `Activated` → **#004987** (dark blue — UN Blue ramp step 2)
   - `Endorsed` → **#0074B7** (medium blue — UN Blue ramp step 3)
   - `In development` → **#64BDEA** (light blue — UN Blue ramp step 5)
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
```
<div style="margin:4px 0 6px 0;padding:4px 8px;border-radius:100px;display:inline-block;font-size:11px;color:#fff;background:{{ framework_status == 'Activated' ? '#004987' : framework_status == 'Endorsed' ? '#0074B7' : '#64BDEA' }}">{{ framework_status }}</div>
<br>
<div style="margin-top:6px">
{{ crisis_1 == 'Cholera' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Bacteria.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Cold wave' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cold-wave.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Conflict' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Conflict.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Cyclone' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Drought' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Dry spells' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Earthquake' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Earthquake.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Epidemic' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Epidemic.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Famine' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Famine.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Fire' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Fire.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Flash flood' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Flash-flood.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Flood' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Flood.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Heatwave' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Heatwave.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Heavy rain' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Heavy-rain.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Plague' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Locust-infestation.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Storm' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Storm.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Tropical cyclones' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Tsunami' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Tsunami.svg" height="22" style="vertical-align:middle"> ' : crisis_1 == 'Volcano' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Volcano.svg" height="22" style="vertical-align:middle"> ' : '' }}{{ crisis_1 }}
</div>
{{ crisis_2 ? CONCAT('<div style="margin-top:4px">', crisis_2 == 'Cholera' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Bacteria.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Cold wave' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cold-wave.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Conflict' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Conflict.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Cyclone' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Drought' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Dry spells' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Earthquake' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Earthquake.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Epidemic' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Epidemic.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Flood' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Flood.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Plague' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Locust-infestation.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Storm' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Storm.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Tropical cyclones' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_2 == 'Tsunami' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Tsunami.svg" height="22" style="vertical-align:middle"> ' : '', crisis_2, '</div>') : '' }}
{{ crisis_3 ? CONCAT('<div style="margin-top:4px">', crisis_3 == 'Cholera' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Bacteria.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Cold wave' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cold-wave.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Conflict' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Conflict.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Cyclone' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Drought' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Dry spells' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Drought.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Earthquake' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Earthquake.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Epidemic' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Epidemic.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Flood' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Flood.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Plague' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Locust-infestation.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Storm' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Storm.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Tropical cyclones' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Cyclone.svg" height="22" style="vertical-align:middle"> ' : crisis_3 == 'Tsunami' ? '<img src="https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/Tsunami.svg" height="22" style="vertical-align:middle"> ' : '', crisis_3, '</div>') : '' }}
```

> **Note:** The tooltip body maps each crisis type to its OCHA humanitarian icon. For crisis types not listed above, the name displays without an icon. Copy-paste the whole block — Datawrapper handles it fine.

#### Step 5: Annotate

1. **Title:** `OCHA-facilitated anticipatory action portfolio`
2. **Description:** (leave empty or add a subtitle)
3. **Legend:** Make sure the color legend shows all 3 categories: Activated, Endorsed, In development
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
| Flood | `Flood.svg` | |
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

If the conditional icon mapping above is too complex, here's a simpler version that shows crisis names without icons:

```
<div style="margin:4px 0 6px 0;padding:4px 8px;border-radius:100px;display:inline-block;font-size:11px;color:#fff;background:{{ framework_status == 'Activated' ? '#004987' : framework_status == 'Endorsed' ? '#0074B7' : '#64BDEA' }}">{{ framework_status }}</div>
<br>
<div style="margin-top:6px"><b>Hazards:</b></div>
<div>{{ crisis_1 }}</div>
{{ crisis_2 ? CONCAT('<div>', crisis_2, '</div>') : '' }}
{{ crisis_3 ? CONCAT('<div>', crisis_3, '</div>') : '' }}
```

---

### 7. Troubleshooting

- **Countries not matching:** Check that the country name in the Ref sheet matches what Datawrapper expects. The ISO code is auto-generated from the country name via VLOOKUP.
- **Icons not showing in tooltip:** The crisis name must match one of the values in the tooltip template (case-sensitive). Use the dropdown to ensure correct values.
- **Colors wrong:** Ensure `Framework_Status` values are exactly `Activated`, `Endorsed`, or `In development` (use dropdown).
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
