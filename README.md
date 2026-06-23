# 🟢 Goodlife BD Intelligence Dashboard

**Live site:** https://goodlife-pharmacy-bd.github.io/bd-dashboard/

> Built for the Goodlife Pharmacy Business Development team to map and analyse pharmacy presence across Kenya and Uganda.

---

## What it does

An interactive BD intelligence dashboard showing:

- **🇰🇪 Kenya** — 8 former provinces as clickable regions
- **🇺🇬 Uganda** — 4 administrative regions as clickable regions
- Both maps displayed side by side, colour-coded by location density (purple intensity scale)
- **3-level drill-down:** Region → Category (Flagship / Classic / Neighbourhood) → Individual mall with Maps link
- **Area drill-down** — within large regions like Nairobi, filter further by area (Karen, Westlands, Kilimani etc.)
- **Status traffic light filter** — filter the map by 🟢 Goodlife Present / 🟡 Opportunity / 🔴 Competitor Dominated
- **Mall size bars** — proportional size indicator (sqm) on each mall card where data is available
- **Goodlife Format Mix tracker** — banner showing Flagship / Classic / Neighbourhood format deployment across mall tiers
- **↓ Download Report** — export the current dashboard as a self-contained HTML file
- **📄 Export PDF** — capture and print the dual map view for presentations

---

## How to update data

The dashboard is driven entirely by **`Market_Share_Malls_v2.xlsx`**. When the file is updated:

1. Open the live site: https://goodlife-pharmacy-bd.github.io/bd-dashboard/
2. Click **↑ Upload Excel** in the action bar
3. Select the updated `Market_Share_Malls_v2.xlsx`
4. The dashboard rebuilds instantly — all stats, maps and drill-downs update
5. Click **↓ Download Report** to save the updated version as a new HTML file
6. To make the update permanent on the live site, replace `index.html` in this repo with the downloaded file

---

## Excel file structure

The dashboard reads 4 sheets from the workbook:

| Sheet | Key Columns |
|---|---|
| **Flagships** | Rank · Mall · Tier · County/City · Approx. Size (sqm) · Market/Area · Pharmacy Chain · Pharmacy Format · Status · Google Maps Link |
| **Classics** | Rank · Mall · Tier · County/City · Approx. Size (sqm) · Pharmacies · Pharmacy Format · Status |
| **Neigbourhoods** | Rank · Mall Name · Tier · County/City · Approx. Size (sqm) · Pharmacy Chain · Pharmacy Format · Status |
| **Uganda** | Mall Name · Tier · Country · City · Area · Pharmacy Partner · Status |

> Note: The `Neigbourhoods` sheet name spelling (one 'u') must match exactly for the parser to find it.

---

## Status legend

| Status | Meaning |
|---|---|
| 🟢 Green | Goodlife is present and in a strong position |
| 🟡 Yellow | Shared with competitor, or opportunity — Goodlife not yet secured |
| 🔴 Red | Competitor dominated — no Goodlife presence |

---

## Mall classification

| Category | Former name | Description |
|---|---|---|
| Flagship | Tier 1 | Premium, large-format malls — highest priority |
| Classic | Tier 2 | Secondary malls — mid-market positioning |
| Neighbourhood | Tier 3 | Community and emerging mall locations |

---

## County → Province mapping (Kenya)

The dashboard automatically maps `County/City` values to Kenya's 8 former provinces:

| Province | Counties / Cities included |
|---|---|
| Nairobi | Nairobi, Kilimani |
| Central | Kiambu, Nyeri, Muranga, Kirinyaga, Nyandarua, Ruiru, Thika, Ruaka |
| Coast | Mombasa, Kilifi, Kwale, Lamu, Taita-Taveta, Tana River, Malindi, Watamu, Diani |
| Eastern | Meru, Embu, Machakos, Kitui, Makueni, Marsabit, Isiolo, Nanyuki, Athi River |
| North Eastern | Garissa, Wajir, Mandera |
| Nyanza | Kisumu, Kisii, Migori, Siaya, Homa Bay, Nyamira |
| Rift Valley | Nakuru, Eldoret, Uasin Gishu, Kericho, Naivasha, Narok, Kajiado, Kitale, Ngong |
| Western | Kakamega, Bungoma, Busia, Vihiga |

---

## City → Region mapping (Uganda)

| Region | Cities / Areas included |
|---|---|
| Central | Kampala, Entebbe, Wakiso, Mukono |
| Eastern | Jinja, Mbale, Tororo, Busia |
| Northern | Gulu, Lira, Arua, Kitgum |
| Western | Mbarara, Fort Portal, Kabale, Masaka |

---

## Repository structure

```
bd-dashboard/
├── index.html                        # Full self-contained dashboard (HTML + CSS + JS + data)
├── .github/
│   └── workflows/
│       └── deploy.yml                # Auto-deploy to GitHub Pages on every push to main
└── README.md                         # This file
```

---

## Auto-deploy

Every push to `main` triggers GitHub Actions which automatically redeploys the live site within ~60 seconds. No build tools or dependencies required — the dashboard is a single self-contained HTML file.

---

## Access

| Resource | URL |
|---|---|
| Live dashboard | https://goodlife-pharmacy-bd.github.io/bd-dashboard/ |
| GitHub organisation | https://github.com/goodlife-pharmacy-bd |
| Repository | https://github.com/goodlife-pharmacy-bd/bd-dashboard |

---

*Built for Goodlife Pharmacy BD team · June 2026*
