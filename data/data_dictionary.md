# Data Dictionary

## Dataset Overview

This document describes the variables included in the modeling dataset:

```text
merged/model data.csv
```

The dataset was derived from the **2024 Israeli Social Survey** conducted by the Israeli Central Bureau of Statistics (CBS).

| Property | Value |
|---|---:|
| Unit of analysis | Individual survey respondent |
| Number of rows | 6,907 |
| Number of columns | 39 |
| Predictor variables | 37 |
| Derived scores | 2 |
| Main outcome used for classification | `AtRisk`, created during modeling from `financial_risk_score` |

The source survey contains additional variables that were not retained in the final modeling table. The variables below were selected after data cleaning, recoding, feature engineering, and redundancy checks.

---

## General Coding Conventions

- `NaN` represents a missing, inapplicable, or structurally unavailable response.
- Original CBS non-response codes such as `888888` and `999999` were converted to `NaN` during preprocessing.
- Most binary variables were recoded as:
  - `1` = Yes
  - `0` = No
- Several categorical variables retain their original CBS category codes.
- Ordinal scales should not automatically be treated as continuous without considering their measurement meaning.
- Imputation, scaling, and one-hot encoding are performed inside the modeling pipeline after the train/test split to prevent data leakage.

---

# Variable Dictionary

## 1. Demographic Characteristics

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `YelidBrham` | Born abroad | Binary | `1` = Yes, `0` = No | 0 |
| `female` | Female respondent indicator, derived from the original gender variable | Binary | `1` = Female, `0` = Male | 0 |
| `Gil` | Age group | Ordinal categorical | `1` = 20–24; `2` = 25–29; `3` = 30–34; `4` = 35–39; `5` = 40–44; `6` = 45–49; `7` = 50–54; `8` = 55–59; `9` = 60–64; `10` = 65–74; `11` = 75+ | 0 |
| `ShnatAliya` | Period of immigration to Israel | Ordinal categorical | `0` = Not applicable / born in Israel; `1` = up to 1947; `2` = 1948–1954; `3` = 1955–1960; `4` = 1961–1971; `5` = 1972–1979; `6` = 1980–1989; `7` = 1990–1995; `8` = 1996 or later | 0 |
| `MatzavMishp` | Marital status | Nominal categorical | `1` = Married; `2` = Separated; `3` = Divorced; `4` = Widowed; `5` = Single | 0 |
| `MispYeladim` | Number of children ever born | Ordinal / count | `0`–`6`; `7` = seven or more | 14 |
| `Dat` | Religion | Nominal categorical | `1` = Jewish; `2` = Muslim; `3` = Christian; `4` = Druze; `5` = Other religion; `6` = Atheist / no religion | 12 |
| `SherutTzahal` | Served in the Israel Defense Forces | Binary | `1` = Yes, `0` = No | 1,210 |
| `SherutLeumi` | Completed national or civilian service | Binary | `1` = Yes, `0` = No | 3,493 |
| `RishyonNehiga` | Holds a driving licence | Binary | `1` = Yes, `0` = No | 3 |

---

## 2. Household Characteristics

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `SugMeshekBait_C` | Household structure, calculated by CBS | Nominal categorical | Four CBS household-structure categories, coded `1`–`4` | 0 |
| `Earners` | Number of income earners in the household | Count | `0`–`5` | 0 |

> For the exact wording of the four `SugMeshekBait_C` categories, refer to the official CBS Social Survey 2024 codebook.

---

## 3. Health and Education

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `health_problem` | Respondent reports a long-term health problem or limitation | Binary | `1` = Yes, `0` = No | 30 |
| `ShnotLimud` | Grouped years of schooling / educational duration | Ordinal categorical | Six ordered categories, coded `1`–`6`; larger values indicate more years of education | 17 |
| `hebrew_proficiency` | Mean proficiency in speaking, reading, and writing Hebrew | Continuous derived scale | `1`–`5`; higher values indicate better Hebrew proficiency | 0 |

### Hebrew proficiency construction

`hebrew_proficiency` is the row-level mean of three survey items:

- Hebrew speaking ability
- Hebrew reading ability
- Hebrew writing ability

The component items showed high internal consistency in the preprocessing analysis.

---

## 4. Employment and Work

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `MaamadAvoda_C_wp` | Employment status / position at work, including a separate code for respondents not currently employed | Nominal categorical | `1`–`4` | 15 |
| `HekefMisra_C_wp` | Employment extent | Nominal categorical | `1`–`3`; includes full-time, part-time, and not applicable / not employed | 177 |
| `MerutzeAvoda_wp` | Satisfaction with work | Ordinal categorical | `1`–`5`; higher values follow the original survey response order | 104 |
| `work_income_level` | Grouped monthly income from work | Ordinal categorical | `0` = not employed / no work income; `1`–`10` = increasing income groups | 575 |

The `_wp` suffix indicates that the variable was adapted during preprocessing so respondents outside the employment question path could be represented explicitly instead of being treated as ordinary missing observations.

---

## 5. Social Participation and Giving

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `HitnadvutShana` | Volunteered during the previous 12 months | Binary categorical | Original coding retained: `1` = Yes, `2` = No | 12 |
| `TrumaShana` | Donated money during the previous 12 months | Binary categorical | Original coding retained: `1` = Yes, `2` = No | 31 |

> These two variables retain the original `1`/`2` survey coding and were not converted to `1`/`0` in the exported modeling file.

---

