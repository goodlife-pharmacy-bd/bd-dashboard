# HANDOFF.md — Goodlife BD Dashboard
## Full context for Claude Code to pick up this project

---

## What this is
A single-file (`index.html`) interactive BD intelligence dashboard for Goodlife Pharmacy.
- Live: https://goodlife-pharmacy-bd.github.io/bd-dashboard/
- Auto-deploys to GitHub Pages on every push to `main`
- No build tools, no dependencies to install — everything is self-contained in `index.html`
- External CDNs used: xlsx.js (Excel parsing), html2canvas (PDF export)

---

## Architecture
Everything lives in `index.html`:
- HTML structure
- All CSS (CSS variables, responsive grid)
- All JavaScript (data, map rendering, modal logic, upload parser)
- Both map SVG paths embedded directly (Kenya provinces + Uganda regions)
- Goodlife logo embedded as base64
- All 142 store locations hardcoded in a `STORES` JS object

---

## Data structure
The `STORES` object in the JS has this shape:
```js
STORES = {
  kenya: {
    "Nairobi": {
      Flagship:      [ { mall, area, pharmacy, format, status, maps, size }, ... ],
      Classic:       [ ... ],
      Neighbourhood: [ ... ]
    },
    "Coast": { ... },
    // 8 provinces total: Nairobi, Coast, Central, Eastern, North Eastern,
    //                    Nyanza, Rift Valley, Western
    // Note: some Kenya cities map to these provinces:
    //   Mombasa/Diani/Malindi/Watamu → Coast
    //   Kisumu/Kisii → Nyanza  
    //   Nakuru/Eldoret/Narok/Kericho/Naivasha/Kajiado → Rift Valley
    //   Kakamega → Western
    //   Meru/Athi River/Nanyuki/Thika → Eastern
    //   Kiambu/Nyeri/Ruiru → Central
  },
  uganda: {
    "Central":  { Flagship: [...], Classic: [...], Neighbourhood: [...] },
    "Western":  { ... },
    "Northern": { Flagship:[], Classic:[], Neighbourhood:[] },  // no data yet
    "Eastern":  { Flagship:[], Classic:[], Neighbourhood:[] },  // no data yet
  }
}
```

Each store entry:
```js
{
  mall: "Garden City Mall",        // mall name
  area: "Thika Road",              // area/neighbourhood within city
  pharmacy: "Goodlife",            // pharmacy chain present
  format: "Goodlife Flagship",     // Goodlife store format deployed
  status: "Green",                 // Green | Yellow | Red
  maps: "https://...",             // Google Maps link (search URL or precise pin)
  size: "60000",                   // approx sqm (string, may be empty)
  city: "Kampala"                  // Uganda only
}
```

---

## Key design decisions made in the original session

### Mall classification (NOT Tier 1/2/3)
The user explicitly renamed:
- ~~Tier 1~~ → **Flagship**
- ~~Tier 2~~ → **Classic**
- ~~Neighbourhood~~ → **Neighbourhood** (note: source Excel sheet is spelled "Neigbourhoods" — one 'u' — the parser must match this exactly)

### Status traffic light
- 🟢 **Green** = Goodlife present and dominant
- 🟡 **Yellow** = Shared, opportunity, or competitor nearby
- 🔴 **Red** = Competitor dominated, no Goodlife

### Uganda status values in the Excel
The Uganda sheet uses: `Green`, `Yellow`, `Priority`, `Maybe`
- `Priority` and `Maybe` should both map to **Yellow** in the dashboard
- The parser should normalise these on upload

### Duplication resolution rule
Some malls appear in both Classics and Neigbourhoods sheets in the Excel.
Rule: **use the Pharmacy Format column to determine correct classification**.
If format says "Goodlife Classic" → Classic. If format says "Goodlife Flagship" → Flagship.
When city is same AND mall name variant differs slightly → treat as different branches.

### Goodlife Format Mix tracker
The banner strip at the top shows Goodlife store formats cross-tabulated by mall category.
It counts only stores where `pharmacy` contains "goodlife" (case-insensitive).
Format detection: check `format` field for "Goodlife Flagship", "Goodlife Classic", "Goodlife Neighbourhood".

---

## Maps
### Kenya
- SVG viewBox: `0 0 650 600`
- Province shapes: real polygon paths extracted from `Kenya_Provinces_Vectorized.svg`
- Nairobi is rendered as a small circle (cx=255, cy=400, r=12) — too small for a polygon at this scale
- Province shapes use `data-region` and `data-country="kenya"` attributes
- Colour: purple intensity scale based on total location count per province

