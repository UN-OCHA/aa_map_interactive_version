# Datawrapper Knowledge

## General Workflow

Datawrapper is used for maps and charts embedded on unocha.org via the Drupal Datawrapper module.

### Data Pipeline (proven pattern)
1. Google Sheet (user-friendly, with dropdowns) serves as the data entry point
2. GitHub Actions fetches the sheet as CSV on a schedule and commits to the repo
3. Datawrapper reads from the raw GitHub CSV URL and auto-refreshes
4. No Datawrapper API tokens needed — just the raw CSV link

Reference implementation: https://github.com/UN-OCHA/insarag_exercises_datawrapper_BDU

### GitHub Actions Workflow Template
```yaml
name: Sync Google Sheet to CSV
concurrency:
  group: {project}-sheet-sync
  cancel-in-progress: true

on:
  schedule:
    - cron: "0 6,18 * * *"   # adjust frequency as needed
  workflow_dispatch:

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Download Google Sheet CSV
        run: curl -L "{GOOGLE_SHEET_GVIZ_URL}" -o {output_file}.csv
      - name: Commit and push if changed
        run: |
          git config user.name "github-actions"
          git config user.email "github-actions@github.com"
          git add {output_file}.csv
          git diff --cached --quiet || git commit -m "Update dataset"
          git push
```

### Google Sheet CSV Export URLs
- By sheet name: `https://docs.google.com/spreadsheets/d/{SHEET_ID}/gviz/tq?tqx=out:csv&sheet={SHEET_NAME}`
- By GID: `https://docs.google.com/spreadsheets/d/{SHEET_ID}/export?format=csv&gid={GID}`
- Published URL: `https://docs.google.com/spreadsheets/d/e/{PUBLISHED_ID}/pub?output=csv`

### Raw GitHub CSV URL Format
`https://raw.githubusercontent.com/UN-OCHA/{repo}/refs/heads/main/{file}.csv`

## Choropleth Maps

### Setup
- New Map -> Choropleth Map -> Select basemap (e.g., World)
- Match column must contain ISO 3166-1 Alpha-3 codes
- Use Categories (not continuous) for categorical data like status values

### Tooltips
- Variables in tooltips must be **lowercase** (Datawrapper auto-lowercases column headers)
- Use ternary syntax for conditionals: `{{ var == 'value' ? 'result' : 'else' }}`
- Use `CONCAT()` for conditional blocks (e.g., optional crisis_2/crisis_3 display)
- HTML is supported in tooltips: `<img>`, `<div>`, `<b>`, inline styles
- Images in tooltips: `<img src="URL" height="22" style="vertical-align:middle">`
- Pill badges: `border-radius:100px` with inline-block display

### External Data Sources
- Link external CSV in the "Upload Data" step
- Select "Use Datawrapper server" (CDN option) for reliability
- Datawrapper auto-refreshes from the URL periodically
- After 30 days without republishing, auto-refresh stops — republish to reset the timer
- The "Link external dataset" JSON option is for visualization properties, NOT for data

### Publishing & Embedding
- Published maps get a URL like: `https://datawrapper.dwcdn.net/{CHART_ID}/{VERSION}/`
- Embed via iframe code on Drupal using the Datawrapper module
- Republish from the "Publish & Embed" page to push updates
- Datawrapper connects to Google Sheets by URL/ID, NOT by tab name — renaming tabs is safe

## Symbol Maps
- Can display icons/markers at geographic coordinates
- Useful when you need icons visible on the map without clicking
- Cannot be combined with choropleth (choose one or the other)
