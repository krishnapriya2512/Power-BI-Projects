# 🚖 Faro Rides: Demand Patterns & Operations Analytics

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-1f6feb)
![Data Model](https://img.shields.io/badge/Data%20Model-Star%20Schema-2ea44f)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

> **Turning two years of raw trip logs into answers that regional and city managers can act on.**

---

## 📌 Table of Contents

1. [Business Context](#-business-context)
2. [Data Model](#-data-model)
3. [Section 1: Trips by Location](#-section-1-trips-by-location)
4. [Section 2: Operations Overview](#-section-2-operations-overview)
5. [Key Insights](#-key-insights)
6. [Interactivity & Filtering](#-interactivity--filtering)
7. [Tools & Skills Demonstrated](#-tools--skills-demonstrated)
8. [Repository Structure](#-repository-structure)

---

## 🏢 Business Context

**Faro Rides** is a cab-sharing company operating across **12 cities in 5 provinces of Spain**. Since launch, the business has logged every trip: date, driver, city, ride type, fare, distance, and more.

The data exists, but nobody could use it to answer important business questions quickly. This project builds a two-page Power BI report that gives **Regional Managers** and **City Managers** a clear, filterable view of demand and operational performance.

| Headline Number | Value |
|---|---:|
| Total Trips (Jan 2024 – Dec 2025) | **21,837** |
| Total Revenue | **€404.37K** |
| Average Fare | **18.52** |
| Average Driver Rating | **4.23 ⭐** |

---

## 🗂 Data Model

The report is built on a **star schema**: one fact table surrounded by four dimension tables.

| Table | Type | Description | Rows |
|---|---|---|---:|
| `FctTrips` | Fact | One row per trip with operational and financial details | 21,837 |
| `DimGeography` | Dimension | Location lookup for each city where Faro operates | 12 |
| `DimRideType` | Dimension | Ride type lookup with pricing details | 3 |
| `DimDate` | Dimension | Full date table with year, quarter, month, week, and day attributes | 731 |
| `DimDrivers` | Dimension | Driver attributes with current rating and activity status | 50 |

---

## 📍 Section 1: Trips by Location

**Objective:** Understand Faro's demand patterns by location.

![Trips by Location dashboard](images/trips-by-location.png)

### Questions answered

| # | Business Question | How the Dashboard Answers It |
|:-:|---|---|
| 1 | How many trips has Faro completed overall? | **Card KPI** showing **21.837K** total trips |
| 2 | Which province generates the most demand? | **Bar chart** of trips by province, led by **Catalonia (5.5K)** and **Andalusia (5.4K)** |
| 3 | How is demand trending over time? | **Line chart** of daily trips from Jan 2024 to Dec 2025 |
| 4 | What are the trips and revenue per city? | **Table** with province, city, trips, and revenue, plus totals |
| 5 | Can regional and city managers focus on their own territory? | **City slicer** and cross-filtering across every visual |

### Findings

- **Catalonia** is the largest market with about 5.5K trips, followed closely by **Andalusia** (5.4K). **Madrid, Valencia, and the Basque Country** each deliver roughly 3.6K–3.7K.
- Daily demand is **stable and consistent**, mostly between **20 and 40 trips per day**, with occasional spikes toward 50. There is no sharp growth or decline across the two years.
- Demand is **evenly spread across the 12 cities** (about 1,750–1,875 trips each). No single city dominates.
- **Tarragona** has the most trips (1,874) and **Valencia** the highest revenue (€34.9K). **Seville** is the lowest on both trips (1,747) and revenue (€32.2K).

### Province snapshot

| Province | Cities | Trips |
|---|---|---:|
| Catalonia | Barcelona, Girona, Tarragona | 5,543 |
| Andalusia | Granada, Málaga, Seville | 5,364 |
| Madrid | Madrid, Alcalá de Henares | 3,665 |
| Valencia | Valencia, Alicante | 3,653 |
| Basque Country | Bilbao, San Sebastián | 3,612 |
| **Total** | **12 cities** | **21,837** |

---

## 🧭 Section 2: Operations Overview

**Objective:** The Regional Managers asked for a new page giving their local city teams a more detailed view of operational performance. It shows overall platform metrics, can be filtered by province, and lets city managers zoom in on their own city.

![Operations Overview dashboard](images/operations-overview.png)

### Questions answered

| Business Question | Answer | Visual Used |
|---|---|---|
| How many trips were completed each month? | Monthly trips ranged from roughly **840 to 960**, with a dip in February in both years | **Line chart** with a **date hierarchy** (drill up/down from year to month), 2024 vs 2025 |
| What time of day is demand heaviest? | **Evenings**, on both weekdays and weekends | **Clustered column chart** by shift and day type, using a **Shift column derived from the hour column** |
| What is the average rating across the platform? | **4.23** | **Card** (KPI) |
| Which ride types generate the most revenue? | **XL** (≈ €0.15M), ahead of Premium (≈ €0.13M) and Standard (≈ €0.12M) | **Clustered bar chart** |
| What is the average fare amount? | **18.52** | **Card** (KPI) |
| Where is revenue concentrated across Spain? | Spread across all **12 cities**, with the Mediterranean coast and the north all well represented | **Bubble map** with a **Province → City hierarchy** |
| Which drivers generate the most revenue, and what are their ratings? | See the table below | **Table** with conditional formatting on rating |

### 🏆 Top drivers by revenue

| Driver | Revenue | Avg. Rating |
|---|---:|:-:|
| Sofía Martínez | 8,994.40 | 4.23 |
| Alejandro Jiménez | 8,852.99 | 4.22 |
| Daniel Gómez | 8,599.20 | 4.22 |
| Nuria Sánchez | 8,492.73 | 4.23 |

> Driver ratings are tightly clustered around the platform average, so top earners are not trading quality for volume.

### Shift definitions

The `Shift` column was derived from the trip hour to group demand into four periods: **Morning, Afternoon, Evening, and Night**. Weekday volume is roughly 2.5× weekend volume in every shift, with Evening the busiest.

---

## 💡 Key Insights

1. **Demand is geographically balanced.** Catalonia and Andalusia lead, but all 12 cities perform within a narrow band, so growth is not dependent on one market.
2. **Demand is steady, not seasonal.** Daily and monthly trips stay in a tight range across 2024 and 2025, with February the softest month.
3. **Evenings are peak time.** Driver availability and incentives should be weighted toward the evening shift, on weekdays especially.
4. **XL rides punch above their weight.** XL leads revenue, so ride-type mix is a strong lever for revenue growth.
5. **Quality is consistent.** A platform rating of 4.23 with top drivers within 0.01 of the average points to a uniformly good experience.

---

## 🎛 Interactivity & Filtering

| Feature | Who It Helps |
|---|---|
| **Province dropdown** | Regional Managers focusing on their own province |
| **City slicer / dropdown** | City Managers zooming in on a single city |
| **Province → City hierarchy** (map & table) | Drill from region to city in one click |
| **Date hierarchy** (Year → Month) | Analysts moving between yearly and monthly trends |
| **Cross-filtering** across all visuals | Everyone: click any bar, bubble, or shift and the whole page responds |

---

## 🛠 Tools & Skills Demonstrated

- **Power BI Desktop**: report design, page layout, KPI cards, maps, conditional formatting
- **Data modelling**: star schema with a fact table and four dimension tables
- **DAX**: calculated columns (e.g. `Shift`) and measures (trips, revenue, average rating, average fare)
- **Hierarchies**: date and geography hierarchies for drill-down analysis
- **Business storytelling**: translating stakeholder questions into the right visual for each one

---

## 📁 Repository Structure

```
faro-rides-powerbi/
├── FaroRides.pbix              # Power BI report file
├── data/                       # Source tables (FctTrips, Dim*)
├── images/
│   ├── trips-by-location.png
│   └── operations-overview.png
└── README.md
```

---

## 👤 Author

**[Your Name]**
[LinkedIn](https://www.linkedin.com/) · [Portfolio](#) · [Email](mailto:you@example.com)

*If you found this project useful, feel free to ⭐ the repo.*
