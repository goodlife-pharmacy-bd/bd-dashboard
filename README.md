# Goodlife BD Intelligence Dashboard

Live site: **https://csmallz96-droid.github.io/goodlife-bd-dashboard/**

## What it does
An interactive pharmacy BD intelligence dashboard showing:
- Kenya 8 provinces + Uganda 4 regions clickable maps
- 3-level drill-down: Region → Category (Flagship/Classic/Neighbourhood) → Individual malls with Maps links
- Status filtering: Goodlife Present (🟢) · Opportunity (🟡) · Competitor (🔴)
- Auto-updated stats cards per country

## How to update data
1. Update `Market_Share_Malls_v2.xlsx` with new mall data
2. Open the live site and click **↑ Upload Excel**
3. The dashboard updates instantly
4. Click **↓ Download Report** to save the updated version
5. Replace `index.html` in this repo to make it permanent

## File structure
- `index.html` — the entire dashboard (self-contained, no server needed)
- `.github/workflows/deploy.yml` — auto-deploy to GitHub Pages on every push

## Data format (Excel columns)
| Sheet | Key Columns |
|---|---|
| Flagships | Rank, Mall, Tier, County/City, Approx. Size, Market/Area, Pharmacy Chain, Pharmacy Format, Status, Google Maps Link |
| Classics | Rank, Mall, Tier, County/City, Approx. Size, Pharmacies, Pharmacy Format, Status |
| Neigbourhoods | Rank, Mall Name, Tier, County/City, Approx. Size, Pharmacy Chain, Pharmacy Format, Status |
| Uganda | Mall Name, Tier, Country, City, Area, Pharmacy Partner, Status |

## Status legend
- 🟢 Green = Goodlife present and dominant
- 🟡 Yellow = Shared, opportunity, or competitor present
- 🔴 Red = Competitor dominated

Built for Goodlife Pharmacy BD team · June 2026
