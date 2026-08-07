# 🚖 Data Cleaning and Exploratory Analysis of NYC Taxi Dataset

## 📌 Project Overview

In this project, I performed data cleaning and exploratory data analysis (EDA) on approximately 3.5 million historical New York City taxi trip records.

The main goal was to identify technical anomalies and logically inconsistent records and prepare the data for reliable exploratory and business-oriented analysis.

## 🛠️ Data Cleaning & Anomaly Detection

I designed a filtering pipeline based on domain constraints and logical consistency.

### Filtering Pipeline

| Feature | Cleaning rule | Purpose |
|---|---|---|
| **Passenger count** | Keep values from 1 to 6 | Remove empty trips and obvious data-entry errors |
| **Trip distance** | Cap at 60 miles | Remove extreme GPS/outlier records |
| **Fare amount** | Keep values from $2.5 to $200 | Remove invalid fares and apparent meter/data glitches |
| **Zero-distance trips** | Keep when the fare is within the valid range | A zero recorded distance does not necessarily mean an invalid trip |
| **Trip duration** | Keep values from 1 minute to 2 hours | Remove instantaneous or obviously corrupted records |

The filtering pipeline removed approximately **19% of the original records**, reducing the dataset from about **3.5M to 2.8M records**.

The purpose was not to remove every unusual observation, but to eliminate records that were inconsistent with the domain and the structure of the data.

---

## 📊 Key Business Insight: Tip Ratios by Hour and Day

I engineered temporal features (`hour`, `day`) and used pivot tables to examine tipping patterns.

The main metric was the **tip ratio**:

$$
\text{Tip Ratio} =
\frac{\text{Tip Amount}}{\text{Fare Amount}} \times 100
$$

The following heatmap shows the median tip ratio by hour and day of the week:

![Tip ratio by hour and day](tip_ratio_heatmap.png)

### Key observations

1. **Weekday evening hours show consistently high tip ratios.**

   The period around **16:00–19:00** stands out across several weekdays.

2. **Weekend late-night hours do not clearly dominate.**

   Friday and Saturday nights show noticeable tipping activity, but they do not consistently exceed the weekday evening pattern.

3. **The pattern is descriptive, not causal.**

   The dataset does not provide enough information to conclude that rush-hour traffic or passenger occupation is the direct cause of higher tipping ratios. These are observed associations that would require additional analysis to explain causally.

---

## 🔍 Important Note About Tip Data

During the exploratory analysis, I found an important limitation of the dataset:

**Tip amounts are recorded only for credit-card transactions.**

Cash tips are not captured by the taximeter and therefore appear as `0.0` in the raw data.

This creates a systematic problem if the goal is to compare actual tipping behavior.

Using the ordinary mean would mix two different situations:

- a genuine zero tip;
- a cash transaction for which the tip amount is simply not recorded.

Therefore, the analysis uses the **median tip ratio** rather than the mean as a more robust descriptive statistic.

This does **not** reconstruct missing cash tips or eliminate the underlying data-collection bias. It simply makes the comparison less sensitive to the artificial concentration of zeros.

---

## ⚡ Performance Optimization & Derived Metrics

The dataset is large enough for memory and performance to become relevant.

The processing pipeline uses columnar data and vectorized operations rather than row-by-row Python processing.

### Derived metrics

Several additional features were created for operational analysis:

- `speed_mph` — estimated vehicle speed based on trip distance and duration;
- `revenue_per_minute` — fare revenue relative to trip duration;
- temporal features such as `hour` and `day`;
- `tip_ratio` — tip amount relative to fare amount.

These metrics were used to investigate relationships between time, congestion, revenue and tipping behavior.

---

## 💻 Technical Stack

- **Python 3**
- **Pandas** — data cleaning, filtering, aggregation and pivot tables
- **NumPy** — numerical operations and feature engineering
- **Matplotlib** — visualization
- **Seaborn** — statistical visualization and heatmaps
- **PyArrow / FastParquet** — reading and processing Parquet data

---

## 📁 Project Structure

```text
.
├── README.md
├── tip_ratio_heatmap.png
└── nyc-taxi-data-cleaning.ipynb
