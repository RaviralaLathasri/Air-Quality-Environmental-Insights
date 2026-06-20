# DAX Measures — Environmental Analytics Dashboard

This file documents all DAX measures and calculated tables used in the Power BI Environmental Analytics Dashboard (Air Quality Index & Forest Cover Analysis).

---

## AQI Measures

### Base AQI
Calculates the overall average AQI value from the dataset.

```dax
Base AQI = AVERAGE('aqi'[aqi_value])
```

### Overall Avg AQI
Returns the overall average AQI across all records.

```dax
Overall Avg AQI = AVERAGE('aqi'[aqi_value])
```

### Avg AQI
Calculates the average AQI from the city-day level dataset.

```dax
Avg AQI = AVERAGE('A_city_day'[Column15])
```

### % Change in Avg AQI
Calculates the year-over-year percentage change in average AQI by comparing the current year against the previous year.

```dax
% Change in Avg AQI = 
VAR CurrentYear = MAX('A_city_day'[DateD])
VAR LastYear = CurrentYear - 1
VAR CurrAvg = CALCULATE([Avg AQI], FILTER(ALL('A_city_day'), 'A_city_day'[DateD] = CurrentYear))
VAR PrevAvg = CALCULATE([Avg AQI], FILTER(ALL('A_city_day'), 'A_city_day'[DateD] = LastYear))
RETURN
IF(NOT ISBLANK(PrevAvg), DIVIDE(CurrAvg - PrevAvg, PrevAvg), BLANK())
```

### Most Polluted State
Identifies the state with the highest average AQI value using SUMMARIZE and TOPN.

```dax
Most Polluted State = 
VAR TableWithAvg = 
    SUMMARIZE(
        'aqi',
        'aqi'[state],
        "AvgAQI", AVERAGE('aqi'[aqi_value])
    )
VAR TopState = TOPN(1, TableWithAvg, [AvgAQI], DESC)
RETURN
CONCATENATEX(TopState, 'aqi'[state], ", ")
```

---

## AQI Simulation Measures (What-If Analysis)

These measures power the "If AQI improves by X%, how much impact will it create?" what-if visual using a What-If parameter table.

### AQI Change % Value
Captures the selected value from the What-If parameter slider.

```dax
AQI Change % Value = SELECTEDVALUE('AQI Change %'[AQI Change %], 0)
```

### Simulated AQI
Applies the selected percentage change to the base AQI to simulate the projected AQI value.

```dax
Simulated AQI = 
[Base AQI] * (1 + 'AQI Change %'[AQI Change % Value] / 100)
```

### Adjusted AQI
Alternative simulation measure applying the AQI change percentage directly to the average AQI value.

```dax
Adjusted AQI = 
AVERAGE(aqi[aqi_value]) *
(1 + 'AQI Change %'[AQI Change % Value] / 100)
```

### AQI Change Impact
Calculates the difference between the simulated and base AQI to show the magnitude of impact.

```dax
AQI Change Impact = 
[Simulated AQI] - [Base AQI]
```

### AQI Impact Value
A SWITCH measure used to drive the waterfall chart, returning the appropriate value depending on which step (Base AQI, AQI Change Impact, or Simulated AQI) is selected.

```dax
AQI Impact Value = 
SWITCH(
    SELECTEDVALUE('AQI_Impact'[Step]),
    "Base AQI", [Base AQI],
    "AQI Change Impact", [AQI Change Impact],
    "Simulated AQI", [Simulated AQI]
)
```

### AQIImpactTable (Calculated Table)
A static DAX table created to define the three steps used in the waterfall chart visual.

```dax
AQIImpactTable = 
DATATABLE(
    "Step", STRING,
    {
        {"Base AQI"},
        {"AQI Change Impact"},
        {"Simulated AQI"}
    }
)
```

---

## Forest Cover Measures

### Forest % Change 2019-2023
Calculates the percentage change in forest cover between ISFR 2019 and ISFR 2023 reports, used to compare against AQI trends for the forest-cover-vs-pollution correlation analysis.

```dax
Forest % Change 2019-2023 = 
VAR Prev = AVERAGE('(ISFR) from 2019 to 2023 (2)'[ISFR 2019])
VAR Curr = AVERAGE('(ISFR) from 2019 to 2023 (2)'[ISFR 2023])
RETURN
IF(Prev = 0, BLANK(), (Curr - Prev) / Prev * 100)
```

---

## Notes

- The What-If parameter table (`AQI Change %`) was created using Power BI's native "New Parameter" feature to enable interactive simulation in the dashboard.
- `AQIImpactTable` is a disconnected calculated table used solely to control category ordering in the waterfall chart.
- Measures referencing `ALL('A_city_day')` are used to ignore existing filter context and ensure year-over-year comparisons are calculated correctly regardless of slicer selections.
