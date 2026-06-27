# 🛍️ Customer Behavior Analysis — End-to-End Data Analytics Project

A complete, production-style data analytics pipeline built on a retail customer shopping dataset — covering data cleaning, SQL analysis, BI dashboarding, and business reporting.

> **Stack:** Python · Pandas · PostgreSQL · Power BI  
> **Dataset:** 3,900 rows · 18 columns · Retail transactional + demographic data

---

## 📁 Repository Structure

```
├── Untitled.ipynb                              # Phase 1 — Data cleaning & PostgreSQL loading
├── customer_behavior.csv                       # Raw dataset
├── customer_behavior.sql                       # Phase 2 — 8 business SQL queries
├── customer_behavior.pbix                      # Phase 3 — Power BI dashboard file
├── Customer_Behavior_Analysis_Report.docx      # Final analytical report
├── Customer_Behavior_Analysis_Presentation.pptx  # Final presentation deck
└── README.md
```

---

## 🔄 Project Pipeline

```
Raw CSV  →  Python Cleaning  →  PostgreSQL  →  SQL Analysis  →  Power BI Dashboard  →  Report & PPT
```

This mirrors a real-world analytics workflow — the same pipeline used inside data teams at product and e-commerce companies.

---

## Phase 1 — Data Cleaning (Python / Pandas)

**File:** `Untitled.ipynb`

### Dataset
| Property | Value |
|----------|-------|
| Rows | 3,900 |
| Original Columns | 18 |
| Final Columns | 19 (after feature engineering) |

### Issues Found & Fixed

| Issue | Action Taken |
|-------|-------------|
| `review_rating` had 37 missing values | Filled using **median rating per product category** to preserve category-specific distribution |
| Column names had spaces & special characters | Standardized to `lowercase_snake_case` for PostgreSQL compatibility |
| `discount_applied` and `promo_code_used` were 100% identical across all 3,900 rows | Dropped `promo_code_used` as redundant |

### Feature Engineering

| New Column | Logic |
|------------|-------|
| `age_group` | Binned `age` into demographic segments (Young Adult, Adult, Middle Age, Senior) |
| `purchase_frequency_days` | Numeric mapping of `frequency_of_purchases` (e.g. Weekly → 7, Fortnightly → 14) |

### PostgreSQL Load
```python
from sqlalchemy import create_engine

engine = create_engine("postgresql+psycopg2://user:password@localhost:5432/Customer_behaviour")
df.to_sql("customer", engine, if_exists="replace", index=False)
```
> ⚠️ **Note:** Never hardcode credentials in production. Use environment variables or a `.env` file.

---

## Phase 2 — SQL Analysis (PostgreSQL)

**File:** `customer_behavior.sql`

8 business questions answered using SQL on the `customer` table in pgAdmin.

| # | Business Question | Techniques Used |
|---|-------------------|-----------------|
| Q1 | Total revenue by gender | `GROUP BY`, `SUM` |
| Q2 | Discount users spending above average | Subquery, `WHERE` filter |
| Q3 | Top 5 products by average review rating | `ROUND`, `CAST`, `ORDER BY`, `LIMIT` |
| Q4 | Avg. purchase — Standard vs Express shipping | `WHERE IN`, `GROUP BY` |
| Q5 | Subscriber vs non-subscriber spend comparison | `COUNT`, `SUM`, `AVG`, `GROUP BY` |
| Q6 | Top 5 products with highest discount usage | `WHERE`, `GROUP BY`, `LIMIT` |
| Q7 | Do repeat buyers (>5 purchases) subscribe? | `WHERE`, `GROUP BY` |
| Q8 | Revenue contribution by age group | `GROUP BY`, `ORDER BY DESC` |

### Key SQL Finding
> Subscribers and non-subscribers spend almost identically per order (~$59 avg). The real value of subscription is **volume**, not order size — an insight invisible without querying the data.

---

## Phase 3 — Power BI Dashboard

**File:** `customer_behavior.pbix`

### Connection
- Connected live to PostgreSQL via **Get Data → Database → PostgreSQL**
- Host: `localhost:5432` | DB: `Customer_behaviour` | Table: `customer`
- All 3,900 rows, 20 columns loaded

### DAX Measures Built
```
Total Revenue         = SUM(customer[purchase_amount])
Avg Purchase Amount   = AVERAGE(customer[purchase_amount])
Total Customers       = DISTINCTCOUNT(customer[customer_id])
Subscriber Revenue    = CALCULATE([Total Revenue], customer[subscription_status] = "Yes")
Repeat Buyer Count    = CALCULATE(DISTINCTCOUNT(customer[customer_id]), customer[previous_purchases] > 5)
```

### Dashboard Visuals
- KPI Cards — Total Revenue, Total Customers, Avg Purchase, Subscriber Count
- Bar Chart — Revenue by Gender
- Donut Chart — Subscriber vs Non-Subscriber split
- Stacked Bar — Repeat Buyers by Subscription Status
- Column Chart — Revenue by Age Group
- Slicers — Season, Category, Gender, Subscription Status (synchronized across all visuals)

---

## 📊 Key Business Insights

| # | Finding |
|---|---------|
| 1 | **Male customers** generated a substantially higher share of total revenue |
| 2 | A notable segment uses discounts but **still spends above average** — high-value, loyalty-ready customers |
| 3 | Top-rated products consistently score **above 3.7 / 5.0** — strong quality perception in specific lines |
| 4 | Express-shipping users spend **marginally more per transaction** — speed-prioritizers are less price-sensitive |
| 5 | Subscribers and non-subscribers spend **~$59 per order on average** — subscription value lies in volume |
| 6 | Many repeat buyers (>5 purchases) **have not converted to subscribers** — an untapped conversion pipeline |
| 7 | **Senior & Middle Age** segments lead revenue; Young Adults are a growth segment for targeted offers |

---

## 📄 Deliverables

| File | Description |
|------|-------------|
| `Customer_Behavior_Analysis_Report.docx` | Full written report covering all three phases, findings, and recommendations |
| `Customer_Behavior_Analysis_Presentation.pptx` | 18-slide deck — structured for business stakeholder presentation |

---

## 🚀 How to Run This Project

### Prerequisites
```
Python 3.x
PostgreSQL (pgAdmin)
Power BI Desktop
```

### Step 1 — Install Python dependencies
```bash
pip install pandas sqlalchemy psycopg2-binary
```

### Step 2 — Run the Jupyter notebook
Open `Untitled.ipynb` and run all cells. Update the PostgreSQL connection string with your own credentials before running the load step.

### Step 3 — Run SQL queries
Open `customer_behavior.sql` in pgAdmin (connected to your local PostgreSQL instance) and execute queries individually or all at once.

### Step 4 — Open Power BI dashboard
Open `customer_behavior.pbix` in Power BI Desktop. Update the data source connection to point to your local PostgreSQL instance if prompted.

---

## 🔮 Future Scope

- Add time-series transaction dates for seasonal trend forecasting
- Apply K-Means clustering for data-driven customer segmentation
- Build a predictive churn model targeting at-risk non-subscribers
- Add geographic mapping visuals using the `location` column
- Run A/B test simulations to model targeted discount-reduction impact

---

## 👤 Author

**Piyush Yadav**  
B.Sc. (Hons) Computer Science — Aryabhatta College, University of Delhi  
[GitHub](https://github.com/piyushyadav0021) · [Portfolio](https://piyushcv.vercel.app)

---

> *This project was built as a portfolio demonstration of a complete, real-world data analytics pipeline — from raw data to business insight.*
