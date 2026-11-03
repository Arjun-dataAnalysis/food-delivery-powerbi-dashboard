# Food Delivery Performance Dashboard (Power BI)

An end-to-end Power BI project analysing **5,940 food delivery orders** across **8 Indian cities** for 2025. The raw data contained deliberate quality issues, which were cleaned in Power Query, modelled as a star schema and turned into a 5-page interactive dashboard with insights and recommendations.

![Overview](images/1_overview.png)

---

## Business questions
1. How is the business performing overall (revenue, orders, cancellations, ratings)?
2. When do customers order — which hours and days are busiest?
3. How good is delivery performance, and what affects customer ratings?
4. Who are the customers, and which cuisines and restaurants perform best?

## Tools
- **Power BI Desktop** — Power Query (data cleaning), data modelling, DAX, report design
- **Excel** — raw data source

## Dataset
Synthetic dataset created for practice (all names and restaurants are fictional), Jan–Dec 2025.

| Table | Type | Rows (after cleaning) |
| --- | --- | --- |
| Orders | Fact | 5,940 |
| Customers | Dimension | 1,200 |
| Restaurants | Dimension | 200 |
| Delivery_Partners | Dimension | 200 |
| Calendar | Dimension (DAX) | 365 |

---

## Data cleaning (Power Query)
| Issue | Fix |
| --- | --- |
| 60 duplicate orders, duplicate customer & restaurant rows | Trimmed IDs, then removed duplicates |
| Dates in 4 different formats | Change Type → Using Locale (English – India) |
| Times in 12-hour and 24-hour formats | Change Type → Using Locale (English – US) |
| City spelling variants (Bombay, Bengaluru, Madras, Calcutta…) | Trim, Capitalize, Conditional Column to standard names |
| Payment & status label variants (COD, cod, Canceled…) | Standardised to one label each |
| Amounts stored as text (₹1,026 / Rs. 980) | Removed symbols and commas, converted to number |
| Discount stored as "15%" text | Removed % sign, converted to number |
| Impossible values (negative delivery time, rating of 6, 0 items) | Replaced with null |
| Cancelled orders with delivery time = 0 | Set to null (no delivery happened) |
| "Area, City" combined in one column | Split Column by delimiter |
| Missing Partner_ID / payment method | Kept as blank / "Unknown" (no imputation) |

New columns: `Order_Hour`, `Net_Amount`, `Delivery_Band`, `Age_Group`.

## Data model
Star schema: **Orders** fact table connected one-to-many (single direction) to **Customers**, **Restaurants**, **Delivery_Partners** and a DAX **Calendar** table (marked as date table).

## Key DAX measures
```DAX
Total Orders = COUNTROWS ( Orders )

Revenue = CALCULATE ( SUM ( Orders[Net_Amount] ), Orders[Order_Status] = "Delivered" )

Cancellation Rate % = DIVIDE ( [Cancelled Orders], [Total Orders] )

On-Time Delivery % =
DIVIDE (
    CALCULATE ( COUNTROWS ( Orders ), Orders[Delivery_Time_min] <= 30, NOT ISBLANK ( Orders[Delivery_Time_min] ) ),
    CALCULATE ( COUNTROWS ( Orders ), NOT ISBLANK ( Orders[Delivery_Time_min] ) )
)

Revenue MoM % =
DIVIDE ( [Revenue] - [Revenue Prev Month], [Revenue Prev Month] )
```
Other measures: Avg Order Value, Avg Delivery Time, Avg Rating, Active Customers, Orders per Customer, Weekend Orders %, Avg Orders per Day, Gold / Non-Gold AOV.

---

## Dashboard pages
| Page | Focus |
| --- | --- |
| 1. Overview | Revenue, orders, AOV, cancellation rate, rating; revenue by month & city; payment mix; MoM growth |
| 2. Demand | Orders by day, by hour, and a day × hour heatmap |
| 3. Delivery | Delivery time by vehicle & month, rating by delivery time, cancellation by payment method |
| 4. Customers | Orders by cuisine & age group, top 10 restaurants, Gold vs Non-Gold AOV |
| 5. Insights | Key findings and recommendations |

| | |
| --- | --- |
| ![Demand](images/2_demand.png) | ![Delivery](images/3_delivery.png) |
| ![Customers](images/4_customers.png) | ![Insights](images/5_insights.png) |

## Headline KPIs
| KPI | Value |
| --- | --- |
| Total orders | 5,940 |
| Avg order value | ₹773 |
| Cancellation rate | 5.8% |
| Avg delivery time | 32.2 min |
| On-time delivery (≤ 30 min) | 48% |
| Avg customer rating | 4.41 / 5 |

---

## Key insights & recommendations
| # | Insight | Evidence | Recommendation |
| --- | --- | --- | --- |
| 1 | Dinner is the peak | 8 PM busiest (784 orders); lunch peaks at 1 PM | Add delivery partners 7–10 PM and 12–2 PM |
| 2 | Weekends drive demand | 2 of 7 days but 34.6% of orders | Weekend offers and extra staffing |
| 3 | Festive surge | October revenue +38% MoM; February dip −19% | Plan campaigns before October; offers in February |
| 4 | Monsoon delays | ~41 min in July vs ~30 min in other months | Rain incentives and realistic ETAs |
| 5 | Speed drives ratings | 4.66 (≤30 min) → 3.60 (>45 min) | Set and track a 30-minute delivery target |
| 6 | Cash orders cancel more | COD 9.2% vs 4–5% for prepaid methods | Encourage UPI with cashback |

---

## Repository structure
```
├── Food_Delivery_Dashboard.pbix
├── Food_Delivery_Orders_2025.xlsx
├── Food_Delivery_Dashboard.pdf
├── images/
│   ├── 1_overview.png
│   ├── 2_demand.png
│   ├── 3_delivery.png
│   ├── 4_customers.png
│   └── 5_insights.png
└── README.md
```

## How to use
1. Download `Food_Delivery_Dashboard.pbix`.
2. Open it in Power BI Desktop (free).
3. If prompted, point the data source to `Food_Delivery_Orders_2025.xlsx` (Home → Transform data → Data source settings).

## Author
**[Your Name]** — [LinkedIn](https://www.linkedin.com/in/your-profile)
