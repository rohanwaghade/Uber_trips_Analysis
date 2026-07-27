# Uber Trips Analytics — End-to-End Data Project

An end-to-end analytics project on a 50,000-row synthetic Uber trips dataset: data cleaning and exploratory analysis in Python, followed by an interactive Power BI dashboard.

---

## 📊 Project Overview

This project analyzes ride-hailing trip data across 6 cities to surface patterns in demand, revenue, cancellations, and driver performance. It's split into two stages:

1. **Python** — data cleaning, feature engineering, and exploratory analysis *(see `/python` — fill in with your notebook/script summary)*
2. **Power BI** — a 5-page interactive dashboard built on the cleaned dataset

---

## 🗂️ Dataset

- **Source file:** `uber_trips_dataset_50k.csv` — 50,000 trip records
- **Fields:** `trip_id`, `driver_id`, `rider_id`, `city`, `pickup_lat/lng`, `drop_lat/lng`, `distance_km`, `fare_amount`, `status`, `payment_method`, `pickup_time`, `drop_time`
- **Note:** the dataset contains only time-of-day (no calendar dates), so all time-based analysis is by hour/time-of-day rather than daily/monthly trends. Pickup/drop coordinates are randomly generated and not tied to real city geography — treat any map visual as illustrative, not literal.

---

## 🧹 Data Cleaning & Feature Engineering (Python)

Raw data issues fixed and features added, output as `uber_trips_cleaned_powerbi.csv`:

| Column added | Description |
|---|---|
| `fare_amount` | Converted from text (`"$10.71"`) to numeric |
| `trip_duration_min` | `drop_time − pickup_time` in minutes, corrected for midnight wraparound |
| `pickup_hour` | Hour of day (0–23) extracted from `pickup_time` |
| `time_of_day` | Morning / Afternoon / Evening / Night bucket |
| `fare_per_km` | `fare_amount ÷ distance_km` |
| `distance_bucket` | 0-2 / 2-5 / 5-10 / 10-15 / 15+ km |
| `is_completed`, `is_cancelled`, `is_noshow` | Status flags for easy DAX aggregation |

*(Add your Python EDA summary/charts here — e.g. key distributions, correlations, or anomalies found before moving to Power BI.)*

---

## 📈 Power BI Dashboard

Built on `uber_trips_cleaned_powerbi.csv`, the report has 5 pages:

### 1. Overview
- KPI cards: Total Trips · Completion Rate % · Revenue (Completed Only) · Avg Fare · Active Drivers
- Donut — Trips by status (Completed / Cancelled / No-Show)
- Bar — Trips by city
- Column — Trips by pickup hour (0–23), showing the daily demand curve
- Bar — Trips by payment method

### 2. Revenue & Fare Analysis
- Line/column — Revenue by pickup hour
- Bar — Avg fare by city
- Bar — Avg fare per km by distance bucket
- Scatter — Distance (km) vs Fare ($), colored by city
- Matrix — City × Payment Method, total revenue, with conditional-formatting heatmap

### 3. Operations: Cancellations & No-Shows
- KPI cards: Cancellation Rate % · No-Show Rate %
- 100% stacked bar — Status by city
- 100% stacked bar — Status by time of day
- Table — Top 10 city/hour combinations ranked by cancellation count

### 4. Driver & Trip Patterns
- Bar — Top 10 drivers by total trips (Top N filter)
- Bar — Top 10 drivers by revenue (completed only)
- Column — Trips by distance bucket
- Column — Avg trip duration by city

### 5. Map View
- Filled map / bubble map by city (trip volume), small multiples by status
- *(Raw pickup/drop coordinates are not used for mapping — see data note above)*

### Key DAX Measures
```DAX
Total Trips = COUNTROWS(uber_trips_cleaned_powerbi)
Completion Rate % = DIVIDE([Completed Trips], [Total Trips])
Cancellation Rate % = DIVIDE([Cancelled Trips], [Total Trips])
No-Show Rate % = DIVIDE([No-Show Trips], [Total Trips])
Revenue (Completed Only) = CALCULATE([Total Revenue], uber_trips_cleaned_powerbi[status] = "Completed")
Avg Fare per km = AVERAGE(uber_trips_cleaned_powerbi[fare_per_km])
Trips per Driver = DIVIDE([Total Trips], [Active Drivers])
Revenue per Driver = DIVIDE([Revenue (Completed Only)], [Active Drivers])
```
Full measure list: [`PowerBI_Dashboard_Guide.md`](./PowerBI_Dashboard_Guide.md)

### Design
Each page uses a custom dark-themed canvas background (`/powerbi_backgrounds`) with a consistent visual identity — a glowing GPS-route motif echoing the pickup→drop trip concept, a shared status color palette (green/coral/gold) across every chart, and a distinct accent color per page:

| Page | Accent |
|---|---|
| Overview | Green `#33D17A` |
| Revenue | Gold `#F2B84B` |
| Operations | Coral `#FF6B57` |
| Drivers | Violet `#9B8CFF` |
| Map | Blue `#4C9EFF` |

---

## 📁 Repo Structure
```
├── data/
│   ├── uber_trips_dataset_50k.csv          # raw dataset
│   └── uber_trips_cleaned_powerbi.csv      # cleaned & feature-engineered
├── python/
│   └── ...                                 # cleaning/EDA notebook or script
├── powerbi/
│   ├── uber_trips_dashboard.pbix           # Power BI report file
│   └── PowerBI_Dashboard_Guide.md          # build guide & DAX reference
├── powerbi_backgrounds/
│   ├── 00_master_background.png
│   ├── 01_overview_background.png
│   ├── 02_revenue_background.png
│   ├── 03_cancellations_background.png
│   ├── 04_driver_background.png
│   └── 05_map_background.png
└── README.md
```

## 🛠️ Tools Used
- **Python** — pandas, numpy *(add matplotlib/seaborn etc. if used)*
- **Power BI Desktop** — data modeling, DAX, visualization
- Custom SVG/PNG canvas backgrounds for dashboard theming

## 🚀 How to Use
1. Clone this repo
2. Open `powerbi/uber_trips_dashboard.pbix` in Power BI Desktop
3. If prompted, point the data source to `data/uber_trips_cleaned_powerbi.csv`
4. Explore the 5 report pages via the tabs at the bottom

## 🔑 Key Insights
*(Fill in once you've reviewed the final dashboard — e.g. peak demand hours, which city has the highest cancellation rate, top drivers by revenue vs. volume, etc.)*

## 📝 Data Notes / Limitations
- No calendar dates — all trends are time-of-day only
- Pickup/drop coordinates are synthetic and not tied to real geography
- Payment method is near-evenly distributed (~25% each), suggesting it may be randomly assigned rather than behavior-driven

---
