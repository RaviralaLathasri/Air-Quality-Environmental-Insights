# 🗄️ Data Modeling — Air Quality Analytics (Power BI)

> A multi-granularity star schema built for analyzing India's air quality across cities, states, and monitoring stations — integrating real-time feeds, historical trends, and environmental context.

---

## 📁 Folder Contents

```
data-modeling/
├── README.md               ← You are here
└── data_model_schema.png   ← Star schema diagram (Power BI model view)
```

---

## 🧠 What Is This Data Model?

This project uses a **Star Schema** — the gold standard for analytical data modeling in Power BI and data warehouses. The design separates data into two roles:

- **Fact tables** — store measurable events (AQI readings, pollutant levels) at different time and location granularities
- **Dimension tables** — provide context and attributes (station metadata, AQI categories, forest cover)

This structure enables fast, intuitive DAX queries and clean Power BI visuals without complex joins.

---

## 📊 Schema Overview

```
                    ┌─────────────────┐
                    │   AQI_Impact    │  ← Health impact lookup
                    └────────┬────────┘
                             │
          ┌──────────────────▼──────────────────────────────┐
          │                                                  │
┌─────────┴──────┐    ┌─────────────┐    ┌──────────────────┴─────┐
│   forestcover  │    │   cityday   │◄───│     AQI Change %       │
│  (ISFR env.)   │───►│  ⭐ FACT    │    │   (% metric lookup)    │
└────────────────┘    └──────┬──────┘    └────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │    cityhour     │
                    │   ⭐ FACT       │◄─── realtime (live feed)
                    └────────┬────────┘
                             │
          ┌──────────────────▼──────────────────┐
          │                                     │
┌─────────┴──────┐    ┌──────────────┐    ┌─────┴──────────┐
│    stations    │───►│  stationday  │    │  stationhour   │
│  (metadata)   │    │  ⭐ FACT      │    │  ⭐ FACT        │
└────────────────┘    └──────────────┘    └────────────────┘
          │                                      ▲
          └──────────────────────────────────────┘
          
          aqi (state-level) ──────────────────────► cityday
```

---

## 📋 Datasets Used

| # | Table | Type | Granularity | Key Fields | Description |
|---|-------|------|-------------|------------|-------------|
| 1 | **cityday** | ⭐ Fact | City × Day | City, Date, AQI, Pollutants | Primary fact table — daily city-level AQI & pollutant concentrations |
| 2 | **cityhour** | ⭐ Fact | City × Hour | City, Datetime, AQI, Pollution Value | Hourly city-level pollution readings |
| 3 | **stationday** | ⭐ Fact | Station × Day | StationId, Date, AQI, Pollutants | Daily station-level pollution measurements |
| 4 | **stationhour** | ⭐ Fact | Station × Hour | StationId, Datetime, Readings | Hourly station-level pollution readings |
| 5 | **stations** | 🔷 Dimension | Station | StationId, City, State, Status | Station metadata — geographic & operational info |
| 6 | **aqi** | 🔷 Dimension | State × Day | State, Date, AQI Value, Prominent Pollutants | State-level daily AQI aggregates |
| 7 | **realtime** | 🔷 Dimension | City × Pollutant | City, Pollutant, Avg/Max/Min, Last Update | Latest AQI readings for live dashboard indicators |
| 8 | **forestcover** | 🔷 Dimension | State × Year | State/UT, ISFR 2019/2021/2023 | Forest cover data for environmental correlation |
| 9 | **AQI_Impact** | 🔸 Lookup | AQI Category | Category, Health Impact, CO/NH3/NO2… Mid | Maps AQI range to health impact descriptions |
| 10 | **AQI Change %** | 🔸 Lookup | AQI Category | Category, % Value | Computed percentage change metrics per category |

---

## 🔗 Relationships

| From | To | Join Key | Cardinality | Purpose |
|------|----|----------|-------------|---------|
| stations | stationday | StationId | 1 : Many | Link station metadata to daily readings |
| stations | stationhour | StationId | 1 : Many | Link station metadata to hourly readings |
| aqi | cityday | State + Date | 1 : Many | Add state-level AQI context to city data |
| realtime | cityhour | City | 1 : Many | Feed live readings into hourly city facts |
| realtime | stationhour | StationId | 1 : Many | Feed live readings into hourly station facts |
| forestcover | cityday | State/UT | 1 : Many | Join ISFR env. data to city pollution facts |
| AQI_Impact | cityday | AQI Category | 1 : Many | Enrich facts with health impact classification |
| AQI Change % | cityday | AQI Category | 1 : Many | Attach percentage change metrics to facts |

---

## ⚙️ Design Decisions & Why They Matter

### Why Star Schema?
A star schema keeps fact tables lean (numbers only) and dimensions rich (context). This means:
- DAX measures run faster — no complex multi-hop joins
- Visuals like slicers (by City, State, Pollutant) work out of the box
- New analysts can understand the model in minutes, not hours

### Why four fact tables instead of one?
Air quality data exists at two geographic levels (city, station) and two time resolutions (daily, hourly). Merging them would create millions of null values and slow every query. Keeping them separate follows the **grain principle** — one row = one measurement event at its natural resolution.

### Why a separate `realtime` table?
Real-time data refreshes on a different schedule than historical data. Isolating it means the live feed can update independently without touching historical fact tables, preventing accidental overwrites and enabling incremental refresh.

### Why `forestcover` as a dimension?
Forest cover (from ISFR 2019, 2021, 2023 surveys) is environmental context data — it doesn't change daily. Treating it as a dimension lets analysts correlate deforestation trends with AQI degradation over time without bloating fact tables.

---

## 📐 Model Snapshot

> See `data_model_schema.png` in this folder for the full Power BI model view with all relationships visualized.

---

## 🛠️ Tools & Technologies

| Tool | Usage |
|------|-------|
| **Power BI Desktop** | Data modeling, DAX, visualization |
| **Power Query (M)** | Data transformation & cleaning |
| **Star Schema** | Analytical modeling pattern |
| **DAX** | Measures, KPIs, time intelligence |

---

## 🙋 About This Project

This data model was designed to support a comprehensive **India Air Quality Analytics Dashboard** that enables:

- Tracking city and state-level AQI trends over time
- Identifying dominant pollutants (PM2.5, PM10, NO2, CO, NH3, O3, Benzene)
- Correlating environmental factors (forest cover) with pollution levels
- Providing real-time AQI monitoring with health impact context
- Comparing ISFR forest data across 2019, 2021, and 2023 survey years

---

*Built with Power BI · Star Schema Design Pattern · Data sourced from CPCB & ISFR*
