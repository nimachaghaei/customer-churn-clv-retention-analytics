# E-Commerce Customer Churn & CLV Analytics

A customer analytics project that combines **churn prediction, customer lifetime value (CLV), and model explainability** to understand who is at risk, how valuable they are, and where customer value is most exposed to churn.

## What is the problem?

In e-commerce, customers don't usually tell you when they're leaving. They simply stop buying.

Looking only at churn risk isn't enough. A high-risk customer worth $50 is very different from a high-risk customer worth $2,000.

This project brings together three perspectives:

* **Churn risk:** How likely is a customer to churn?
* **Customer value:** How much value is the customer expected to generate?
* **Risk drivers:** What customer behavior is pushing the churn prediction?

The goal is not to automatically decide what action marketing should take. Instead, the analysis provides a better picture of **customer risk and value** that can support retention planning.

---

## Project Workflow

The project is divided into three notebooks:

### 01 — Customer Churn Prediction

Uses **XGBoost** to predict customer churn from historical purchasing behavior.

Features include:

* Recency
* Frequency
* Monetary value
* Tenure
* Average Order Value
* Purchase Interval Standard Deviation
* Recent Activity Ratio

I compared several classification models before selecting XGBoost based on its overall performance.

The final model achieved a **ROC-AUC of approximately 0.72**. I also lowered the classification threshold to **0.35** to identify more potential churners.

**SHAP** is then used to understand which factors are contributing most to each customer's prediction.

### 02 — Customer Lifetime Value

This notebook estimates future customer purchasing behavior using probabilistic models:

* **BG/NBD** → predicts future purchase behavior
* **Gamma-Gamma** → estimates expected monetary value
* **3-month CLV** → combines both to estimate future customer value

BG/NBD was evaluated against simple baselines on a 90-day holdout period:

| Model         |        MAE |       RMSE |
| ------------- | ---------: | ---------: |
| Zero baseline |     1.3220 |     2.5982 |
| Mean baseline |     1.1683 |     2.2612 |
| **BG/NBD**    | **0.9597** | **1.5573** |

Customers with repeat-purchase history were then grouped into four CLV segments:

| Segment         | Mean CLV | Total CLV |
| --------------- | -------: | --------: |
| A - VIP / Whale |  ~$2,049 |   ~$1.03M |
| B - High Value  |    ~$489 |    ~$247K |
| C - Medium      |    ~$268 |    ~$135K |
| D - Low Value   |    ~$115 |     ~$58K |

### 03 — Risk × Value Analysis

The final notebook brings the churn and CLV outputs together.

This makes it possible to look at both **risk and value at the same time**, instead of treating every customer equally.

The analysis also calculates:

**CLV at Risk = CLV × Churn Probability**

This is used as a **prioritization metric** to estimate how much projected customer value is currently exposed to churn risk. It should not be interpreted as guaranteed lost revenue.

---

## Key Findings

The analysis found a clear difference between customer segments.

Average churn probability increases as customer value decreases:

| Segment         | Avg. Churn | Avg. CLV |
| --------------- | ---------: | -------: |
| A - VIP / Whale |      23.6% |   $2,049 |
| B - High Value  |      28.8% |     $489 |
| C - Medium      |      35.5% |     $268 |
| D - Low Value   |      45.2% |     $115 |

At the same time, the relationship between churn probability and CLV is only moderately weak, with a correlation of **-0.21**. This suggests that customer value and churn risk are related, but they are not telling us the same thing.

The most interesting difference appears when looking at **CLV at risk**:

| Segment         | CLV at Risk |
| --------------- | ----------: |
| A - VIP / Whale |    ~$232.6K |
| B - High Value  |     ~$70.3K |
| C - Medium      |     ~$47.6K |
| D - Low Value   |     ~$25.1K |

The VIP segment contains relatively few high-risk customers, but those customers account for the largest amount of CLV at risk.

This is why looking at **risk and value together** is more useful than looking at churn probability alone.

---

## SHAP: Understanding the Risk Drivers

SHAP is used to move beyond simply predicting churn and look at **what is driving those predictions**.

The main risk drivers also vary between customer groups.

For VIP customers, **Tenure and Recency** frequently appear among the strongest risk drivers.

For Low Value customers, **Recency, Monetary value, and Purchase Interval variability** are more common top drivers.

These results show which customer characteristics are most associated with the model's predictions, rather than suggesting direct causal relationships.

---

## Dataset

The project uses the **Online Retail II** dataset from the UCI Machine Learning Repository.

The data contains transactional records from a UK-based online retailer covering **December 2009 to December 2010**.

The raw dataset is not included in this repository.

---

## Repository Structure

```text
customer-churn-clv-retention-analytics/
│
├── data/
│   └── README.md
│
├── notebooks/
│   ├── 01_Customer_Churn_Prediction_XGBoost.ipynb
│   ├── 02_Customer_CLV_Modeling.ipynb
│   └── 03_Customer_Risk_Value_Analysis.ipynb
│
├── output/
│   ├── xgb_churn_predictions_v3.csv
│   └── clv_predictions.csv
│
├── images/
│  
│   
├── requirements.txt
└── README.md
```

---

## Tech Stack

**Python**
`pandas` · `numpy` · `scikit-learn` · `xgboost` · `shap` · `lifetimes` · `matplotlib` · `seaborn` · `plotly` · `pyarrow`

---

## A Note on Scope

This project focuses on **identifying customer risk and value**, not on automatically selecting retention actions.

The results could support different retention strategies depending on the business, campaign economics, customer preferences, and available incentives. Deciding which action to take is intentionally outside the scope of this analysis.
