# 🛍️ Customer Shopping Behavior Analysis

A full-stack data analytics project analyzing customer shopping behavior across 3,900 transactions — uncovering patterns in spending, product preferences, customer segments, and subscription behavior to drive strategic business decisions.

---

## 📌 Project Overview

A retail company wanted to better understand its customers to improve sales, satisfaction, and long-term loyalty. This project tackles the core business question:

> *"How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?"*

---

## 📸 Dashboard Preview

![Customer Behavior Dashboard](<img width="1432" height="828" alt="Screenshot (310)" src="https://github.com/user-attachments/assets/924c3d7a-a5e8-41a4-9ee0-810109a0a156" />
)

---

## 📁 Repository Structure

```
customer-shopping-behavior-analysis/
│
├── images/
│   └── dashboard.png                        # Power BI dashboard screenshot
│
├── Business Problem Document.pdf            # Project brief
├── Customer Shopping Behavior Analysis.pdf  # Full project report
├── Customer-Shopping-Behavior-Analysis.pptx # Stakeholder presentation
├── Customer_Shopping_Behavior_Analysis.ipynb # Data cleaning & EDA notebook
├── customer_behavior_dashboard.pbix         # Power BI interactive dashboard
├── customer_behavior_sql_queries.sql        # PostgreSQL business queries
├── customer_shopping_behavior.csv           # Raw dataset (3,900 rows, 18 columns)
└── README.md
```

---

## 📊 Dataset Summary

| Property | Details |
|---|---|
| **Rows** | 3,900 |
| **Columns** | 18 |
| **Missing Data** | 37 values in `Review Rating` column |

**Key Features:**
- **Demographics:** Age, Gender, Location, Subscription Status
- **Purchase Details:** Item Purchased, Category, Purchase Amount (USD), Season, Size, Color
- **Shopping Behavior:** Discount Applied, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type, Payment Method

---

## 🐍 Python – Data Preparation & EDA

Performed in **Jupyter Notebook** using `pandas`:

- **Data Loading & Exploration:** `df.info()`, `.describe()` for structure and statistics
- **Missing Data Handling:** Imputed missing `Review Rating` values using the median per product category
- **Column Standardization:** Renamed all columns to snake_case
- **Feature Engineering:**
  - `age_group` — binned customer ages into demographic groups
  - `purchase_frequency_days` — derived from purchase frequency data
- **Data Consistency Check:** Confirmed `discount_applied` and `promo_code_used` were redundant; dropped `promo_code_used`
- **Database Integration:** Loaded cleaned DataFrame into PostgreSQL for SQL analysis

---

## 🗄️ SQL – Business Analysis (PostgreSQL)

10 analytical queries addressing key business questions:

| # | Question | Insight |
|---|---|---|
| 1 | Revenue by Gender | Males generated $157,890 vs. Females $75,191 |
| 2 | High-Spending Discount Users | 839 customers used discounts yet spent above average |
| 3 | Top 5 Products by Rating | Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Skirt (3.78) |
| 4 | Shipping Type Comparison | Express avg. $60.48 vs. Standard $58.46 |
| 5 | Subscribers vs. Non-Subscribers | Similar avg. spend (~$59); non-subscribers dominate volume |
| 6 | Discount-Dependent Products | Hat (50%), Sneakers (49.66%), Coat (49.07%) |
| 7 | Customer Segmentation | Loyal: 3,116 · Returning: 701 · New: 83 |
| 8 | Top 3 Products per Category | Jewelry, Blouse, Sandals, Jacket lead their categories |
| 9 | Repeat Buyers & Subscriptions | 2,518 repeat buyers are non-subscribers vs. 958 subscribers |
| 10 | Revenue by Age Group | Young Adults lead at $62,143 |

---

## 📈 Power BI Dashboard

An interactive dashboard highlighting:

- 📦 **3.9K** total customers
- 💵 **$59.76** average purchase amount
- ⭐ **3.75** average review rating
- Subscription status breakdown (Yes 27% / No 73%)
- Revenue & sales by category and age group

**Filters available:** Subscription Status, Gender, Category, Shipping Type

---

## 💡 Business Recommendations

1. **Boost Subscriptions** — Promote exclusive perks; only 27% of customers are currently subscribed
2. **Customer Loyalty Programs** — Incentivize the 701 returning customers to become loyal buyers
3. **Review Discount Policy** — Nearly 50% of purchases for some products use discounts; ensure margin sustainability
4. **Product Positioning** — Feature top-rated products (Gloves, Sandals, Boots) in marketing campaigns
5. **Targeted Marketing** — Prioritize Young Adults and Middle-aged segments, as they drive the highest revenue
6. **Express Shipping Upsell** — Express shipping correlates with slightly higher spend; promote as premium option

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Python** (pandas) | Data cleaning, EDA, feature engineering |
| **PostgreSQL** | Structured query analysis |
| **Power BI** | Interactive visualization & dashboard |
| **Jupyter Notebook** | Development environment |

---

## 🚀 Getting Started

### Prerequisites
- Python 3.8+
- PostgreSQL
- Power BI Desktop
- Jupyter Notebook

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/kartikey3d/customer-trends-data-analysis.git
   cd customer-trends-data-analysis
   ```

2. **Install Python dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2 jupyter
   ```

3. **Run the notebook**
   ```bash
   jupyter notebook Customer_Shopping_Behavior_Analysis.ipynb
   ```

4. **Load data into PostgreSQL**
   - Follow the database integration steps at the end of the notebook

5. **Run SQL queries**
   - Open `customer_behavior_sql_queries.sql` in pgAdmin or any PostgreSQL client

6. **View the dashboard**
   - Open `customer_behavior_dashboard.pbix` in Power BI Desktop

---

## 📄 License

This project is for educational and portfolio purposes.
