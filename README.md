# HYDR1130 Web — SCS (NRCS) Peak Discharge Calculator

A single-file web version of the SCS-method peak runoff computation from **HYDR1130**,
the Peak Runoff program in LaDOTD's **hydr2009** package (LA DOTD Hydraulics Manual,
1987, Chapter 1).

Everything is in `index.html` — no build step, no dependencies. Open it in a browser
and it works.

## Inputs
| Field | English | Metric |
|---|---|---|
| Drainage Area | acres (max 2,000) | hectares (max 800) |
| Hydraulic Length | ft | m |
| Curve Number | 1–100 | 1–100 |
| 24-Hour Rainfall | in | mm |
| Watershed Slope | % (0.1–30) | % (0.1–30) |
| Peak Adjustment Factor | – | – |

## Multiple return periods / stations
Below the single calculator is a batch table — one line per computation, like
HYDR1130's station input. It comes pre-seeded with 2/5/10/25/50/100-yr rows copying
the calculator's watershed values, so typically you only type the 24-hr rainfall for
each return period. Rows can be added, duplicated, and deleted; **Compute All Rows**
fills the peak-discharge column (hover a result for full precision, Tc, and runoff
depth), and **Download CSV** exports the table.

## How it computes (same algorithm as HYDR1130)
1. `S = 1000/CN − 10`, `Ia = 0.2S` (TR-55 Ch. 2 runoff curve number method)
2. Watershed lag `= L^0.8 (S+1)^0.7 / (1900 √Y)`; `Tc = lag / 0.6` (TR-55 lag method)
3. SCS **Type II** 24-hr storm distributed in unit periods `D = Tc/7.5`;
   incremental runoff from the CN equation
4. Increments convolved with the SCS triangular unit hydrograph
   (peak factor 484, `Tp = D/2 + 0.6 Tc`, recession `1.67 Tp`)
5. Peak of the composite hydrograph × Fp = reported peak discharge

The implementation was verified against 82 batch runs of the original
`hydr1130.exe` across the full input range; all results match the program's
printed precision (typically < 0.01 % difference).

## Deploy on GitHub + Vercel
1. Create a new GitHub repository and push this folder:
   ```
   git init
   git add index.html README.md
   git commit -m "SCS peak discharge calculator (HYDR1130 web)"
   git branch -M main
   git remote add origin https://github.com/<your-user>/<repo>.git
   git push -u origin main
   ```
2. On [vercel.com](https://vercel.com) → **Add New… → Project** → import the repo.
3. Framework preset: **Other**. No build command, no output directory — just deploy.
   Vercel serves `index.html` at the root automatically.

(GitHub Pages works too: repo → Settings → Pages → deploy from `main` branch root.)

## Disclaimer
Independent re-implementation for convenience; not an official LaDOTD product.
For LaDOTD project submittals, verify results against the official HYDR1130 program.
