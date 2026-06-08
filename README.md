# 🛒 The Look E-Commerce — Sales Performance Analysis
### Data Analytics Project with Power BI & Google BigQuery

---

## 📌 About the Project

This project involves a comprehensive analysis of **The Look**, a fictional e-commerce clothing retailer's dataset, using **Power BI** and **Google BigQuery**. The project is designed to simulate real-world data analytics scenarios, addressing critical business questions around sales performance, customer behavior, cancellation & return losses, and geographic distribution.

As a result of the analysis, **3 interactive dashboards** were created, each accompanied by actionable business recommendations.

---

## 🎯 Business Questions Analyzed

- How have sales changed over the years? Is there a growth trend?
- Which product categories and brands generate the most revenue?
- Are there seasonal sales patterns? Which months peak?
- What are the order cancellation and return rates? What is the cost to the business?
- Which categories have the highest cancellation and return rates?
- Which countries do customers shop from?
- What does customer spending segmentation look like? Is the platform premium or price-sensitive?
- Which browsers do users use to access the platform?

---

## 🗂️ Dataset

**Source:** Google BigQuery — `thelook_ecommerce` dataset  
**Project ID:** `project-f459b334-59af-4d62-803`

| Table | Description | Approx. Rows |
|---|---|---|
| `orders` | Order information | 124,000+ |
| `order_items` | Order line items | 999,000+ |
| `products` | Product details | 999,000+ |
| `users` | User information | 999,000+ |
| `inventory_items` | Inventory records | 999,000+ |
| `events` | User interaction events | 999,000+ |
| `distribution_centers` | Distribution center info | 10 |

---

## 🔧 Technologies Used

| Technology | Purpose |
|---|---|
| **Power BI Desktop** | Dashboard design and visualization |
| **Google BigQuery** | Data source and cloud storage |
| **DAX (Data Analysis Expressions)** | Custom calculations and measures |
| **Power Query** | Data cleaning and transformation |

---

## 🔗 Data Model & Relationships

After importing the data into Power BI, the following table relationships were manually established:

```
order_items (order_id)            → orders (order_id)             [Many to One]
order_items (product_id)          → products (id)                 [Many to One]
orders (user_id)                  → users (id)                    [Many to One]
inventory_items (product_id)      → products (id)                 [Many to One]
products (distribution_center_id) → distribution_centers (id)     [Many to One]
```

---

## 🧹 Data Quality Check

All tables were inspected in Power Query Editor using Column Quality and Column Distribution tools:

| Table | Status | Notes |
|---|---|---|
| users | ✅ Clean | All columns 100% Valid |
| products | ✅ Clean | All columns 100% Valid |
| orders | ✅ Clean | returned_at null — expected (non-returned orders) |
| order_items | ✅ Clean | shipped_at, delivered_at, returned_at nulls — business logic expected |
| inventory_items | ✅ Clean | sold_at 63% null — unsold items |
| distribution_centers | ✅ Clean | 10 rows, all fields complete |
| events | ⚠️ Note | user_id 100% null — anonymous visitors |

---

## 📊 DAX Measures

The following custom DAX measures were developed in this project:

```dax
-- Return Rate
Return Rate = 
DIVIDE(
    CALCULATE(COUNT(orders[order_id]), orders[status]="Returned"),
    COUNT(orders[order_id])
)

-- Cancellation Rate
Cancellation Rate = 
DIVIDE(
    CALCULATE(COUNT(orders[order_id]), orders[status]="Cancelled"),
    COUNT(orders[order_id])
)

-- Best Selling Month
Best Selling Month = 
FORMAT(
    DATEVALUE("1/" & MAXX(
        ADDCOLUMNS(VALUES(order_items[created_at].[Month]),
        "Sales", CALCULATE(SUM(order_items[sale_price]))),
        [Month]) & "/2024"),
    "MMMM"
)

-- Worst Selling Month
Worst Selling Month = 
FORMAT(
    DATEVALUE("1/" & MINX(
        ADDCOLUMNS(VALUES(order_items[created_at].[Month]),
        "Sales", CALCULATE(SUM(order_items[sale_price]))),
        [Month]) & "/2024"),
    "MMMM"
)

-- Spending Segmentation
Spending Segment = 
VAR ActualPrice = VALUE(order_items[sale_price])
RETURN SWITCH(
    TRUE(),
    ActualPrice >= 150, "High",
    ActualPrice >= 50,  "Medium",
    "Low"
)
```

---

## 📈 Dashboard 1 — General Sales Performance

![Dashboard 1](Dashboard%201.jpeg)

### Key KPIs
| Metric | Value |
|---|---|
| Total Orders | 124,000 |
| Total Revenue | $10.77 Million |
| Average Order Value | $86.52 |
| Return Rate | 10% |

### Visuals & Findings

**Monthly Total Spending (Line Chart)**
A clear peak is observed in May and June, likely driven by pre-summer clothing purchases. January and December show comparatively lower performance.

