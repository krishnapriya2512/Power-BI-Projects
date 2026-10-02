# 📊 Campfly Sales Analytics Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-1f6feb)
![Power Query](https://img.shields.io/badge/Power%20Query-ETL-6f42c1)
![Pages](https://img.shields.io/badge/Report%20Pages-4-orange)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

> **Turning ~8,000 raw sales orders into answers on revenue, customers, channels, currencies, warehouses, products and regions.**

---

## 📌 Table of Contents

1. [Business Context](#-business-context)
2. [Objectives at a Glance](#-objectives-at-a-glance)
3. [Dataset & Data Model](#-dataset--data-model)
4. [Dashboard Walkthrough](#-dashboard-walkthrough)
   - [Page 1: Executive Overview](#page-1--executive-overview)
   - [Page 2: Trends, Channels & Currency](#page-2--trends-channels--currency)
   - [Page 3: Customers & Products](#page-3--customers--products)
   - [Page 4: Operations, Profitability & Regions](#page-4--operations-profitability--regions)
5. [Key Insights & Recommendations](#-key-insights--recommendations)
6. [DAX Highlights](#-dax-highlights)
7. [Design Decisions & Data Notes](#-design-decisions--data-notes)
8. [How to Run This Project](#-how-to-run-this-project)
9. [Repository Structure](#-repository-structure)
10. [Tools & Skills Demonstrated](#-tools--skills-demonstrated)
11. [Next Steps](#-next-steps)

---

## 🏢 Business Context

**Campfly** sells through three channels (Wholesale, Distributor and Export) to 50 customers across 100 delivery regions, fulfilling orders from four warehouses and invoicing in five currencies.

Every order was being logged, but the data was hard to use. Decision-makers could not quickly see which customers, channels, products and regions drove sales, whether sales were growing, or how efficiently orders were shipped.

This project builds a **four-page, interactive Power BI dashboard** that consolidates orders, shipping, customers and product details into one place, so stakeholders can spot patterns, improve processes and make better sales decisions.

| Headline Number | Value |
|---|---:|
| Orders analysed (1 Jan 2017 – 12 Dec 2019) | **7,991** |
| Total revenue* | **154.57M** |
| Total cost | **96.78M** |
| Total profit | **57.79M** |
| Profit margin | **37.4%** |
| Average unit price | **2,284.54** |
| Average unit cost | **1,431.91** |
| Average order value | **19,343** |
| Average days to ship | **10.4 days** |

\*Revenue combines NZD, USD, AUD, GBP and EUR with no currency conversion, so totals are indicative. See [Design Decisions & Data Notes](#-design-decisions--data-notes).

---

## 🎯 Objectives at a Glance

Every project objective maps to a specific page and set of visuals.

| # | Objective | Page | Visuals Used | What the Dashboard Shows |
|:-:|---|:-:|---|---|
| 1 | **Gain Overview** | 1 | 7 KPI **cards**, line chart, bar chart | Total revenue, cost, profit, margin, orders, **average unit price (2,284.54)** and **average unit cost (1,431.91)** at a glance |
| 2 | **Analyze Sales Trends** | 1, 2 | **Line chart** (Year-Month) and **clustered column** (Month × Year) | Monthly revenue ranges from 3.5M to 5.5M. Peaks: Mar 2017, Aug–Sep 2017, Jan 2018, Dec 2018. February is typically the softest month |
| 3 | **Customer Insights** | 3 | **Top 10 bar**, **scatter** (orders vs revenue), **customer KPI table** | 50 customers place 135–210 orders each. Top customer (Customer 12) = 4.08M. Top 10 customers = 23.4% of revenue |
| 4 | **Channel Analysis** | 2 | **Table**, **donut**, channel KPIs | Wholesale leads with 53.7% of revenue; Export has the best margin (38.1%) |
| 5 | **Currency Impact** | 2 | **Column chart** and **100% stacked column** by currency | NZD (37.6%) and USD (28.9%) dominate exposure; the mix is stable across years |
| 6 | **Warehouse Efficiency** | 4 | **Bar**, **distribution column**, **heatmap matrix**, late-order cards | Orders ship in 3–18 days (avg 10.4). 23.8% ship after 14 days. Warehouses are equally fast |
| 7 | **Product Performance** | 3 | **Bar** (with share tooltip), **heatmap matrix** (product × year), **scatter** (units vs revenue) | Top 7 of 14 products generate 85.9% of revenue |
| 8 | **Cost & Profitability** | 4 | **Combo chart**, **scatter**, **price-band column** | Margin holds at 37.4%; products range 35.1%–40.1% |
| 9 | **Regional Analysis** | 4 | **Top 10 bar**, region **slicer** | Demand is spread across 100 regions; the top region is only 1.4% of revenue |

---

## 🗂 Dataset & Data Model

**Source:** `Campfly Sales Analysis Dashboard.xlsx – Sales Orders.csv` (7,991 rows, 13 columns, one row per order and product)

### Data dictionary

| Column | Type | Description |
|---|---|---|
| `OrderNumber` | Text | Unique order ID |
| `OrderDate` | Date | Date the order was placed |
| `Ship Date` | Date | Date the order shipped |
| `Customer Name Index` | Integer | Anonymised customer code (1–50) |
| `Channel` | Text | Wholesale, Distributor or Export |
| `Currency Code` | Text | NZD, USD, AUD, GBP or EUR |
| `Warehouse Code` | Text | AXW291, GUT930, NXH382 or FLR025 |
| `Delivery Region Index` | Integer | Anonymised delivery region code (1–100) |
| `Product Description Index` | Integer | Anonymised product code (1–14) |
| `Order Quantity` | Integer | Units ordered (5–12) |
| `Unit Price` | Decimal | Selling price per unit |
| `Total Unit Cost` | Decimal | **Cost per unit** (despite the name) |
| `Total Revenue` | Decimal | Quantity × Unit Price |

### Engineered columns

| Column | Formula idea | Why it exists |
|---|---|---|
| `Line Cost` | Quantity × Total Unit Cost | The source cost is per unit, so order-level cost must be calculated |
| `Line Profit` | Total Revenue − Line Cost | Profit by any slice |
| `Fulfillment Days` | `DATEDIFF(OrderDate, Ship Date, DAY)` | Shipping speed is not in the source |
| `Fulfillment Bucket` / `Bucket Order` | 3–6, 7–10, 11–14, 15–18 days | Delivery-time distribution, sorted in order |
| `Price Band` | Under 1,000 / 1,000–2,499 / 2,500–3,999 / 4,000+ | Tests whether price level changes margin |
| `Customer`, `Region`, `Product` | Text labels such as "Customer 12" | Readable labels that cannot be accidentally summed |

### Model

| Table | Role | Notes |
|---|---|---|
| `Sales` | Fact | One row per order |
| `Date` | Dimension | Calendar 2017–2019 with Year, Quarter, Month, Month No and Year-Month; **marked as the date table** |
| `Late threshold` | What-if parameter | Numbers 3–18, default 14, drives the late-order measures |
| `_Measures` | Measure table | Holds all DAX measures in one place |

**Relationships:** `Date[Date]` → `Sales[OrderDate]` (active, one-to-many) and `Date[Date]` → `Sales[Ship Date]` (inactive, available through `USERELATIONSHIP`).

---

## 🧭 Dashboard Walkthrough

> 📸 Screenshots live in the [`images/`](images) folder.

### Page 1: Executive Overview

**Objective 1 (Overview), with a first look at Objectives 2 and 4**

![Page 1](images/01_executive_overview.png)

| Visual | Fields | Purpose |
|---|---|---|
| 7 KPI cards | Revenue, Cost, Profit, Margin %, Orders, Avg Unit Price, Avg Unit Cost | Headline numbers in five seconds |
| Line chart | Year-Month vs Total Revenue | Trend at a glance |
| Bar chart | Channel vs Total Revenue | Sales mix |
| Slicers | Year, Channel, Currency Code | Filter the whole page |

---

### Page 2: Trends, Channels & Currency

**Objectives 2, 4 and 5**

![Page 2](images/02_trends_channels_currency.png)

| Question | Visual | Finding |
|---|---|---|
| Where are the peak periods? | Line chart (Year-Month) | Biggest months: Mar 2017 (5.54M), Aug–Sep 2017 (5.15M), Dec 2018 (5.12M), Jan 2018 (5.07M) |
| Is there seasonality? | Column chart (Month, one series per year) | Mild only. Peaks fall in different months each year. February is the weakest month in 2019 (3.46M) |
| Which channel is most effective? | Channel table + donut | See table below |
| How exposed are we to each currency? | Column chart + 100% stacked column by year | See table below |

**Channel performance**

| Channel | Orders | Revenue | Share | Profit | Margin | Avg Order Value |
|---|---:|---:|---:|---:|---:|---:|
| Wholesale | 4,286 | 82.97M | 53.7% | 30.73M | 37.0% | 19,358 |
| Distributor | 2,510 | 48.97M | 31.7% | 18.43M | 37.6% | 19,510 |
| Export | 1,195 | 22.64M | 14.6% | 8.63M | 38.1% | 18,943 |

> Wholesale wins on **volume**, Export on **margin**, and the margin gap is only about 1 percentage point. The channel mix matters more than channel profitability.

**Revenue by invoice currency (native currency, not converted)**

| Currency | Orders | Revenue | Share of nominal total |
|---|---:|---:|---:|
| NZD | 3,026 | 58.15M | 37.6% |
| USD | 2,323 | 44.65M | 28.9% |
| AUD | 1,309 | 25.91M | 16.8% |
| EUR | 664 | 13.14M | 8.5% |
| GBP | 669 | 12.73M | 8.2% |

> ⚠️ The dataset has no exchange rates, so the dashboard shows **currency exposure and mix** rather than a converted fluctuation effect. A caveat text box on the page says so explicitly.

---

### Page 3: Customers & Products

**Objectives 3 and 7**

![Page 3](images/03_customers_products.png)

| Question | Visual |
|---|---|
| Who are the highest-value customers? | **Top 10 customers** bar chart (Top N filter) |
| Who orders often versus who orders big? | **Scatter**: orders (x) vs revenue (y), one dot per customer |
| What does each customer look like in detail? | **Customer KPI table**: orders, revenue, average order value, margin % |
| Which products sell best? | **Bar chart** with a *share of total revenue* tooltip |
| How does each product perform over time? | **Heatmap matrix**: product × year, with a color scale |
| Which products sell many units but earn little? | **Scatter**: units sold vs revenue per product |

**Top 5 customers**

| Customer | Orders | Revenue | Share of Revenue |
|---|---:|---:|---:|
| Customer 12 | 210 | 4.08M | 2.6% |
| Customer 17 | 175 | 3.82M | 2.5% |
| Customer 34 | 176 | 3.68M | 2.4% |
| Customer 18 | 186 | 3.64M | 2.4% |
| Customer 29 | 179 | 3.61M | 2.3% |

**Product performance**

| Product | Revenue | Share | Margin | Units |
|---|---:|---:|---:|---:|
| Product 7 | 25.71M | 16.6% | 37.1% | 11,156 |
| Product 1 | 25.49M | 16.5% | 37.6% | 11,028 |
| Product 2 | 22.85M | 14.8% | 38.1% | 9,903 |
| Product 11 | 20.62M | 13.3% | 37.3% | 9,034 |
| Product 5 | 17.02M | 11.0% | 37.9% | 7,231 |
| Product 13 | 11.77M | 7.6% | 35.1% | 5,369 |
| Product 9 | 9.26M | 6.0% | 38.3% | 4,225 |
| *Products 6, 8, 14, 10, 12, 3, 4* | *2.86M–3.34M each* | *1.8%–2.2% each* | *35.4%–40.1%* | *1,294–1,447 each* |

---

### Page 4: Operations, Profitability & Regions

**Objectives 6, 8 and 9**

![Page 4](images/04_operations_profitability_regions.png)

**Warehouse efficiency (Objective 6).** A *what-if slider* sets what counts as "late" (default: more than 14 days), and the cards, charts and heatmap update instantly.

| Warehouse | Orders | Share of Orders | Revenue | Avg Days to Ship | % Late (over 14 days) |
|---|---:|---:|---:|---:|---:|
| AXW291 | 3,756 | 47.0% | 73.06M | 10.54 | 24.0% |
| GUT930 | 1,850 | 23.2% | 36.25M | 10.35 | 22.8% |
| NXH382 | 1,569 | 19.6% | 29.53M | 10.50 | 25.1% |
| FLR025 | 816 | 10.2% | 15.72M | 10.05 | 23.0% |

Delivery time is spread almost evenly across the four buckets (3–6 days: 1,987 orders; 7–10: 2,064; 11–14: 2,037; 15–18: 1,903).

**Cost & profitability (Objective 8)**

| Visual | What it answers |
|---|---|
| **Combo chart**: revenue and cost (columns) with margin % (line) by month | Is profit keeping pace with sales? |
| **Scatter**: weighted average price (x) vs margin % (y), per product | Do higher-priced products earn better margins? |
| **Column chart**: margin % by price band | Does price level change profitability? |

| Price Band | Orders | Margin |
|---|---:|---:|
| Under 1,000 | 1,725 | 36.6% |
| 1,000–2,499 | 3,336 | 37.6% |
| 2,500–3,999 | 1,979 | 37.2% |
| 4,000+ | 951 | 37.5% |

**Regional analysis (Objective 9).** A Top 10 bar chart and a searchable region slicer. Regions are anonymised codes, so a geographic map was not possible.

| Region | Revenue | Share |
|---|---:|---:|
| Region 23 | 2.23M | 1.4% |
| Region 6 | 2.12M | 1.4% |
| Region 35 | 2.06M | 1.3% |
| Region 67 | 1.92M | 1.2% |
| Region 24 | 1.92M | 1.2% |

---

## 💡 Key Insights & Recommendations

1. **Sales are flat-to-softening, not seasonal.** Revenue was 52.58M (2017) and 53.46M (2018, +1.7%). Compared like-for-like (Jan–Nov), 2019 is about **3.6% below 2018**. The dataset ends on 12 Dec 2019, so December is incomplete and full-year 2019 looks worse than it is. Monthly swings are driven more by individual large orders than by a repeating seasonal pattern.

2. **The customer base is healthy and diversified.** No customer exceeds 2.6% of revenue, and the top 10 account for 23.4%. Losing any single account would not hurt much, so the opportunity is to *grow* the top accounts, not to defend against concentration.

3. **A few products carry the business.** Seven of fourteen products produce **85.9%** of revenue, while the other seven each contribute only about 2%. *Recommendation:* review pricing, promotion or range rationalisation for the long-tail products.

4. **Channel choice is a volume decision, not a margin decision.** Wholesale brings 53.7% of revenue; Export has the best margin (38.1%) but the smallest share (14.6%). Growing Export would lift margin only slightly, so it should be justified by volume potential.

5. **Warehouses are equally fast, but 1 in 4 orders ships late.** Average shipping time is 10.0–10.5 days at every warehouse, and about **24%** of orders take more than 14 days. The delay is a *process-wide* issue, not a single-site problem. Also note that **AXW291 handles 47% of all orders**, a capacity concentration risk.

6. **Margins are stable, and price level doesn't explain them.** Margin sits near 37.4% across products (35.1%–40.1%) and across price bands (36.6%–37.6%). Unit cost ranges from 40% to 85% of the selling price from order to order, so **cost variation, not price, is the lever** to investigate for margin improvement.

7. **Currency exposure is concentrated in NZD and USD.** Together they make up about two-thirds of the (nominal) revenue, and the mix is stable from year to year. Hedging or pricing decisions should start there.

8. **Regional demand is broad.** The top region contributes just 1.4% of revenue and the top 10 regions only 12.6%, so regional marketing doesn't have a single obvious priority market. Targeting should be based on growth and margin rather than size alone.

---

## 🧮 DAX Highlights

```DAX
-- Core measures
Total Revenue   = SUM(Sales[Total Revenue])
Total Cost      = SUM(Sales[Line Cost])
Total Profit    = [Total Revenue] - [Total Cost]
Profit Margin % = DIVIDE([Total Profit], [Total Revenue])
Total Orders    = DISTINCTCOUNT(Sales[OrderNumber])
Avg Order Value = DIVIDE([Total Revenue], [Total Orders])

-- Row-level cost (source cost is per unit)
Line Cost = Sales[Order Quantity] * Sales[Total Unit Cost]

-- Shipping speed
Fulfillment Days = DATEDIFF(Sales[OrderDate], Sales[Ship Date], DAY)

-- Time intelligence
Revenue LY    = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('Date'[Date]))
Revenue YoY % = DIVIDE([Total Revenue] - [Revenue LY], [Revenue LY])

-- Share of total (ignores the product filter only)
Revenue % of Total =
    DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL(Sales[Product])))

-- Interactive late-order logic driven by a what-if slider
Late Orders =
VAR t = [Late threshold Value]
RETURN COUNTROWS(FILTER(Sales, Sales[Fulfillment Days] > t))

% Late Orders = DIVIDE([Late Orders], [Total Orders])
```

---

## 🔍 Design Decisions & Data Notes

- **`Total Unit Cost` is a per-unit value.** It is always lower than the unit price, so total cost is `Quantity × Total Unit Cost`. Summing the raw column would understate cost and overstate profit.
- **Measures, not calculated columns, for totals.** Measures respond to slicers and cross-filtering; calculated columns do not.
- **A dedicated Date table** (marked as a date table) powers year-over-year and month-over-month analysis. The `Ship Date` relationship is kept inactive and available on demand.
- **Mixed currencies are never presented as one currency.** The data has no exchange rates, so currency analysis focuses on exposure and mix, with a caveat on the page. Converting needs a rates table (see [Next Steps](#-next-steps)).
- **Channels differ from the brief's example.** The brief mentions online/offline; the actual channels are **Wholesale, Distributor and Export**.
- **Index columns are anonymised** (customer, region, product), so the dashboard uses readable labels and cannot draw a map.
- **The data ends on 12 Dec 2019.** December 2019 is incomplete, so 2019 comparisons should use like-for-like periods.
- **Honest charts:** bar charts start at zero, and the late-order definition is adjustable rather than hard-coded.

---

## ▶️ How to Run This Project

1. Clone the repository and open `Sales_dashboard.pbix` in **Power BI Desktop** (Windows).
2. If prompted that the data source cannot be found: **Home → Transform data → Data source settings → Change Source**, and point it to `data/Campfly Sales Analysis Dashboard.xlsx - Sales Orders.csv`.
3. Click **Refresh**.
4. Use the slicers (Year, Channel, Currency, Region) and the **late-threshold slider** on Page 4 to explore.

---

## 📁 Repository Structure

```
sales-analytics-powerbi/
├── Sales_dashboard.pbix
├── data/
│   └── Campfly Sales Analysis Dashboard.xlsx - Sales Orders.csv
├── images/
│   ├── 01_executive_overview.png
│   ├── 02_trends_channels_currency.png
│   ├── 03_customers_products.png
│   └── 04_operations_profitability_regions.png
└── README.md
```

---

## 🛠 Tools & Skills Demonstrated

- **Power BI Desktop**: 4-page report design, KPI cards, line/bar/column/donut/scatter/combo charts, matrix heatmaps, tooltips, slicers, Top N filters
- **Power Query**: data cleaning, type and locale handling
- **Data modelling**: fact table, date dimension, active and inactive relationships, what-if parameter
- **DAX**: calculated columns, measures, time intelligence (`SAMEPERIODLASTYEAR`), `CALCULATE`/`ALL` for share of total, variables and `FILTER` for dynamic logic
- **Analytical thinking**: spotting a per-unit cost trap, handling mixed currencies, and flagging incomplete periods
- **Business storytelling**: translating each business objective into the right chart and an actionable insight

---

## 🚀 Next Steps

- Add a **monthly exchange-rate table** to convert all sales to one currency and measure the real impact of currency fluctuations
- Add **forecasting** (Power BI analytics pane or a DAX trend) for the next 6 months
- Use **drill-through** pages for customer and product detail
- Publish to the **Power BI Service** with a scheduled refresh

---

## 👤 Author

Built by **KP** · [GitHub @krishnapriya2512](https://github.com/krishnapriya2512)

⭐ If you found this project useful, consider giving it a star.
