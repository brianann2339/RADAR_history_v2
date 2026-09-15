# RADAR_history_v2 — 急診戰情圖歷史回溯 2020–2024

Retrospective emergency-department crowding dashboard (Taipei Tzu Chi Hospital ED, 2020–2024), rebuilt so that OCCUPANCY, EDWIN, NEDOCS and READI follow the formulas of the live 急診戰情室 web app.

**Page:** https://brianann2339.github.io/RADAR_history_v2/

## What is here

| File | Content |
|---|---|
| `index.html` | Dashboard: hourly trend, month/hour heatmaps, EDWIN peaks, COVID validation, 死亡 × 壅塞 tab |
| `master_hourly.csv` | One row per hour, 2020-01-01 to 2024-12-31: census, triage counts, boarders, and the four indices with NEDOCS components |
| `monthly_summary.csv`, `monthly/` | Monthly summaries and per-month hourly files |
| `png/`, `overview_2020_2024.png`, `validation_covid.png` | Static figures |
| `surge_months.json` | Months flagged as surges |
| `mortality_results.json` | Aggregated crowding × in-hospital mortality model results |

Everything in this repository is aggregated. No patient names, chart numbers or encounter numbers are included, and any cell with fewer than 5 deaths is suppressed.

## Formulas

- OCCUPANCY = census / 40 × 100
- EDWIN = Σ(triage weight × patients) / (attendings × max(1, 40 − boarders)); attendings 4 at 10:00–22:00, otherwise 3
- NEDOCS = 85.8 × census/40 + 600 × boarders/691 + 13.4 × resus + 0.93 × longest-admit hours + 5.64 × longest-waiting-room hours − 20
- READI = (census/40 + arrivals in the last hour / physician capacity) × mean triage weight

Inputs that retrospective records cannot provide use the live code's own fallbacks or the owner's stated rule: longest-admit hours = 1.5 × third quartile of boarder stay; waiting-room hours = 0 (no physician-seen time in the records); resus = patients waiting for ICU; boarders counted from 1 h after registration; physician capacity from the duty roster.

2025 is not included because the 2025 records have no in-hospital triage level, so EDWIN and READI cannot be computed.

## Mortality models

Logistic regression with year-month fixed effects and day-clustered standard errors. Models that cannot be estimated (too few events for the number of parameters, or a numerically failed fit) are shown as "not estimable" instead of numbers. This applies to the triage 4–5 subgroup (45 deaths).

Earlier version (not updated): https://brianann2339.github.io/RADAR_history/
