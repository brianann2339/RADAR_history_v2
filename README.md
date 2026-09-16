# RADAR_history_v2 — 急診戰情圖歷史回溯 2020–2025

Retrospective emergency-department crowding dashboard (Taipei Tzu Chi Hospital ED, 2020–2025), rebuilt so that OCCUPANCY, EDWIN, NEDOCS and READI follow the formulas of the live 急診戰情室 web app.

**Page:** https://brianann2339.github.io/RADAR_history_v2/

## What is here

| File | Content |
|---|---|
| `index.html` | Dashboard: hourly trend, month/hour heatmaps, EDWIN peaks, COVID validation, 死亡 × 壅塞 tab |
| `master_hourly.csv` | One row per hour, 2020-01-01 to 2025-12-31 (52,608 rows): census, triage counts, boarders, and the four indices with NEDOCS components |
| `monthly_summary.csv`, `monthly/` | Monthly summaries and per-month hourly files |
| `png/`, `overview_2020_2025.png`, `validation_covid.png` | Static figures |
| `surge_months.json` | Months flagged as surges |
| `mortality_results.json` | Aggregated crowding × in-hospital mortality model results |
| `5min/` | 5-minute series, one JSON per calendar month (72 files, ~250 KB each) — fetched by the page only when you zoom in |

The chart has four resolutions: monthly, daily, hourly and 5-minute. The 5-minute series (631,296 points) is not embedded in `index.html`; when the visible range is under about 35 days the page fetches the months it needs from `5min/` and switches back to hourly when you zoom out. A copy of the HTML opened from a local file cannot fetch them and stays on hourly.

Everything in this repository is aggregated. No patient names, chart numbers or encounter numbers are included, and any cell with fewer than 5 deaths is suppressed.

## Formulas

- OCCUPANCY = census / 40 × 100
- EDWIN = Σ(triage weight × patients) / (attendings × max(1, 40 − boarders)); attendings 4 at 10:00–22:00, otherwise 3
- NEDOCS = 85.8 × census/40 + 600 × boarders/691 + 13.4 × resus + 0.93 × longest-admit hours + 5.64 × longest-waiting-room hours − 20
- READI = (census/40 + arrivals in the last hour / physician capacity) × mean triage weight

Inputs that retrospective records cannot provide use the live code's own fallbacks or the owner's stated rule: longest-admit hours = 1.5 × third quartile of boarder stay; waiting-room hours = 0 (no physician-seen time in the records); resus = patients waiting for ICU; boarders counted from 1 h after registration; physician capacity from the duty roster.

2025 is included. `急診資料@2025.xlsx` has no in-hospital triage level of its own, so it is taken from a separate hospital extract keyed by encounter number; over 2020-2024 that source reproduces the recorded triage level for 99.75% of 311,398 comparable visits, and it covers 100% of 2025 visits. The duty roster also runs through 2025, so READI uses real rostered physician capacity for every year.

## Mortality models

Logistic regression with year-month fixed effects and day-clustered standard errors, over 2020-2025. Models that cannot be estimated (too few events for the number of parameters, or a numerically failed fit) are shown as "not estimable" instead of numbers. This applies to the triage 4–5 subgroup (45 deaths).

Earlier version (not updated): https://brianann2339.github.io/RADAR_history/
