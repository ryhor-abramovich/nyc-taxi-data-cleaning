# 🚖 Data Cleaning and Exploratory Analysis of NYC Taxi Dataset (3.5M Records)

## 📌 Project Overview

This project focuses on data cleaning and exploratory data analysis (EDA) of raw historical New York City taxi trip records.

The main goal was to identify technical anomalies, inconsistent records, and data-quality issues, then build a clean dataset suitable for reliable statistical and business analysis.

The original dataset contained approximately **3.5 million taxi trips**. After applying the cleaning pipeline, approximately **2.8 million records** remained.

---

## 🛠️ Data Cleaning & Anomaly Detection

A multi-stage filtering pipeline was designed using logical constraints and domain-specific checks.

### Filtering Pipeline

| Feature | Cleaning Rule | Rationale |
| :--- | :--- | :--- |
| **Passenger Count** | `1 ≤ passenger_count ≤ 6` | Removes invalid passenger counts and obvious data-entry errors |
| **Trip Distance** | Remove trips with `trip_distance > 60` miles | Filters extreme distance values and potential GPS anomalies |
| **Fare Amount** | Keep `$2.5 ≤ fare_amount ≤ $200` | Removes negative, zero, and implausibly high fares |
| **Zero-Distance Trips** | Retain `0` miles when fare is between `$2.5` and `$70` | Allows potentially valid stopped/traffic-related trips to remain |
| **Trip Duration** | Keep trips between `1 minute` and `2 hours` | Removes implausibly short and excessively long trips |

### Zero-Distance Trips

Zero distance was **not automatically treated as an anomaly**.

Trips with `trip_distance = 0` were retained when the recorded fare was within a plausible range (`$2.5–$70`). This accounts for potentially valid situations where a taxi was stopped or barely moving while the meter was active.

### Result

The filtering pipeline removed approximately **19% of the original records**, reducing the dataset from approximately **3.5M to 2.8M trips**.

---

## 📊 Feature Engineering

Several features were created to support temporal and operational analysis:

- `hour` — hour of pickup
- `day` — day of the week
- `tip_ratio` — tip amount relative to fare amount
- `speed_mph` — estimated trip speed
- `revenue_per_minute` — revenue generated per operational minute

The main business-analysis metric was:

$$
\text{Tip Ratio} =
\frac{\text{Tip Amount}}{\text{Fare Amount}}\times100
$$

---

## 💰 Key Business Insight: Tip Patterns by Time

Hourly and weekday patterns were analyzed using pivot tables and **median tip ratios**.

### Key Findings

**Rush Hour Pattern — Monday to Friday, 16:00–19:00**

The highest median tip ratios were observed during weekday evening rush hours.

This pattern may be associated with weekday commuting and longer or more congested trips, although the dataset does not directly identify passenger purpose.

**Weekend Late-Night Pattern**

Friday and Saturday late-night trips also showed substantial tipping activity, but did not exceed the weekday evening pattern in median tip ratio.

---

## 🔍 Data Collection Bias: Cash Tips

An important limitation was identified during the exploratory analysis.

The dataset records tip information reliably for **credit-card transactions**, while cash tips are generally not recorded and appear as `0.0` in the raw data.

This creates a large number of artificial zero values in `tip_amount`.

Because of this limitation, **median tip ratios** were used as a more robust summary statistic. The median reduces the influence of these zero values and makes comparisons between time periods less sensitive to the missing cash-tip information.

> The median does not recover the unobserved cash tips; the analysis remains limited by the information captured in the source dataset.

---

## ⚡ Performance & Operational Metrics

The dataset contains more than **3.5 million records**, so the analysis was designed around vectorized operations and columnar Parquet data.

### Performance

- Processed large `.parquet` datasets using Pandas.
- Used vectorized Pandas/NumPy operations for filtering and feature engineering.
- Used PyArrow / FastParquet for Parquet data access.

### Operational Metrics

Two additional indicators were engineered:

- **`speed_mph`** — estimated vehicle speed, useful for examining the impact of urban congestion.
- **`revenue_per_minute`** — operational revenue yield across different tariff/rate codes.

---

## 📈 Exploratory Analysis

The project includes analysis of:

- passenger counts;
- trip distances;
- fares;
- trip durations;
- payment types;
- tipping behavior;
- hourly patterns;
- weekday/weekend differences;
- taxi rate codes;
- vehicle speed;
- revenue efficiency.

Pivot tables and visualizations were used to identify temporal and operational patterns in the cleaned dataset.

---

## 🛠️ Technical Stack

- **Python 3**
- **Pandas** — data cleaning, filtering, aggregation, pivot tables
- **NumPy** — numerical and vectorized operations
- **Matplotlib** — visualization
- **Seaborn** — statistical visualization
- **PyArrow / FastParquet** — Parquet data processing

---

## 📁 Project Structure

```text
NYC-Taxi/
│
├── NYC_Taxi_EDA.ipynb
├── README.md
└── heatmap.png

