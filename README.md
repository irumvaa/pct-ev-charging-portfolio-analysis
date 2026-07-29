# PCT EV Charging Portfolio Analysis

Analysis of a solar + grid hybrid EV charging station in Eastern Kentucky, built during a summer 2026 internship with the Mountain Association's Energy Team. Covers Aug 1, 2025 – Jul 2, 2026 of 15-minute interval SolarEdge monitoring data.

**Live interactive dashboard:** https://irumvaa.github.io/pct-ev-charging-portfolio-analysis/

## What's in this repo

| File | What it is |
|---|---|
| `index.html` | Interactive dashboard (also the GitHub Pages site) — monthly production vs. consumption, a full-year charging calendar heatmap, time-of-day and day-of-week breakdowns, session-length distribution, and net-zero recommendations |
| `EKY_EV_Charging_Portfolio_Analysis.xlsx` | Full workbook: raw combined data, charging-session detection, daily/monthly rollups, time-of-day and day-of-week analysis, and a dashboard tab — all formula-driven |
| `EKY_EV_Raw_15min_Data_Combined.xlsx` | Just the combined raw 15-minute readings, no derived columns |
| `EKY_EV_Analysis_Methodology.docx` | Step-by-step writeup of how the data was combined, how charging sessions are detected, and how every sheet/chart was built — enough detail to redo it from scratch |

## Method, in short

A 15-minute interval counts as EV charging when total Consumption exceeds 10 kW; consecutive flagged intervals are grouped into one session. Every number in the workbook and dashboard traces back to the same combined raw dataset.

## Key findings

- 338 charging sessions over 336 days; solar covers about 76% of total consumption for the year
- Charging is concentrated in daylight hours (80% morning/afternoon) and on weekends (Saturday alone is ~20% of all sessions)
- The net-zero gap is almost entirely a winter shortfall (Nov–Feb), not a demand-side problem
