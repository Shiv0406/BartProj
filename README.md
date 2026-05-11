# BART Post-COVID Ridership Recovery Analysis

## Overview
This project investigates which BART (Bay Area Rapid Transit) stations recovered 
from pandemic-era ridership loss and what neighborhood characteristics predict 
recovery. Using 96 months of ridership data across 48 stations, I built a 
data pipeline, tested demographic hypotheses, and applied k-means clustering 
to project ridership performance at 13 proposed BART expansion stations.

## Research Question
Did BART recover to pre-pandemic ridership levels, and which station and 
neighborhood characteristics predict which stations bounced back?

## Data Sources
- **BART Monthly Ridership Data** (2018–2025): Origin-destination matrices 
  downloaded from bart.gov. 96 Excel files totaling ~240,000 rows.
- **BART 2024 Station Profile Study**: Rider income demographics by station.
- **U.S. Census ACS 5-Year Estimates (2023)**: 
  - Table B01003: Population by ZIP Code (density calculation)
  - Table B19013: Median Household Income by ZIP Code

## Methodology

### Phase 1 — Data Pipeline
Built a reusable `load_and_clean_month()` function to handle BART's 
inconsistent Excel formats across 96 files. Produced a unified DataFrame 
of 240,000+ rows with origin, destination, trips, and month columns.

### Phase 2 — Exploration & Validation
Validated data integrity with sanity checks against raw files. Identified 
and removed phantom trips (same origin and destination) and malformed rows.


### Phase 3 — Recovery Analysis
Defined a pre-COVID baseline as the average of January 2018 – February 2020. 
Calculated a recovery index (current trips ÷ baseline trips) for each station 
each month. Built a system-wide recovery curve and station-level rankings.

<img width="476" height="219" alt="Screenshot 2026-05-11 at 3 04 15 PM" src="https://github.com/user-attachments/assets/fa47f55f-1ea2-401f-98a6-ccc5d099bf4a" />

**Key finding:** Suburban stations (e.g., Antioch) significantly outperformed 
downtown SF financial district stations (e.g., Montgomery) in recovery.
<img width="476" height="219" alt="Screenshot 2026-05-11 at 3 04 35 PM" src="https://github.com/user-attachments/assets/df1edc6a-5671-40f4-a2d8-a074f51611e9" /><img width="476" height="304" alt="Screenshot 2026-05-11 at 3 05 38 PM" src="https://github.com/user-attachments/assets/87303251-000a-422f-9a40-b70dacb81612" />



### Phase 4 — Demographic Layer
Joined rider income data from BART's Station Profile Study and population 
density from Census ACS to test two hypotheses:

- **Hypothesis 1:** High-income stations would recover worse due to remote work
- **Hypothesis 2:** High-density urban stations would retain more riders

**Finding:** Neither income nor population density meaningfully predicted 
recovery — both hypotheses were rejected. This suggests recovery is driven 
by station-specific factors beyond neighborhood demographics alone.
<img width="476" height="304" alt="Screenshot 2026-05-11 at 3 06 05 PM" src="https://github.com/user-attachments/assets/0b86d3fb-bdf5-4a56-b806-216e8c6d3b74" />
<img width="476" height="304" alt="Screenshot 2026-05-11 at 3 06 45 PM" src="https://github.com/user-attachments/assets/e99b1e49-45f0-4568-ab59-dcf0715d9518" />

### Phase 5 — Expansion Station Analysis
Applied k-means clustering (k=3, validated by elbow method) to group existing 
stations by population density and median household income. Three clusters emerged:

| Cluster | Profile | Avg Recovery Index |
|---------|---------|-------------------|
| 0 | Suburban Middle | 0.360 |
| 1 | Dense Urban | 0.303 |
| 2 | Wealthy Low Density | 0.315 |

**Why k-means clustering?**

k-means clustering was selected because it's unsupervised, interpretable, and works well for small datasets. 
I only had 48 stations so simpler was better. 


**Why three clusters?**

Using the Elbow method, I plotted WCSS against k and found the curve flattened after 3.
<img width="614" height="469" alt="image" src="https://github.com/user-attachments/assets/8b6d39a0-1344-468a-9531-d57daf9a1287" />




Proposed stations across three corridors were scaled and assigned to their 
nearest demographic cluster to project performance:

- **VTA Silicon Valley (4 stations):** 0.303–0.360 projected recovery
- **East Bay — Livermore & Irvington (2 stations):** 0.315 projected recovery
- **Contra Costa/eBART (7 stations):** 0.360 projected recovery

<img width="476" height="378" alt="Screenshot 2026-05-11 at 3 07 11 PM" src="https://github.com/user-attachments/assets/47f12ef5-eec7-4fe0-8c24-d3ac008f9fbb" />
<img width="476" height="322" alt="Screenshot 2026-05-11 at 3 07 36 PM" src="https://github.com/user-attachments/assets/b079ddd3-1459-497b-8972-36216a636ce6" />


**Notable finding:** As more stations open along the SR-4 corridor, Antioch's 
ridership dominance may diminish as trips distribute across new stations — 
similar to how Castro Valley transitioned from a terminus to a mid-line station.

## Key Visualizations
- System-wide recovery curve (2018–2025)
- Station-level recovery rankings
- Recovery index vs. high-income rider share (null result)
- Recovery index vs. population density (null result)
- K-means cluster scatter plot: existing vs. proposed stations
- Projected recovery bar chart by corridor

## Tools & Technologies
- **Python** — pandas, matplotlib, scikit-learn, requests
- **Jupyter Lab** — analysis and narrative notebook
- **Census API** — programmatic data retrieval
- **SQLite** — in-memory SQL queries for station aggregations
- **Git/GitHub** — version control

## Limitations
- Demographics from 2024 applied to 2018–2023 recovery period introduces 
  a temporal mismatch. Gentrification in Oakland and Mission District stations 
  may have shifted demographic profiles since 2018.
- Proposed station projections assume demographic similarity predicts 
  performance. Given that demographics showed weak predictive power for 
  existing stations, these should be interpreted as benchmarks, not forecasts.
- K-means clustering on 48 stations is a small sample. Cluster assignments 
  should be interpreted with caution.

## Repository Structure