**Yearly Total Spending (Line Chart)**
Continuous growth from 2019 to 2024. 2024 is the highest revenue year. The apparent decline in 2025–2026 is due to incomplete data for those years — not a real downturn.

**Order Tracking by Year (Horizontal Bar Chart)**
All order statuses grew in volume each year. Critical finding: Cancelled orders also grew proportionally — the cancellation problem was never resolved and scaled with the business.

**Total Spending by Category (Bar Chart)**
Jeans and Outerwear & Coats are the highest revenue-generating categories. Shorts and Sleep & Lounge are the lowest performers.

**Order Status Summary (Pie Chart)**
- Complete: 30%
- Cancelled: 25%
- Shipped: 20%
- Processing: 15%
- Returned: 10%

1 in every 3 orders ends in cancellation or return.

**Spending Segmentation (Donut Chart)**
Based on a custom DAX segmentation model:
- Low Segment ($0–$50): 61.16% — The platform's primary revenue driver
- Medium Segment ($50–$150): 31.91% — Stable backbone
- High Segment ($150+): 6.93% — Niche premium audience

---

## 📉 Dashboard 2 — Cancellation & Return Loss Analysis

![Dashboard 2](Dashboard%202.jpeg)

### Key KPIs
| Metric | Value |
|---|---|
| Total Cancellation + Return Loss | $659.25 Billion |
| Cancellation Loss Only | $243.06 Billion |
| Return Loss Only | $268.60 Billion |

### Visuals & Findings

**Treemap — Cancellation & Return Volume by Category**
Sweaters, Jeans, and Outerwear & Coats have the highest cancellation/return volumes. Notably, the best-selling categories are also the most returned — suggesting a systemic issue in product-fit expectations.

**Bar Chart — Net Revenue Losses by Category**
Total spending vs. lost net profit is compared by category. Sweaters and Outerwear & Coats show the most significant losses.

### Business Recommendations
- **Outerwear & Knitwear:** High return rates are often driven by sizing mismatches in e-commerce. An AI-powered size assistant should be integrated to reduce returns.
- **Jeans:** Cancellations can be reduced by shortening shipping times and applying penalty policies to suppliers with high error rates.

---

## 🌍 Dashboard 3 — Geographic & User Analysis

![Dashboard 3](Dashboard%203.jpeg)

### Visuals & Findings

**World Map — Total Spending by Country**
China is by far the largest market ($1,248,404). USA is second ($553,894), Brazil is third ($226,765).

**Country & City Tables**
- Total geographic reach: $2,161,090
- Top city: Beijing ($4,557)
- Gender split: Male $2,938,420 — Female $2,568,111

**User Behavior Summary (Donut Chart)**
39.28% of users visit product pages. The purchase conversion rate is 23.44%.

**Browser Segmentation (Treemap)**
Chrome is the dominant browser. Firefox and Safari follow. Over 55% of users access the platform via Chrome or Safari.

### Business Recommendations
- **Emerging Markets:** Trend analysis over the last 3 years shows UK and Germany growing at +22% annually — the highest-potential markets outside China.
- **Action Plan (2027):** Marketing budgets should prioritize Chrome/Safari optimization to maximize reach and conversion.

---

## 💡 Key Insights & Strategic Recommendations

### Sales Performance
1. **Strong Growth Trend** — Continuous growth from 2019 to 2024 signals a healthy business model
2. **Seasonality Exists** — May–June peak demands early inventory and campaign preparation
3. **2024 Anomaly** — The unusual May 2024 spike should be investigated for root cause

### Cancellation & Returns
4. **Critical Issue** — 1 in 3 orders results in cancellation or return — the company's top operational priority
5. **Category-Focused Solution** — Size assistant and logistics improvements needed for Sweaters and Jeans categories

### Customer Segmentation
6. **Low Segment Drives Revenue** — 61% revenue share; cross-selling campaigns (e.g. "Buy 3 Get 2") should target this price-sensitive audience
7. **Medium-to-High Upgrade Path** — Free shipping thresholds and discount coupons should incentivize medium segment customers to move up
8. **Protect the Premium Audience** — The 6.93% high segment needs a VIP loyalty program and AI-powered personal styling assistant

### Geographic Strategy
9. **China is Dominant** — Dedicated logistics and customer service investment needed for the Chinese market
10. **UK & Germany Opportunity** — +22% annual growth potential warrants increased marketing budget allocation

---

## 📁 File Structure

```
thelook-ecommerce-powerbi/
│
├── Dashboard 1.jpeg          # General Sales Performance
├── Dashboard 2.jpeg          # Cancellation & Return Loss Analysis
├── Dashboard 3.jpeg          # Geographic & User Analysis
├── The Look E-Commerce Analizi.pbix  # Power BI project file
└── README.md                 # Project documentation
```

---



*This project is for educational purposes. The Look dataset simulates a fictional e-commerce scenario.*
