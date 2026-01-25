# 🌏 Air Quality & Environmental Insights — Power BI Dashboard

## 📌 Project Overview

This project analyzes **India’s air quality trends (2015–2020)** by combining historical pollution data, **real-time AQI**, and **forest cover statistics** to present a holistic view of environmental health in India.

The dashboard is built using **Power BI** and focuses on transforming raw environmental datasets into **clear, actionable insights** through strong data modeling, DAX measures, and visual storytelling.

---

## 🎯 Objectives

* Understand long-term air quality trends across Indian cities and states
* Identify pollution hotspots and most affected regions
* Analyze pollutant-level contributions to AQI (PM2.5, PM10, NO₂, CO, etc.)
* Compare environmental sustainability (forest cover) with pollution growth
* Provide an interactive, decision-ready dashboard

---

## 🗂️ Datasets Used

| Dataset         | Description                                               |
| --------------- | --------------------------------------------------------- |
| **cityday**     | Primary fact table with daily city-level AQI & pollutants |
| **cityhour**    | Hourly city-level pollution data                          |
| **stationday**  | Daily station-level pollution data                        |
| **stationhour** | Hourly station-level pollution data                       |
| **stations**    | Station metadata (StationId, City, State, Status)         |
| **aqi**         | State-level daily AQI aggregates                          |
| **realtime**    | Latest AQI readings for live indicators                   |
| **forestcover** | ISFR data (2019, 2021, 2023) for environmental context    |

---

## 🔧 Data Cleaning & ETL

* Standardized **date and datetime formats**
* Normalized **city and state names**
* Resolved **duplicate station records**
* Filled missing values (forward-fill for hourly station streams)
* Flagged nulls for transparency
* Removed **sensor outliers** beyond plausible ranges (PM2.5 / PM10)
* Ensured pollutant **unit consistency**
* Created derived metrics such as:

  * Monitoring station count per city
  * Population-weighted indicators (where applicable)

---

## 🧩 Data Model (Star Schema)

**Fact Table**

* `cityday` (Date, City, Pollutants, AQI, AQI Bucket)

**Dimension Tables**

* `stations` (StationId, City, State)
* `aqi` (State-level summaries)
* `realtime` (Latest AQI values)
* `forestcover` (State/UT forest statistics)
* `Date` (Calendar table)

**Relationships**

* One-to-Many: Dimensions → Facts
* Single-direction filtering (bi-directional only when required for slicers)

---

## 📐 KPIs & DAX Measures

* **Average AQI** – Overall air quality indicator for India
* **Forest Cover Change Index** – Tracks environmental sustainability trend
* **Most Polluted City** – City with highest AQI (Delhi in this dataset)

---

## 📊 Key Dashboard Insights

* Pollution hotspots are concentrated around major urban regions
* **PM2.5 and PM10** are the dominant contributors to AQI
* AQI levels peaked during **2018–2019**
* Majority of regions fall under the **Moderate AQI** category
* Forest cover trends show **mixed environmental progress** despite rising pollution

---

## 🖥️ Dashboard Features

* Interactive slicers for:

  * Date
  * State
  * Pollutant
  * AQI Category
* Scatter maps for hotspot identification
* Trend charts for time-series analysis
* KPI cards for quick insights

---

## 🚀 Deployment

The dashboard is published on **Power BI Service** for easy access and sharing.

🔗 **Dashboard Link:https://app.powerbi.com/view?r=eyJrIjoiOWRjMDA5MTQtNmI1OS00Y2Y2LTgyMzQtMzRjZTczYjRiYjQ0IiwidCI6ImRhZTA4MDk3LTgyNjAtNDk5Ni05MDY2LWZhZTExYmY3MWVhNiJ9

---

## 🛠️ Tools & Technologies

* **Power BI** (Data Modeling, DAX, Visualization)
* **Power Query** (ETL)
* **Excel / CSV** datasets
* **Star Schema Modeling**

---

## 📌 Key Learnings

* End-to-end BI project execution
* Designing scalable data models
* Writing optimized DAX measures
* Translating data into business & environmental insights
* Building professional, portfolio-ready dashboards

---

## 📬 Contact

**Author:** Latha Sri Ravirala
🔗 LinkedIn: [https://www.linkedin.com/in/lathasri-ravirala-06b606309](https://www.linkedin.com/in/lathasri-ravirala-06b606309)

---

⭐ If you find this project useful, feel free to star the repository!

