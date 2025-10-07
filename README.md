# Divvy Tripdata 2024 – Ride Behavior Analysis

## Problem Statement

Divvy, Chicago’s bike-sharing system, provides millions of trips each year, serving both **casual** and **member** users.  
The goal of this project is to understand how **ride behaviors differ** between these user types across **time** (daily, weekly, and seasonal patterns) and **space** (stations and routes).  

### Key research questions:
- How do **casual** and **member** users differ in ride duration, frequency, and temporal trends?  
- Which **stations** and **routes** are most popular for each user group?  
- What **seasonal or weekly demand patterns** can inform **operational planning** and **marketing strategies**?  

By building a queryable and normalized PostgreSQL database, analysts can efficiently extract insights that drive data-driven decisions for Divvy’s operations, fleet allocation, and customer engagement.

---

## Methodology

### Data Acquisition & Storage
- Source: 12 monthly Divvy trip data files (`202401` – `202412`) for the year 2024.  
- Environment: PostgreSQL + R.  
- Each CSV file was imported into its own **monthly staging table** under the schema `divvy`.

### Data Integration & Normalization
- All monthly tables were combined and **deduplicated** into a unified **`fact_trips`** table.  
- The schema was normalized to **Third Normal Form (3NF)** with the following dimension tables:
  - `dim_station` – unique station metadata  
  - `dim_date` – calendar table for time-based analytics  
  - `dim_member_type` – user type (“member”, “casual”)  
  - `dim_bike_type` – equipment category (“classic”, “electric”, “docked”)  

### Database Design & Optimization
- Constraints: Primary keys, foreign keys, and `CHECK` constraints to enforce data quality.  
- Indexes: Optimized for frequent analytical queries (by date, user type, station, route, duration).  
- Views: Analyst-friendly access layers including  
  - `vw_trips_enriched` – complete fact view with dimension joins  
  - `vw_daily_counts`, `vw_weekly_counts` – time-series summaries  
  - `vw_top_start_stations`, `vw_route_counts` – spatial patterns  
- Materialized views for faster aggregation (e.g., monthly summaries).  
- Helper SQL functions:  
  - `norm_station_name()` – cleans station names  
  - `route_key()` – generates route identifiers

### Data Analysis & Visualization
- SQL used for aggregation, trend analysis, and quality checks.  
- R used for visualization and exploratory data analysis:
  - Daily and weekly ride trends by user type  
  - Ride duration distributions  
  - Top start stations and most popular routes  
  - Hourly and weekday ride patterns  
- Packages: `DBI`, `RPostgres`, `dplyr`, `ggplot2`, `lubridate`, `readr`, `glue`.

---

## Results

### Usage Patterns
- **Members** consistently contributed the majority of rides, with steadier activity across the year.  
- **Casual** riders showed pronounced **seasonality**, peaking during warmer months (May–September).  

### Temporal Trends
- Daily and weekly analyses revealed **commuter peaks** (weekday mornings and evenings) for members, versus **weekend leisure peaks** for casual users.  
- Average trip duration was notably longer for casual users, consistent with recreational usage.  

### Spatial Insights
- A small number of **hub stations** dominated total rides, particularly in downtown and lakefront areas.  
- Popular **routes** often connected tourist zones to transit hubs, highlighting high-traffic corridors for rebalancing strategies.  

### Data Quality
- Deduplication and constraints ensured unique `ride_id` entries.  
- Negative or null durations were effectively filtered out by validation rules.  

---

## Key Findings & Impact

- **User segmentation** is essential: member rides reflect predictable commuting behavior, while casual rides are event- and weather-driven.  
- **Operational optimization**: Station-level demand data supports bike rebalancing and station expansion planning.  
- **Marketing opportunities**: Seasonal campaigns can target casual users during high-demand months, while loyalty initiatives can deepen member retention.  
- **Analytical foundation**: The PostgreSQL + R pipeline enables repeatable, scalable analytics as new monthly data becomes available.

---

## Project Stack

| Component | Tool / Technology | Purpose |
|------------|------------------|----------|
| Database | PostgreSQL | Storage, normalization, querying |
| Data Load | R (`DBI`, `RPostgres`, `readr`) | Automated CSV ingestion |
| Modeling | SQL (3NF schema) | Fact-dimension structure |
| Analysis | SQL Views, R (`ggplot2`, `dplyr`) | EDA and visualization |
| Visualization | ggplot2 | Time series, distribution, and spatial plots |

---

## How to Reproduce

1. Load 2024 CSVs into `/resources/data/` and update connection details in the R script.  
2. Run schema and table creation SQL scripts.  
3. Execute R loading script to populate monthly tables.  
4. Run normalization SQL to build dimensions and `fact_trips`.  
5. Refresh materialized views for summary analytics.  
6. Use provided R EDA scripts for visualization and trend analysis.

---

## Next Steps

- Integrate **real-time station data** (capacity, availability).  
- Add **geospatial coordinates** to `dim_station` for route mapping.  
- Develop **predictive models** (Prophet, ARIMA) to forecast demand and station usage.  
- Deploy **interactive dashboards** (e.g., Power BI or R Shiny) for ongoing monitoring.
