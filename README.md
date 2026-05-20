# 📦 Olist Sales Forecasting — End-to-End Data Science Project

A complete end-to-end sales forecasting pipeline using **XGBoost + PostgreSQL** on real Brazilian e-commerce data from Olist. Predicts daily revenue using lag features, rolling statistics, and calendar features.

---

## 📌 Project Overview

**Business Problem:** E-commerce businesses need to forecast daily revenue to plan inventory, marketing spend, and operations effectively.

**Solution:** Build a machine learning model that predicts next day's revenue using historical sales patterns.

**Model Performance: R² = 0.56 | RMSE = 11,420 BRL**

---

## 📂 Project Structure

```
sales-forecasting-ecommerce/
├── data/
│   ├── raw_data/          ← original Olist CSV files (not tracked)
│   └── clean_data/        ← cleaned + merged datasets
├── notebooks/
│   └── sales_forecasting.ipynb
├── sql/
│   └── 01_sales_query.sql
├── visuals/
│   └── *.png
└── README.md
```

---

## 📊 Dataset

- **Source:** Kaggle — Brazilian E-Commerce Public Dataset by Olist
- **Tables Used:** 5 (orders, order items, payments, products, category translation)
- **Raw Rows:** 400,000+ across all tables
- **Final Dataset:** 581 daily revenue rows after cleaning and feature engineering
- **Date Range:** October 2016 — August 2018

---

## 🗄️ PostgreSQL Pipeline

All 5 cleaned tables loaded into PostgreSQL `olist_ecommerce` database.

**SQL JOIN query combined:**
```sql
orders → order_items (via order_id)
order_items → products (via product_id)
products → translation (via product_category_name)
orders → payments (via order_id)
```

Result: 113,368 rows → aggregated to 611 daily revenue records.

---

## 🧹 Data Cleaning

- Filtered only **delivered** orders (96,478) — removed cancelled, processing, shipped
- Dropped delivery date columns — not needed for forecasting
- Removed 610 products with missing category names (< 2% of data)
- Removed 9 zero payment value rows — invalid transactions
- Kept 383 zero freight values — free shipping is valid business practice

**Started with 400k+ raw rows → 113,368 after joining → 581 daily rows**

---

## 📈 Exploratory Data Analysis

### Monthly Revenue Over Time

![Monthly Revenue](visuals/Montly%20Revenue%20Over%20Time.png)

> Clear upward growth trend from 2016 to mid 2018. November 2017 spike = Black Friday effect. Last 2 months show declining trend → possibly incomplete data.

---

### Top 10 Categories by Revenue

![Top Categories](visuals/Top%2010%20Categories%20by%20Revenue.png)

> bed_bath_table and health_beauty dominate revenue. Home goods and personal care are strongest performing categories.

---

### Monthly Revenue Trend of Top 3 Categories

![Category Trends](visuals/Monthly%20Revenue%20Trend%20of%20Top%203%20Categories.png)

> health_beauty shows most consistent growth. computers_accessories shows extreme volatility. bed_bath_table shows seasonal spikes.

---

### Actual vs Predicted Revenue

![Actual vs Predicted](visuals/Actual%20vs%20Predicted%20Revenue.png)

> Model successfully tracks general trend and weekly patterns. Struggles with sudden revenue spikes caused by promotions/events.

---

## ⚙️ Feature Engineering

| Feature | Description |
|---|---|
| `lag_1` | Yesterday's revenue |
| `lag_7` | Same day last week |
| `lag_30` | Same day last month |
| `rolling_mean_7` | 7-day average revenue |
| `rolling_std_7` | 7-day revenue volatility |
| `day_of_week` | Day number (0-6) |
| `is_weekend` | 1 if Saturday/Sunday |
| `month` | Month number (1-12) |

> Adding day_of_week and is_weekend improved R² from **0.28 → 0.56**

**Used chronological train/test split (80/20) — no random shuffling for time series**

---

## 🤖 Model Building

- **Algorithm:** XGBoost Regressor
- **Train/Test Split:** 80% / 20% (chronological)
- **Training rows:** 465 | **Test rows:** 116
- **Features:** 8 (lag + rolling + calendar)
- **Target:** Daily total revenue (BRL)

---

## 📉 Model Evaluation

| Metric | Value |
|---|---|
| R² Score | 0.56 |
| RMSE | 11,420 BRL |
| Mean Daily Revenue | 33,352 BRL |
| Average Error | ~34% |

- Model captures **general trend and weekly patterns** ✅
- Struggles with **sudden revenue spikes** → promotions/events ⚠️

---

## 🔑 Key Findings

- Revenue grew consistently from October 2016 to mid 2018
- November 2017 Black Friday = highest revenue spike
- health_beauty is most consistent and predictable category
- Day of week is strongest predictor of daily revenue
- Weekends vs weekdays show significantly different sales patterns
- Sudden spikes are caused by promotions — not predictable from history alone

---

## 🚀 Future Improvements

- Add **promotional calendar features** (Black Friday, holidays, events)
- Collect more years of data (currently only 2 years)
- Add **category-level features** to daily model
- Try **LSTM** (deep learning) for better sequence modeling
- Deploy as real-time **Power BI dashboard**
- Add **hyperparameter tuning** with GridSearchCV

---

## 🛠️ Tech Stack

- Python (pandas, numpy, matplotlib, seaborn)
- XGBoost
- PostgreSQL + SQLAlchemy
- Scikit-learn
- Jupyter Notebook

---

## 👤 Author

**Shaheer Khan**
Aspiring Data Scientist | Pakistan
GitHub: [@Shaheer3701](https://github.com/Shaheer3701)