### Uganda
- SVG viewBox: `0 0 685 661`
- Region shapes: 4 path elements extracted from `Uganda_regions_traced.svg`
- Outer border path also embedded separately
- Lake Victoria islands embedded as small paths with no fill
- Same purple intensity colour scale as Kenya
- Region shapes use `data-region` and `data-country="uganda"` attributes
- Regions: Northern, Eastern, Central, Western

---

## County → Province mapping (Kenya parser)
```js
const C2P = {
  nairobi: "Nairobi", kilimani: "Nairobi",
  ruiru: "Central", thika: "Central", ruaka: "Central",
  kiambu: "Central", nyeri: "Central", muranga: "Central",
  mombasa: "Coast", kilifi: "Coast", watamu: "Coast",
  diani: "Coast", malindi: "Coast",
  meru: "Eastern", "athi river": "Eastern", machakos: "Eastern",
  embu: "Eastern", nanyuki: "Eastern",
  garissa: "North Eastern", wajir: "North Eastern", mandera: "North Eastern",
  kisumu: "Nyanza", kisii: "Nyanza", migori: "Nyanza", siaya: "Nyanza",
  nakuru: "Rift Valley", eldoret: "Rift Valley", "uasin gishu": "Rift Valley",
  kericho: "Rift Valley", naivasha: "Rift Valley", narok: "Rift Valley",
  kajiado: "Rift Valley", kitale: "Rift Valley", ngong: "Rift Valley",
  kakamega: "Western", bungoma: "Western", busia: "Western",
  "ex nairobi": "Eastern"
}
```

## City → Region mapping (Uganda parser)
```js
const C2R_UG = {
  kampala: "Central", entebbe: "Central", wakiso: "Central", mukono: "Central",
  jinja: "Eastern", mbale: "Eastern", tororo: "Eastern",
  gulu: "Northern", lira: "Northern", arua: "Northern",
  mbarara: "Western", "fort portal": "Western", kabale: "Western"
}
```

---

## Excel upload parser
When user uploads `Market_Share_Malls_v2.xlsx`, the parser:
1. Reads 4 sheets: `Flagships`, `Classics`, `Neigbourhoods` (note spelling), `Uganda`
2. For Kenya sheets: maps `County/City` → province using C2P
3. Builds the STORES object and **replaces** the hardcoded data entirely
4. Recalculates all stats, recolours the map, rebuilds format tracker

Key Excel columns per sheet:
- Flagships: `Mall`, `County/City`, `Market / Area`, `Pharmacy Chain`, `Pharmacy Format`, `Status`, `Google Maps Link`, `Approx. Size (sqm)`
- Classics: `Mall`, `County/City`, `Pharmacies`, `Pharmacy Format`, `Status`
- Neigbourhoods: `Mall Name`, `County/City`, `Pharmacy Chain`, `Pharmacy Format`, `Status`
- Uganda: `Mall Name`, `Tier`, `City`, `Area`, `Pharmacy Partner`, `Status`

---

## Known bugs fixed in this session
1. **JS SyntaxError** — unescaped apostrophe in `'N'hood'` string broke all interactivity. Fixed to `'Nhood'`.
2. **Variable declaration order** — `const modal/mbody/mtitle` were declared after the click event listeners that used them. Fixed by moving declarations before the hover/click binding section.
3. **Uganda map colours** — old version used green/yellow/pink per region. User wanted all-purple intensity scale matching Kenya. Fixed.
4. **Pages deployment** — fork initially served stale content. Fixed by switching to legacy Pages build type and force-pushing a cache-bust commit.

---

## What the user wants next (as of end of session)
- The click-to-open-modal is now working (just fixed and deployed)
- Possible next improvements discussed:
  - Further refinement of the Excel parser for edge cases
  - Uganda data is sparse (only Central/Western have data) — Northern/Eastern are empty
  - The `Priority`/`Maybe` Uganda status values need normalising in the parser
  - Personal repo (`csmallz96-droid/goodlife-bd-dashboard`) needs manual deletion by user

---

## GitHub details
- Org: `goodlife-pharmacy-bd`
- Repo: `goodlife-pharmacy-bd/bd-dashboard`
- Branch: `main`
- Pages: legacy build from `main` branch root
- Auto-deploy: `pages build and deployment` workflow triggers on every push to `main`
- To deploy: just push any change to `index.html` on `main`

---

## How to work on this locally
```bash
git clone https://github.com/goodlife-pharmacy-bd/bd-dashboard
cd bd-dashboard
# Open index.html in browser directly - no server needed
open index.html
# Edit index.html, then:
git add index.html
git commit -m "your change"
git push origin main
# Site updates automatically in ~60 seconds
```

*Last updated: June 2026 — Collins Rotich, Goodlife Pharmacy BD team*
