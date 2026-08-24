#  Superstore Sales — Power BI Dashboard

> Interactive 6-page Power BI dashboard built on 4 years of US retail superstore data, featuring dynamic DAX measures, YoY indicators, and insight-driven visuals.

---

##  Project Overview

**Project Title:** Superstore Sales Power BI Dashboard
**Tool:** Power BI Desktop, DAX

This dashboard transforms raw superstore sales data into actionable business intelligence across 6 focused pages. Every visual answers a specific business question — the goal was decision-driven analytics, not just data display.

**Tools Used:**
-  Power BI Desktop — dashboard development
-  DAX — 30+ calculated measures and KPI indicators
-  Python (pandas) — data preparation
-  GitHub — version control

---

##  Repository Structure

```
superstore-dashboard/
│
├── dashboard/
│   └── superstore_dashboard.pbix
│
├── screenshots/
│   ├── page1_overview.png
│   ├── page2_product.png
│   ├── page3_regional.png
│   ├── page4_discount.png
│   ├── page5_customer.png
│   └── page6_trends.png
│
└── README.md
```

---

##  Dataset Overview

| Metric | Value |
|--------|-------|
| Time Period | 2014 — 2017 |
| Total Revenue | $2.30M |
| Total Profit | $286K |
| Overall Margin | 12.47% |
| Total Orders | 5,009 |
| Unique Customers | 793 |
| Loss Order Rate | 18.72% |

---

##  Dashboard Pages

### Page 1 — Executive Overview
**Business Question:** Is the company healthy — profitable, growing, and operationally efficient?

![Page 1 - Overview](screenshots/page1_overview.png)

**KPIs:** Total Revenue, Total Profit, Total Orders, Profit Margin %, Loss Rate % — all with YoY growth indicators (▲/▼)

**Key Visuals:**
- Category combo chart — Furniture margin at 1.77% vs Technology 20.58%
- Region combo chart — South margin 11.61% vs East 13.49%
- Monthly revenue trend — Nov ($76K) and Dec ($75K) always peak
- Segment combo chart — Home Office leads margin at 16.54%

---

### Page 2 — Product Profitability
**Business Question:** Which products make money and which destroy margin?

![Page 2 - Product Profitability](screenshots/page2_product.png)

**KPIs:** Total Revenue, Total Profit, Profit Margin %

**Key Visuals:**
- Treemap — Furniture commands revenue share but delivers least profit
- Waterfall chart — Tables ($4K loss) and Bookcases ($3K loss) drag Furniture down
- Matrix table — Tables -8.96% margin and 23.71% discount in red immediately visible
- Sales Bucket combo — XL orders drive most profit

---

### Page 3 — Regional & State Performance
**Business Question:** Where is the business winning and where is it bleeding geographically?

![Page 3 - Regional Performance](screenshots/page3_regional.png)

**KPIs:** Total Revenue, Total Profit, Profit Margin %

**Key Visuals:**
- Top 10 States — New York ($19.3K) and California ($14.4K) anchor 41% of profit
- Bottom 5 Loss States — Ohio ($5.5K loss) and Pennsylvania ($3.9K loss) biggest drains
- Treemap — West and East dominate, South and Central lag behind
- Matrix — Pennsylvania -19.02% margin and Ohio -34.14% margin in red

---

### Page 4 — Discount & Margin Analysis
**Business Question:** Is discounting driving growth or systematically destroying profit?

![Page 4 - Discount Analysis](screenshots/page4_discount.png)

**KPIs:** Total Discount Given, Loss Orders, Loss Rate %, Avg Discount %

**Key Visuals:**
- Scatter plot — Tables and Bookcases bottom right (high discount, negative margin)
- Hero chart — margin crashes from +30.4% (No Discount) to -100% (High Discount)
- Stacked bar — Furniture receives most high-discount orders proportionally
- Sub-category margin bar — Tables -7.15% and Bookcases -1.27% in red

---

### Page 5 — Customer Intelligence
**Business Question:** Who are the best customers and which segments are most valuable?

![Page 5 - Customer Intelligence](screenshots/page5_customer.png)

**KPIs:** Total Customers, Revenue per Customer, Repeat Rate (98.49%)

