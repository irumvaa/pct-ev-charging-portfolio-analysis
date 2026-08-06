# PCT EV Charging Portfolio Analysis

Analysis of a solar + grid hybrid EV charging station in Eastern Kentucky, built during a summer 2026 internship with the Mountain Association's Energy Team. Covers Aug 1, 2025 to Aug 1, 2026 (a full year) of 15-minute interval SolarEdge monitoring data.

**Live interactive dashboard:** https://irumvaa.github.io/pct-ev-charging-portfolio-analysis/

## What's in this repo

| File | What it is |
|---|---|
| `index.html` | Interactive dashboard (also the GitHub Pages site): monthly production vs. consumption, a full-year charging calendar heatmap, time-of-day and day-of-week breakdowns (with medians alongside means), session-length distribution, a sortable session log, and a panel-capacity sizing model for reaching net zero |
| `EKY_EV_Charging_Portfolio_Analysis.xlsx` | Full workbook: raw combined data, charging-session detection, daily/monthly rollups, time-of-day and day-of-week analysis, a capacity-sizing sheet, and a dashboard tab, all formula-driven |
| `EKY_EV_Raw_15min_Data_Combined.xlsx` | Just the combined raw 15-minute readings, no derived columns |

## Key findings

- 367 charging sessions over 366 days; solar covers 39.7% of total consumption on a moment-matched basis (78.5% on a simple annual-totals basis, see Methodology for why these differ)
- Charging is concentrated in daylight hours (about 80% morning/afternoon) and on weekends (Saturday alone is about 19% of all sessions)
- Session length is heavily skewed: median session is 0.75 hrs, well under half the mean of 1.48 hrs, pulled up by a handful of very long sessions
- The net-zero gap (7,466 kWh/year) is almost entirely a winter shortfall (Dec through Feb), not a demand-side problem
- Reaching annual net zero through panel capacity alone would take about 5.5 kW of additional capacity (roughly a 27% increase on the current ~20 kW system); even then, 5 of 13 months stay net-negative and moment-matched self-sufficiency only reaches 43.3%, since a lot of the added production still gets exported rather than used directly

## Methodology

**Source data.** The SolarEdge export came as weekly CSV files, 15-minute readings of Production, To Building, To Grid, Consumption, From Solar, and From Grid (all in kW). There is no separate "EV charger" column; the EV load has to be inferred from the data.

**Combining the data.** All weekly files were read, deduplicated on timestamp, sorted, and filtered to Aug 1, 2025 through Aug 1, 2026. Result: 35,132 rows at 15-minute intervals, with only one gap (the spring DST change).

**Detecting a charging session.** Any interval where Consumption exceeds 10 kW is flagged as EV charging. Consecutive flagged intervals are grouped into one session. This was built as a chain of simple formulas in the workbook (a flag column, a session-start flag, a running counter, and a session ID), so every session's start time, end time, duration, and energy use is a live formula, not a hard-coded value. It was cross-checked against a Python prototype and both agreed on 367 sessions.

**Time-of-day buckets.** Morning is 6am to noon, Afternoon is noon to 6pm, Evening is 6pm to 10pm, Night is 10pm to 6am. Applied consistently to every interval and every session.

**Two different "self-sufficiency" numbers, on purpose.** Self-Sufficiency (39.7%) counts only solar actually used the moment it was made, matched to the same 15-minute interval as the consumption it offset. Solar Offset (78.5%) is the simpler total production ÷ total consumption ratio used in solar sales quotes and net-metering billing, which ignores timing entirely. Both are shown side by side throughout, since they answer different questions.

**Medians alongside means.** Session duration and related figures are right-skewed (a few very long sessions pull the mean well above the typical case), so medians are shown next to averages in the Time of Day and Day of Week breakdowns, and as a dedicated KPI.

**Panel capacity sizing for net zero.** A capacity-only model (no batteries, no demand shifting): existing production is scaled up uniformly until annual production equals annual consumption, which gives the additional kW needed. The model also reports what that sizing does and doesn't achieve, since annual net zero on paper doesn't mean every month is positive, or that most consumption is met by solar at the moment it's needed.

**Workbook structure.** Raw Data (15-min) holds the combined readings plus the session-detection helper columns. Charging Sessions holds one row per detected session, each column a formula keyed off the session ID. Daily Summary and Monthly Summary roll those up by day and month. Time of Day Analysis and Day of Week Analysis aggregate the sessions by those two dimensions, including medians. Capacity Sizing holds the net-zero panel-sizing model. The Portfolio Dashboard tab pulls KPIs and charts from all of the above.

**Validation.** Every formula in the workbook (over 254,000 of them) was recalculated and checked for errors before being treated as final, and the totals were spot-checked by hand against a handful of individual rows.

**The dashboard.** Built on top of the finished workbook: it aggregates the same Daily Summary, Monthly Summary, Charging Sessions, Time of Day/Day of Week, and Capacity Sizing tables into a JSON object embedded directly in the page, so it needs no server to run. Charts use Chart.js; the calendar heatmap and the session log's search/sort are hand-built in plain JavaScript.
