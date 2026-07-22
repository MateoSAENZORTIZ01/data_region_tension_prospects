# ⚡ Flexibility Target Identification Pipeline

### Identifying High-Value Industrial Enterprises for Flexibility Energy Aggregation

> **Project Goal**
>
> Build a transparent, data-driven methodology for identifying industrial enterprises that are the best candidates for flexibility aggregation services, using only publicly available Dutch electricity grid data and geographical information available during the project.

---

# Table of Contents

1. Executive Summary
2. Business Problem
3. Project Objectives
4. Overall Methodology
5. Data Sources
6. Data Preparation
7. Congestion Analysis
8. Flexibility Valorization Model
9. Geographic Prioritization
10. Industrial Enterprise Identification
11. Outputs Produced
12. Assumptions & Limitations
13. Repository Structure
14. Future Improvements

---

# Executive Summary

Electricity congestion has become one of the largest barriers to connecting new customers, renewable generation, and electrification projects in the Netherlands.

The objective of this project was **not** to estimate technical flexibility directly.

Instead, we wanted to answer a business question:

> **Which industrial enterprises are most valuable to approach for flexibility aggregation?**

Rather than starting from companies themselves, we worked backwards:

```text
Grid congestion
        │
        ▼
Supply Areas
        │
        ▼
Postcodes
        │
        ▼
Industrial Zones
        │
        ▼
Industrial Enterprises
```

The final output is a ranked list of industrial enterprises located inside the highest-value congestion areas.

---

# Business Problem

A flexibility aggregator creates value by helping the electricity grid during periods of congestion.

However:

- Not every company can provide flexibility.
- Not every congested region has enough industrial load.
- Not every industrial area has equal commercial value.

The challenge therefore becomes:

> **Where should business development teams focus first?**

To answer this, we first ranked **locations**, and only afterwards searched for companies located there.

---

# Project Objectives

The project had four primary objectives.

| Objective | Description |
|------------|-------------|
| Measure congestion | Quantify where the Dutch electricity grid is under the greatest stress |
| Rank regions | Prioritize areas where flexibility has the highest potential value |
| Locate industry | Identify industrial areas inside those regions |
| Produce targets | Generate a list of enterprises for commercial outreach |

---

# Overall Methodology

```text
                    Grid Capacity Data
                           │
                           ▼
                Clean & Standardize Data
                           │
                           ▼
              Compute Grid Stress Metrics
                           │
                           ▼
              Composite Valorization Score
                           │
                           ▼
                 Rank Supply Areas
                           │
                           ▼
                  Map to PC6 Postcodes
                           │
                           ▼
                  Aggregate to PC4 Areas
                           │
                           ▼
          Identify Industrial Business Parks
                           │
                           ▼
         Search Industrial Enterprises
                           │
                           ▼
             Final Commercial Target List
```

---

# Data Sources

The project intentionally relied only on datasets available during development.

## 1. Grid Capacity Map Data

The primary dataset came from the Dutch grid operators' capacity map documentation. It includes postcode-level congestion information, supply-area capacities, TenneT congestion areas, and planned grid expansion projects.

The documentation describes five core datasets:

| Dataset | Purpose |
|----------|----------|
| `congestie_pc6` | Congestion status per 6-digit postcode |
| `voedingsgebieden` | Supply area capacities and queues |
| `tennetgebieden` | TenneT congestion areas |
| `tennetcongestie` | Substation congestion mapping |
| `projecten` | Planned grid expansion projects |

These datasets include:

- Available transport capacity
- Required transport capacity
- Queue sizes
- Congestion colour codes
- Grid operator information
- Planned reinforcement projects

---

# Data Preparation

The notebook begins by loading five CSV datasets and standardizing Dutch column names into English for readability and consistency across the analysis. This creates a common schema for subsequent calculations and joins.

Key preparation steps included:

- Loading all raw CSV files
- Renaming Dutch columns into English
- Checking missing values
- Validating data types
- Verifying relationships between datasets
- Preparing tables for merging and analysis

---

# Understanding Grid Congestion

The available data contains several indicators describing congestion.

## Capacity

How much electricity the network can transport.

## Required Capacity

How much electricity customers require during peak periods.

## Queue

Capacity already requested but waiting for connection.

## Congestion Colour Codes

| Colour | Meaning |
|---------|----------|
| Transparent | No congestion |
| Yellow | Early congestion |
| Orange | Significant congestion |
| Red | Severe congestion |
| -1 | No information available |

---

# Flexibility Valorization Model

The notebook develops a composite score to quantify how attractive a location is for flexibility services.

## Metric 1 — Capacity Utilization

Measures how heavily existing transport capacity is being used.

