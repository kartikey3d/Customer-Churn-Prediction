# Customer Churn Prediction

A machine learning pipeline for predicting customer churn using demographic and account data. The notebook walks through the full ML workflow — from data exploration to model deployment — using a dataset of 10,000 bank customers.

---

## Overview

This project builds and evaluates multiple classifiers to predict whether a bank customer will churn (leave). The best-performing model is saved as a reusable pipeline that can score new customers in real time.

**Best model:** Gradient Boosting Classifier  
**Test ROC AUC:** 0.8692 | **Test Accuracy:** 86.8%

---

## Dataset

**File:** `customer_data.csv`  
**Shape:** 10,000 rows × 12 columns  
**Target:** `churn` (1 = churned, 0 = retained) — ~20% positive rate

| Feature | Description |
|---|---|
| `customer_id` | Unique customer identifier (dropped before training) |
| `credit_score` | Customer credit score |
| `country` | Country of residence (France, Spain, Germany) |
| `gender` | Male / Female |
| `age` | Customer age |
| `tenure` | Years as a customer |
| `balance` | Account balance |
| `products_number` | Number of bank products held |
| `credit_card` | Has credit card (1/0) |
| `active_member` | Active member flag (1/0) |
| `estimated_salary` | Estimated annual salary |
| `churn` | **Target** — churned (1) or not (0) |

---

## Project Structure

```
Analysis.ipynb          # Main notebook
customer_data.csv       # Input dataset
best_churn_pipeline.pkl # Saved model pipeline (output)
README.md
```

---

## Notebook Walkthrough

| Step | Description |
|---|---|
| 1 | Imports and display settings |
| 2 | Load dataset, inspect shape |
| 3 | Initial inspection — dtypes, missing values, class balance |
| 4 | Exploratory Data Analysis — distributions, correlations, categorical breakdowns |
| 5 | Feature Engineering — balance per product, salary-to-balance ratio |
| 6 | Preprocessing pipeline — scaling numerics, one-hot encoding categoricals |
| 7 | Train/test split (80/20, stratified) |
| 8 | Train & compare 5 models via cross-validated ROC AUC |
| 9 | Evaluate best model on held-out test set |
| 10 | Feature importance plot |
| 11 | Save pipeline with `joblib` |
| 12 | Predict churn for a new customer sample |

---

## Models Compared

| Model | Mean CV AUC |
|---|---|
| Logistic Regression | 0.7877 |
| Random Forest | 0.8486 |
| **Gradient Boosting** | **0.8628** ✅ |
| AdaBoost | 0.8462 |
| SVC | 0.8351 |

---

## Feature Engineering

Two features are derived before training:

- **`balance_per_product`** — account balance divided by number of products (0 when products = 0)
- **`salary_balance_ratio`** — estimated salary divided by account balance

---

## Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn joblib
```

---

## Usage

### Run the notebook
```bash
jupyter notebook Analysis.ipynb
```

### Load the saved pipeline and predict
```python
import joblib
import pandas as pd

pipeline = joblib.load("best_churn_pipeline.pkl")

new_customer = pd.DataFrame([{
    "credit_score": 650,
    "country": "France",
    "gender": "Male",
    "age": 40,
    "tenure": 3,
    "balance": 50000.0,
    "products_number": 2,
    "credit_card": 1,
    "active_member": 1,
    "estimated_salary": 60000
}])

# Note: feature engineering must be applied before prediction
pred = pipeline.predict(new_customer)
prob = pipeline.predict_proba(new_customer)[:, 1]
print(f"Predicted churn: {pred[0]}, Probability: {prob[0]:.3f}")
```

---

## Results

| Metric | Value |
|---|---|
| Accuracy | 0.868 |
| Precision | 0.780 |
| Recall | 0.489 |
| F1-Score | 0.601 |
| ROC AUC | 0.869 |

> The model performs well at identifying non-churners but has moderate recall for the positive (churn) class, which is expected given the ~20% class imbalance. Consider threshold tuning or resampling techniques if recall is a priority.
