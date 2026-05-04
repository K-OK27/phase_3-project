# phase_3-project
churn
# SyriaTel Customer Churn Prediction

*Binary classification* to predict whether a telecom customer will stop doing business with SyriaTel.  
Built with *Logistic Regression* and *Decision Tree* – optimized for interpretability and business action.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Business Problem](#business-problem)
3. [Dataset](#dataset)
4. [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
5. [Modeling Approach](#modeling-approach)
6. [Hyperparameters](#hyperparameters-explained)
7. [Results & Model Comparison](#results--model-comparison)
8. [Business Recommendations](#business-recommendations)

---

## Project Overview

- *Goal:* Predict customer churn (yes/no) using historical account and usage data.
- *Target audience:* SyriaTel retention managers who want to reduce revenue loss.
- *Models used:* Logistic Regression (production‑ready) + Decision Tree (interpretable rules).
- *Key finding:* Customer service calls, international plan, and daytime minutes are the strongest predictors of churn.

---

## Business Problem

Every customer who churns costs SyriaTel:
- Lost future revenue
- Customer acquisition cost (wasted)

By identifying *at‑risk customers early*, the company can offer targeted retention incentives (discounts, plan changes, loyalty rewards).

---

## Dataset

*Source:* SyriaTel customer data (CSV file: bigml_59c28831336c6604c800002a.csv)

*Features (after cleaning):*

| Feature | Description |
|---------|-------------|
| account_length | How many months the customer has been with SyriaTel |
| international_plan | Yes / No |
| voice_mail_plan | Yes / No |
| number_vmail_messages | Number of voicemail messages |
| total_day_minutes | Daytime minutes used |
| total_eve_minutes | Evening minutes used |
| total_night_minutes | Night minutes used |
| total_intl_minutes | International minutes used |
| customer_service_calls | Number of calls to customer service |
| state_churn_rate | Average churn rate of the customer’s state (target‑encoded) |
| churn | *Target:* 1 = churned, 0 = stayed |

---

## Exploratory Data Analysis (EDA)

Key insights from the data:

- *Overall churn rate:* ~14% (imbalanced – needs special handling).
- *Customer service calls:*  
  Customers who churn make *2.5 calls on average* vs 1.2 for those who stay.  
  If a customer makes >3 calls, churn probability doubles.
- *International plan:*  
  Churn rate = *42%* for customers with international plan vs *10%* without.
- *Voicemail plan:*  
  Customers with voicemail churn *less than half* as often.
- *Daytime minutes:*  
  Churners have a second peak above 200 minutes – possibly feeling overcharged.

<img width="425" height="165" alt="customer service calls vs churn" src="https://github.com/user-attachments/assets/06fe4532-1813-4b62-b459-37ca62c8aefd" />

<img width="422" height="260" alt="plan vs churn" src="https://github.com/user-attachments/assets/2b19ca57-c7e6-4e29-bedd-16ac9635624c" />




---

## Modeling Approach

### 1. Data Preparation
- Drop phone_number, area_code (low value), and all *_charge columns (redundant with minutes).
- Encode binary categories (international_plan, voice_mail_plan).
- Target‑encode state into state_churn_rate.
- Handle class imbalance with *SMOTE* (synthetic oversampling) + class_weight='balanced'.

### 2. Train / Test Split
- 80% train, 20% test, stratified by churn.
- Random state = 42 for reproducibility.

### 3. Models
| Model | Scaling | Hyperparameters |
|-------|---------|-----------------|
| *Logistic Regression* | Yes (StandardScaler) | class_weight='balanced', max_iter=1000 |
| *Decision Tree* | No | class_weight='balanced', max_depth=5 |

### 4. Evaluation Metrics
- Precision, Recall, F1‑score (focus on *churn class*)
- ROC‑AUC
- Confusion matrix

---

## Hyperparameters 

### Logistic Regression
- class_weight='balanced' – automatically gives more importance to the minority (churn) class.
- max_iter=1000 – ensures the optimization algorithm converges.
- random_state=42 – reproducible results.

### Decision Tree
- class_weight='balanced' – penalizes misclassifying churn more heavily.
- max_depth=5 – keeps the tree shallow → easy to extract business rules, prevents overfitting.
- random_state=42 – reproducible splits.

---

## Results & Model Comparison

<img width="429" height="160" alt="logistic regression" src="https://github.com/user-attachments/assets/7e26d41b-8bb5-4c57-9704-1104401615fc" />

<img width="410" height="168" alt="decision tree" src="https://github.com/user-attachments/assets/28423b8c-ec2e-45cb-94ae-130032d9f1e6" />

<img width="448" height="185" alt="confusion matrix" src="https://github.com/user-attachments/assets/acec8c2c-0fea-457e-919c-102fc60fc5f0" />


interpretation: Logistic Regression has lower precision for churn (0.30) but good recall (0.72), making it better for identifying most at-risk customers.

Decision Tree has better overall accuracy (88%) and higher precision for churn (0.57), but slightly lower recall (0.59).

Recommendation: Use Logistic Regression for churn probability scoring; use Decision Tree for rule-based alerts ( if service calls ≥ 4 → flag for retention).

*Verdict:* Logistic Regression is *better* for production because:
- Higher recall (catches more actual churners)
- Better ROC‑AUC (more accurate risk scores)
- Stable probabilities (well‑calibrated)

*Decision Tree’s role:* Extract human‑readable rules (e.g., “if customer_service_calls > 3 → high risk”).


---

## Business Recommendations

1. *Alert retention team* when a customer makes *>3 customer service calls* in a month.
2. *Offer loyalty discounts* to customers with:
   - International plan *AND* total daytime minutes >200 min.
3. *Promote voicemail plan* – customers without voicemail churn at twice the rate.
4. *Weekly risk scoring* – run the Logistic Regression model on all active customers; target the top 20% highest scores for immediate outreach.
5. *Experiment with thresholds* – instead of 0.5 probability, use a cutoff that maximizes cost savings (e.g., contact only those with >70% predicted churn risk).