```text
Capacity Utilization

= Required Capacity / Available Capacity
```

Higher values indicate the grid is operating closer to its limits.

---

## Metric 2 — Queue Intensity

Measures the relationship between queued connection requests and available capacity.

```text
Queue Intensity

= Queued Capacity / Available Capacity
```

Large queues indicate strong unmet demand.

---

## Metric 3 — Capacity Gap

Measures the absolute shortage in transport capacity.

```text
Capacity Gap

= Required Capacity − Available Capacity
```

Positive values indicate demand exceeds available transport capability.

---

## Metric 4 — Grid Stress Index

A composite indicator combining multiple congestion dimensions into a single normalized measure.

The notebook applies logarithmic scaling to reduce the influence of extreme outliers while preserving meaningful ranking differences.

---

## Project Relief Discount

Planned reinforcement projects can reduce the long-term attractiveness of flexibility opportunities.

Therefore, the methodology applies a discount to locations expected to receive future grid upgrades.

---

## Composite Valorization Score

All normalized metrics are combined into one weighted score.

Conceptually:

```text
Final Score

=
Weighted Capacity Utilization
+
Weighted Queue Intensity
+
Weighted Capacity Gap
+
Weighted Grid Stress
−
Project Relief Discount
```

This score represents the estimated commercial value of providing flexibility in each supply area.

---

# Tier Classification

Rather than using fixed thresholds, the notebook assigns priority tiers using quantiles.

Example:

| Tier | Meaning |
|------|----------|
| Tier 1 | Highest priority |
| Tier 2 | High priority |
| Tier 3 | Medium priority |
| Tier 4 | Lower priority |

---

# Geographic Prioritization

The analysis progressively translates infrastructure-level scores into actionable geographic units.

```text
Supply Areas
      │
      ▼
PC6 Postcodes
      │
      ▼
PC4 Regions
      │
      ▼
Industrial Areas
      │
      ▼
Industrial Enterprises
```

This hierarchy enables business development teams to move from infrastructure metrics to commercially relevant locations.

---

# Industrial Enterprise Identification

Once the priority regions were established, the focus shifted from geography to companies.

The process consisted of:

1. Selecting Tier 1 and high-scoring regions.
2. Identifying industrial estates within those regions.
3. Searching for industrial enterprises located in those areas.
4. Creating a target list for flexibility aggregation outreach.

This final stage bridges technical grid analysis with commercial opportunity identification.

---

# Outputs Produced

The analysis generates several outputs that support commercial targeting.

| Output | Purpose |
|---------|---------|
| Supply Area Scores | Ranked supply areas with congestion metrics |
| Postcode Scores | PC6-level mapping of supply areas |
| Regional Summary | Aggregated geographic statistics |


Next step -> | Industrial Enterprise List | Final commercial targets |

---

# Why This Approach Works

Instead of asking:

> *Which companies might be flexible?*

we asked:

> *Where is flexibility worth the most?*

Only after determining where flexibility creates the highest value did we identify industrial enterprises operating in those locations.

This significantly reduces the search space and aligns commercial efforts with the regions where grid flexibility is likely to deliver the greatest benefit.

---

# Assumptions & Limitations

The methodology intentionally reflects only the information available during the project.

Limitations include:

- Public grid data represents a snapshot in time.
- Company operational flexibility cannot be inferred from location alone.
- Industrial presence does not guarantee flexibility potential.
- Planned grid reinforcement projects may reduce future congestion.
- Additional company-level consumption data would improve prioritization.

---

# Repository Structure

```text
project/
│
├── data/
│   ├── congestie_pc6.csv
│   ├── voedingsgebieden.csv
│   ├── tennetgebieden.csv
│   ├── tennetcongestie.csv
│   └── projecten.csv
│
├── flexibility_valorization_analysis.ipynb
│
├── outputs/
│   ├── supply_area_scores.csv
│   ├── postcode_scores.csv
│   ├── regional_summary.csv
│   └── enterprise_targets.csv
│
└── README.md
```

---

# Future Improvements

Potential enhancements include:

- Integrating company electricity consumption profiles.
- Including sector-specific flexibility potential.
- Incorporating smart meter data.
- Adding market revenue estimates for flexibility services.
- Refreshing congestion data periodically.
- Automating company discovery through business registries and GIS tools.

---

# Conclusion

This project established a transparent and reproducible workflow for transforming publicly available Dutch electricity grid congestion data into a prioritized pipeline of industrial enterprise opportunities for flexibility aggregation.

By moving systematically from **grid infrastructure**, to **geographic prioritization**, and finally to **enterprise identification**, the methodology provides a practical bridge between technical grid analysis and commercial decision-making while remaining fully traceable to the datasets available during the project.