**Key Visuals:**
- Top 10 Customers by Profit — Christopher Martinez leads in 2015
- Scatter plot — Peter Fuller outlier at $9K revenue but loss-making (below break-even line)
- Segment combo — Home Office smallest segment but leads margin at 16.54%
- Sales Bucket combo — XL orders most profitable per customer

---

### Page 6 — Time & Seasonal Trends
**Business Question:** When does the business peak and is growth sustainable?

![Page 6 - Time Trends](screenshots/page6_trends.png)

**KPIs:** YoY Revenue Growth %, Best Season (dynamic), Best Month (dynamic)

**Key Visuals:**
- 4-year monthly trend — consistent growth with Nov/Dec peaks every year
- Annual YoY combo — margin improved from 10.24% (2014) to 13.41% (2016) before slight dip
- Monthly order count — November (158) and December (161) lead all months
- Season combo — Fall leads at 13.67% margin, Winter weakest at 7.19%

---

## 📐 DAX Measures — Key Patterns

### YoY Growth Pattern (used across all KPI cards)
```dax
YoY Revenue Growth % =
VAR CurrentYear = SELECTEDVALUE(superstore_clean[Order_Year])
VAR CurrentRevenue = CALCULATE([Total Revenue])
VAR PrevRevenue = CALCULATE(
    [Total Revenue],
    ALL('Date Table'),
    superstore_clean[Order_Year] = CurrentYear - 1
)
RETURN
IF(
    ISBLANK(CurrentYear) || ISBLANK(PrevRevenue),
    BLANK(),
    DIVIDE(CurrentRevenue - PrevRevenue, PrevRevenue, 0)
)
```

### Arrow Display Pattern
```dax
YoY Growth =
VAR yoy = [YoY Revenue Growth %]
RETURN
IF(
    ISBLANK(yoy), "--",
    IF(
        yoy > 0, "▲ " & FORMAT(yoy, "0.0%"),
        IF(yoy < 0, "▼ " & FORMAT(ABS(yoy), "0.0%"), "— 0.0%")
    )
)
```

### Color Coding Pattern
```dax
YoY Color =
SWITCH(
    TRUE(),
    [YoY Revenue Growth %] > 0, "#28A745",
    [YoY Revenue Growth %] < 0, "#DC3545",
    "#6C757D"
)
```

### Dynamic Best Month
```dax
Best Month =
VAR MonthTable =
    ADDCOLUMNS(
        VALUES(superstore_clean[Order_Month_Name]),
        "Revenue", CALCULATE([Total Revenue])
    )
RETURN
MAXX(
    TOPN(1, MonthTable, [Revenue], DESC),
    superstore_clean[Order_Month_Name]
)
```

---

##  Key Business Insights

### 1. Discounting is the #1 profit killer
- No-discount orders earn **29.5% margin**
- High-discount orders lose **-100% margin** — every order above 20% destroys value
- Medium discount (21-40%) produces **-19.70% margin**

### 2. Furniture is a revenue trap
- $742K revenue but only **2.48% margin** overall
- Tables lose **$17.7K** from 26% average discount
- Bookcases lose **$3.5K** from 22% average discount

### 3. Geographic performance gap
- East leads at **15.5% margin**
- South at **7.5%** — half of East's performance
- Ohio (-34.14%) and Pennsylvania (-19.02%) are biggest loss states

### 4. High revenue ≠ high profit customers
- Peter Fuller — high revenue but loss-making (below break-even line)
- Home Office segment smallest but leads margin at **16.54%**
- Consumer segment largest revenue but lowest margin at **10.68%**

### 5. Customer loyalty is exceptional
- **98.49% repeat customer rate**
- Problem is margin not churn

### 6. Seasonal concentration risk
- **Fall = 37% of annual revenue**
- November (158 orders) and December (161 orders) lead all months
- Winter margin drops to **7.19%** — lowest of all seasons
- Spring margin peaks at **18.04%**

---

##  How to Open

1. Download **Power BI Desktop** (free) from microsoft.com
2. Open `superstore_dashboard.pbix`
3. Update data source path if prompted
4. Refresh data
5. Use Year, Segment, Region slicers to explore insights

---

## 👤 Author

**Rajveer**
- [rajveerpatil759@gmail.com]
-  [LinkedIn](www.linkedin.com/in/rajveerpatil019)


---

##  License

This project is for portfolio and educational purposes.
Data source: Superstore Sales Dataset (publicly available sample retail dataset)
