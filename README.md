# Predicting Financial Vulnerability in Israel

## Team Members
-    Or Arbeli
-    Ovad Biton
-    Benny Salirosas
-    Shai Sadeh

---

# Project Overview

Financial vulnerability is a major challenge among people in Israel, affecting their quality of life, independence, and overall well-being. Early identification of financially vulnerable individuals can support policymakers and welfare organizations in directing resources more effectively.

This project develops machine learning models that predict financial vulnerability using data from the Israeli Social Survey. The project includes the complete data science pipeline, from data cleaning and feature engineering to model evaluation and interpretation.

---

# Research Question

Can financial vulnerability be accurately predicted using demographic, socioeconomic, health, and social characteristics collected in the Israeli Social Survey?

---

# Business / Social Impact

Identifying financially vulnerable individuals before severe financial hardship develops may help:

- Target social services more efficiently.
- Improve allocation of public resources.
- Support evidence-based policymaking.
- Detect at-risk populations earlier.

---

# Dataset

Source:

- Israeli Central Bureau of Statistics (CBS)
- Israeli Social Survey

The dataset contains responses from approximately **6,900 participants**.

### Target Variable

A new binary target variable was constructed to represent financial vulnerability.

The target is based on multiple indicators of financial hardship reported by respondents. Individuals experiencing three or more financial risk indicators were classified as financially vulnerable.

---

# Features

The final dataset contains **38 predictor variables**, including:

- Demographic characteristics
- Age
- Gender
- Education
- Employment
- Household composition
- Health indicators
- Social participation
- Housing characteristics
- Income-related variables

---

# Methodology

The project follows the standard machine learning workflow:

1. Data cleaning
2. Missing value handling
3. Variable recoding
4. Target variable construction
5. Exploratory Data Analysis (EDA)
6. Feature engineering
7. Feature selection
8. Model training
9. Model comparison
10. Model interpretation

---

# Models Evaluated

Three supervised learning algorithms were compared:

- Logistic Regression
- Random Forest
- Gradient Boosting

Performance was evaluated using:

- Accuracy
- Precision
- Recall
- F1-score
- ROC-AUC

---

# Results

The models successfully identified financially vulnerable individuals.

Among the evaluated models, **Gradient Boosting achieved the best overall predictive performance**, outperforming Logistic Regression and Random Forest.

Model interpretation techniques were used to identify the most influential predictors contributing to financial vulnerability.

---

# Dashboard

The project includes an interactive dashboard for exploring:

- Financial vulnerability prevalence
- Population characteristics
- Model predictions
- Feature importance
- Variable distributions

---

# Technologies Used

- Python
- Pandas
- NumPy
- Scikit-learn
- Gradient Boosting
- Matplotlib
- Seaborn
- Plotly
- Jupyter Notebook

---