## 6. Household Financial Management and Saving

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `AchrayutKesef` | Person mainly responsible for managing household finances | Nominal categorical | Three categories, coded `1`–`3` | 27 |
| `MenahelTazrim` | Actively manages or monitors household cash flow | Binary | `1` = Yes, `0` = No | 27 |
| `MishtameshAppMaakav` | Uses an application or digital tool to monitor finances | Binary | `1` = Yes, `0` = No | 30 |
| `HoraotKeva` | Uses standing orders / automatic payments | Binary | `1` = Yes, `0` = No | 54 |
| `HashkaotBank` | Holds savings or investments through a bank | Binary | `1` = Yes, `0` = No | 97 |
| `HassachMezuman` | Saves money in cash | Binary | `1` = Yes, `0` = No | 81 |
| `HassachGmach` | Saves through a mutual-aid fund or gemach | Binary | `1` = Yes, `0` = No | 70 |
| `HashkaotShukHon` | Holds capital-market investments | Binary | `1` = Yes, `0` = No | 82 |
| `HashkaotMatbeotVirt` | Holds investments in virtual currencies | Binary | `1` = Yes, `0` = No | 67 |
| `HashkaotAher` | Holds another type of investment or saving product | Binary | `1` = Yes, `0` = No | 68 |

The investment variables are separate indicators and are not mutually exclusive. A respondent may have more than one saving or investment channel.

---

## 7. Financial Knowledge, Attitudes, and Behaviour

| Variable | Description | Type | Coding / Range | Missing values |
|---|---|---|---|---:|
| `YedaHashkaa` | Self-assessed knowledge about investments | Ordinal categorical | `1`–`5` | 182 |
| `SikunHashkaot` | Attitude toward or willingness to take investment risk | Ordinal categorical | `1`–`5` | 172 |
| `LoHosech` | Frequency or degree of not saving money | Ordinal categorical | `1`–`5` | 259 |
| `HeshbonotBazman` | Pays bills on time | Ordinal categorical | `1`–`5` | 147 |
| `MatarotKaspiot` | Sets long-term financial goals | Ordinal categorical | `1`–`5` | 285 |
| `ShokelKniot` | Carefully considers whether purchases are affordable | Ordinal categorical | `1`–`5` | 147 |

These six variables retain the direction of the original CBS response categories. Their coding should be checked before combining them into a single index, because a higher code does not necessarily represent a uniformly more desirable financial behaviour for every item.

The preprocessing analysis found low internal consistency across these items, so they were retained as separate predictors rather than summed into one general financial-behaviour scale.

---

# Derived Scores

## `FinancialLiteracyScore`

| Property | Description |
|---|---|
| Type | Integer derived score |
| Range | `0`–`7` |
| Missing values | 0 |
| Meaning | Number of correct answers across seven objective financial-literacy questions |
| Interpretation | Higher scores indicate greater objective financial knowledge |

The score is based on questions covering concepts such as:

- Inflation
- Simple and compound interest
- Investment return
- Purchasing power
- Capital-market risk

Each correctly answered item contributes one point.

---

## `financial_risk_score`

| Property | Description |
|---|---|
| Type | Numeric derived score |
| Range | `0`–`8` |
| Missing values | 0 |
| Role | Primary target score |
| Interpretation | Higher scores indicate exposure to more indicators of financial hardship |

The score is the sum of eight binary financial-risk indicators:

1. Difficulty covering regular household expenses
2. Forgoing medical treatment because of financial difficulty
3. Forgoing medication because of financial difficulty
4. Forgoing dental treatment because of financial difficulty
5. Forgoing food because of financial difficulty
6. Forgoing a hot meal because of financial difficulty
7. Inability to cover an unexpected expense
8. Difficulty covering expenses during the previous year

Each risk indicator contributes:

- `1` when financial hardship is present
- `0` when it is not present

The score therefore ranges from `0` to `8`.

---

## Binary Classification Target: `AtRisk`

`AtRisk` is not stored in `model data.csv`. It is created in the modeling notebook using:

```python
AtRisk = (financial_risk_score >= 3).astype(int)
```

| Value | Meaning |
|---:|---|
| `0` | Fewer than three financial-risk indicators |
| `1` | Three or more financial-risk indicators |

`financial_risk_score` and `FinancialLiteracyScore` should not be included as predictors when the purpose is to predict `AtRisk`, unless their use is explicitly justified. In particular, using `financial_risk_score` as a predictor would create direct target leakage because `AtRisk` is calculated from it.

---

# Modeling Notes

## Feature roles

The exported file contains:

- **37 candidate predictors**
- **1 financial-literacy score**
- **1 financial-risk target score**

The classification notebook uses the 37 individual variables as predictors and derives `AtRisk` from `financial_risk_score`.

## Missing values

Missingness is not always random. Some variables are missing because the respondent was not asked the question, for example:

- Military service
- National service
- Employment-related questions
- Investment and financial-management questions

These structurally missing values should be handled within the modeling pipeline rather than automatically dropping all incomplete rows.

## Categorical encoding

Nominal variables such as religion, marital status, household type, and financial responsibility should generally be one-hot encoded.

Ordinal variables may be:

- retained as ordered numeric codes when that ordering is substantively meaningful; or
- one-hot encoded when equal spacing between categories cannot be assumed.

## Leakage prevention

The following practices are required:

1. Split the data into training and test sets before fitting preprocessing steps.
2. Fit imputers, encoders, and scalers only on the training data.
3. Apply the fitted transformations to validation and test data.
4. Exclude `financial_risk_score` from the predictor matrix when predicting `AtRisk`.

---

# Source and Reproducibility

- **Source:** Israeli Central Bureau of Statistics, Israeli Social Survey 2024.
- **Raw-data preparation:** `notebooks/01_eda.ipynb`
- **Final modeling dataset:** `merged/model data.csv`
- **Modeling and dashboard notebook:** `merged/eda_model_dashboard.ipynb`

The official CBS codebook remains the authoritative source for the precise wording of original survey questions and category labels. This document describes the variables as they appear after project-specific cleaning and feature engineering.
