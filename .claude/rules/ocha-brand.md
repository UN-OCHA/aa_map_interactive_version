# OCHA Visual Identity & Brand Guidelines

## Brand Portal
https://brand.unocha.org

## Primary Color: OCHA Blue
- **OCHA Blue (primary):** `#009EDB`
- Brand guide: https://brand.unocha.org/document/281801#/colours/colours

## UN Blue Colour Ramp
Source: https://brand.unocha.org/document/281801#/colours/colours/colour-ramps

Full ramp (dark to light):
```
#002E6E -> #004987 -> #0074B7 -> #009EDB (primary) -> #64BDEA -> #C5DFEF -> #E3EDF6
```

| Step | Hex | Usage Example |
|------|-----|---------------|
| 1 | `#002E6E` | Darkest blue |
| 2 | `#004987` | Dark blue (e.g., "Activated" status) |
| 3 | `#0074B7` | Medium blue (e.g., "Endorsed" status) |
| 4 | `#009EDB` | OCHA Blue primary |
| 5 | `#64BDEA` | Light blue (e.g., "In development" status) |
| 6 | `#C5DFEF` | Very light blue |
| 7 | `#E3EDF6` | Lightest blue |

## Neutral Colors
- No data / empty states: `#E6E6E6` (light gray)

## Typography
Source: https://brand.unocha.org/document/281801#/fonts/fonts

**Primary font:** Roboto (Google Font)

| Element | Weight | Size |
|---------|--------|------|
| Body text | 400 (Regular) | 18px |
| Heading 1 | 900 (Black) | 36px |
| Heading 2 | 700 (Bold) | 22px |
| Heading 3 | 700 (Bold) | 18px |
| Heading 4 | 700 (Bold) | 16px |
| Large display | 900 (Black) | 72px |
| Links | 700 (Bold) | 18px |
| Quotes | 400 (Regular) | 24px |
| Primary buttons | 500 (Medium) | 13px, uppercase |
| Secondary/Tertiary buttons | 400 (Regular) | 13px |

Google Fonts import: `https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700;900&display=swap`

## OCHA Humanitarian Icons
- Repository: https://github.com/UN-OCHA/humanitarian-icons-2026
- SVG base URL: `https://raw.githubusercontent.com/UN-OCHA/humanitarian-icons-2026/main/svg/{filename}.svg`
- Icon families: Disasters/hazards/crises, Sectors, People, Activities, etc.
- Icons are SVG format, black by default, can be styled with CSS

### Common Crisis Icon Mappings
Some crisis names don't match their icon filenames directly:

| Crisis Name | Icon File | Reason |
|-------------|-----------|--------|
| Cholera | `Bacteria.svg` | Cholera is a bacterial disease |
| Dry spells | `Drought.svg` | Visually similar hazard |
| Floods | `Flood.svg` | Crisis name is plural, icon file is singular |
| Plague | `Locust-infestation.svg` | Plague in AA context refers to locust plague |
| Tropical cyclones | `Cyclone.svg` | Same hazard, different name |

## Web Presence
- Main site: https://www.unocha.org
- CMS: Drupal
- Datawrapper module available for embedding charts/maps
- Anticipatory Action page: https://www.unocha.org/anticipatory-action

## Accessibility
- Always ensure sufficient color contrast
- Provide text alternatives for visual data (tooltips, legends)
- Use semantic HTML where possible
