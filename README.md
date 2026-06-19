# Household Energy Consumption Analysis
### Visualization & Outlier Detection with PCA, t-SNE, and DBSCAN

> **Course:** INFO411 – Machine Learning and Data Mining  
> **Institution:** University of Otago, New Zealand  
> **Language:** Julia (Pluto.jl notebook)

---

## Project Overview

This project analyzes the power consumption patterns of **241 households** measured every 30 minutes over a single day (4 March 2009). The goal is to understand behavioral archetypes in energy usage and identify households with anomalous consumption patterns — insights directly applicable to **smart grid optimization and energy demand management**.

---

## Key Questions

1. What are the dominant energy consumption patterns across households?
2. Can we identify distinct behavioral clusters (e.g. working families, night-shift workers)?
3. Which households are true anomalies, and why?
4. Does **Dynamic Time Warping (DTW)** outperform Euclidean distance for time series energy data?

---

## Methods & Pipeline

```
Raw TSV Data (241 households × 48 half-hour intervals)
        │
        ▼
  Data Preparation
  ├── Matrix X  (48 × 241) — full half-hourly resolution
  └── Matrix Xa (5 × 241)  — aggregated into 5 daily periods
        │
        ▼
  Visual Statistics
  ├── Per-household time series plots (interactive slider)
  ├── Bar chart: average consumption by time block
  └── Boxplot: distribution across all households per period
        │
        ▼
  Dimensionality Reduction (on both X and Xa)
  ├── PCA  — captures main variation gradient
  └── t-SNE — reveals local structure & separations
        │
        ▼
  Distance Computation
  ├── Euclidean Distance (241 × 241 matrix)
  └── Dynamic Time Warping / DTW (241 × 241 matrix)
        │
        ▼
  DBSCAN Clustering & Outlier Detection
  ├── Parameter tuning via k-distance plots
  ├── eps=3, minPts=6 (Euclidean) → 26 outliers
  ├── eps=4, minPts=6 (DTW)       →  8 outliers
  └── Evaluation via Silhouette Coefficients
```

---

## Key Findings

### Consumption Patterns
| Pattern Type | Description | Example Households |
|---|---|---|
| Dual-peak | Morning + evening spikes (typical working family) | HH 182 |
| Evening-heavy | Activity concentrated after 16:00 | HH 107 |
| Morning-heavy | Activity concentrated before noon | HH 165 |
| All-day | Sustained usage (WFH / caregiving) | HH 118 |

Peak consumption across all households: **16:00–21:00**

### PCA vs t-SNE
- **PCA on X**: Strong variation gradient along PC1 — total daily kWh is the primary driver
- **Period aggregation (Xa)** smooths out differences, reducing cluster visibility in both methods

### DTW vs Euclidean for Outlier Detection

| Metric | Outliers Found | Mean Silhouette |
|---|---|---|
| Euclidean | 26 | 0.34 |
| **DTW** | **8** | **0.58** |

**DTW outperforms Euclidean** by reducing false noise classifications by **69%**, capturing the true temporal shape of consumption curves rather than just point-to-point distances.

### 8 Identified Anomalous Households (DTW)
Households `[16, 30, 56, 157, 159, 174, 183, 194]` — flagged due to:
- Unusual multi-peak patterns
- Abnormally high overnight/late-night demand
- Consumption profiles shifted outside standard household behavior

---

## Tech Stack

| Tool | Purpose |
|---|---|
| `Julia` + `Pluto.jl` | Interactive notebook environment |
| `CSV.jl`, `DataFrames.jl` | Data loading & manipulation |
| `MultivariateStats.jl` | PCA |
| `TSne.jl` | t-SNE dimensionality reduction |
| `Distances.jl` | Euclidean pairwise distance matrix |
| `DynamicAxisWarping.jl` | DTW pairwise distance matrix |
| `Clustering.jl` | DBSCAN clustering |
| `StatsPlots.jl`, `Plots.jl` | Visualization |
| `Statistics.jl` | Silhouette coefficient evaluation |

---

## How to Run

1. Install [Julia](https://julialang.org/downloads/) and [Pluto.jl](https://plutojl.org/)
2. Clone this repository
3. Place `households-24hrs.tsv` in the same directory
4. Open Pluto and run the notebook:

```julia
using Pluto
Pluto.run()
```

5. Navigate to `Assignment_1_Putri_Dita_Elaheebocus_Ruqayya.jl`

> ⚠️ The notebook is **interactive** — use the slider to explore individual household consumption profiles.
