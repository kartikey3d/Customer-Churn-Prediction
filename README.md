# Customer Churn Prediction

An end-to-end machine learning project that predicts whether a bank customer will leave (churn). It covers exploratory data analysis, feature engineering, model comparison with cross-validation, evaluation, and a saved, ready-to-use scikit-learn pipeline.

## Results at a glance

| Item | Value |
|---|---|
| Dataset | 10,000 customers, 12 columns, no missing values |
| Target | `churn` (1 = left, 0 = stayed), about 20.4% positive |
| Best model | Gradient Boosting (200 estimators) |
| Cross-validated ROC AUC (5-fold, train set) | 0.8628 |
| Test ROC AUC | 0.8692 |
| Test accuracy | 0.868 |
| Test precision / recall / F1 (churn class) | 0.78 / 0.49 / 0.60 |

Cross-validated ROC AUC for all candidate models:

| Model | Mean AUC | Std |
|---|---|---|
| Gradient Boosting | 0.8628 | 0.0097 |
| Random Forest | 0.8486 | 0.0130 |
| AdaBoost | 0.8462 | 0.0133 |
| SVC | 0.8351 | 0.0104 |
| Logistic Regression | 0.7877 | 0.0244 |

The most influential features are `age` (~0.33), `products_number` (~0.27), `balance_per_product`, `balance`, `active_member`, and `country_Germany`.

## Project files

| File | Description |
|---|---|
| `Analysis.ipynb` | Full workflow notebook: EDA, feature engineering, modelling, evaluation, saving, example prediction |
| `customer_data.csv` | Raw dataset (10,000 rows) |
| `best_churn_pipeline.pkl` | Trained pipeline (preprocessing + Gradient Boosting), saved with `joblib` |

## Dataset

| Column | Type | Description |
|---|---|---|
| `customer_id` | int | Unique customer ID (dropped before modelling) |
| `credit_score` | int | Credit score |
| `country` | categorical | France, Germany or Spain |
| `gender` | categorical | Male / Female |
| `age` | int | Customer age |
| `tenure` | int | Years as a customer |
| `balance` | float | Account balance |
| `products_number` | int | Number of bank products held |
| `credit_card` | 0/1 | Has a credit card |
| `active_member` | 0/1 | Is an active member |
| `estimated_salary` | float | Estimated salary |
| `churn` | 0/1 | Target: customer left the bank |

## Workflow (as in the notebook)

1. **Imports**: pandas, numpy, matplotlib, seaborn, scikit-learn, joblib.
2. **Load and inspect**: shape, dtypes, summary statistics, missing values, class balance.
3. **EDA**: KDE plots by churn, pairplot, violin plot (age), tenure and categorical count plots, gender donut charts, correlation heatmap, balance vs. products scatter, churn rate by number of products.
4. **Feature engineering**
   - `balance_per_product`: balance / products_number
   - `salary_balance_ratio`: salary / balance (inf/NaN filled with the median)
   - `age_group`: `<25`, `25-34`, `35-44`, `45-54`, `55-64`, `65+`
   - `tenure_bucket`: `0`, `1-2`, `3-5`, `6-10`, `10+`
   - `high_balance`: 1 if balance is above the 75th percentile
5. **Preprocessing** (`ColumnTransformer`)
   - Numeric: median imputation, then `StandardScaler`
   - Categorical: most-frequent imputation, then `OneHotEncoder(handle_unknown='ignore')`
6. **Train/test split**: 80/20, stratified, `random_state=42` (8,000 train / 2,000 test).
7. **Model comparison**: Logistic Regression, Random Forest, Gradient Boosting, AdaBoost and SVC, each in a pipeline with the preprocessor, scored with 5-fold stratified CV on ROC AUC.
8. **Final evaluation**: the best model by mean CV AUC is fit on the training set and evaluated on the held-out test set (metrics, classification report, confusion matrix).
9. **Feature importance**: top 20 features from the Gradient Boosting model.
10. **Save pipeline**: `joblib.dump(best_pipeline, 'best_churn_pipeline.pkl')`.
11. **Example prediction** for a new customer.

## Getting started

### Requirements

- Python 3.9+
- pandas, numpy, matplotlib, seaborn, scikit-learn, joblib, jupyter

```bash
pip install pandas numpy matplotlib seaborn scikit-learn joblib jupyter
```

> The pickle must be loaded with a scikit-learn version compatible with the one used to train it. If loading fails or warns, install the matching version, or re-run the notebook to regenerate the file.

### Run the notebook

Place `customer_data.csv` next to the notebook, then:

```bash
jupyter notebook Analysis.ipynb
```

## Using the saved pipeline

The pipeline expects the **engineered** columns, so apply the same feature engineering as in training before calling `predict`. Do not include `customer_id`.

```python
import numpy as np
import pandas as pd
import joblib

pipeline = joblib.load("best_churn_pipeline.pkl")

customer = pd.DataFrame([{
    "credit_score": 650, "country": "France", "gender": "Male", "age": 40,
    "tenure": 3, "balance": 50000.0, "products_number": 2,
    "credit_card": 1, "active_member": 1, "estimated_salary": 60000.0,
}])

# Feature engineering (must match training)
customer["balance_per_product"] = customer["balance"] / customer["products_number"].replace(0, np.nan)
customer["balance_per_product"] = customer["balance_per_product"].fillna(0)

ratio = customer["estimated_salary"] / customer["balance"].replace(0, np.nan)
customer["salary_balance_ratio"] = ratio.replace([np.inf, -np.inf], np.nan).fillna(0.8393)  # training median

customer["age_group"] = pd.cut(customer["age"], [0, 25, 35, 45, 55, 65, 100],
                               labels=["<25", "25-34", "35-44", "45-54", "55-64", "65+"])
customer["tenure_bucket"] = pd.cut(customer["tenure"], [-1, 0, 2, 5, 10, 100],
                                   labels=["0", "1-2", "3-5", "6-10", "10+"])
customer["high_balance"] = (customer["balance"] > 127_644.24).astype(int)  # training 75th percentile

for col in ["country", "gender", "credit_card", "active_member",
            "age_group", "tenure_bucket", "high_balance"]:
    customer[col] = customer[col].astype("object")

print("Churn:", pipeline.predict(customer)[0])
print("Churn probability:", round(pipeline.predict_proba(customer)[0, 1], 3))
```

The two constants above (`0.8393`, the salary/balance median, and `127_644.24`, the balance 75th percentile) were computed on the full dataset. Recompute them from `customer_data.csv` (`df["balance"].quantile(0.75)` and the median of the salary/balance ratio) to match training exactly.

## Limitations and next steps

- **Low recall on churners (0.49)**: the model misses about half of the customers who leave. Consider lowering the decision threshold, using `class_weight` or resampling (e.g. SMOTE), and tuning for recall or F1 depending on the business cost of a missed churner.
- **No hyperparameter tuning**: `GridSearchCV` is imported but models use near-default settings. Tuning Gradient Boosting (learning rate, depth, subsampling) would likely help.
- **Feature engineering leakage risk**: the `high_balance` quantile and the salary/balance median are computed on the full dataset before the train/test split. For production, compute them on the training set only and store them with the pipeline.
- **Single dataset, single split**: results come from one dataset and one holdout; validate on fresh data before deployment.
- Possible additions: SHAP explanations, XGBoost/LightGBM, probability calibration, and a small API or Streamlit app for serving predictions.

## License

Add a license of your choice (e.g. MIT) before publishing.
