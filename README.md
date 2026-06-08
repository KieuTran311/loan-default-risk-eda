# Loan Default Risk - Exploratory Data Analysis

Tools: Python, Pandas, Matplotlib, Seaborn, Jupyter Notebook

## Project Overview

This project uses Python and exploratory data analysis to examine factors associated with loan default risk.

## Business Problem

Banks cannot approve every loan application, but rejecting too many applications can also reduce business opportunities. This project explores borrower characteristics associated with higher default rates to identify patterns that could support lending decisions.

## Dataset

Credit Risk Dataset from Kaggle. Key information includes:

- Borrower demographics
- Income and employment information
- Loan amount and interest rate
- Loan purpose
- Previous default history
- Loan status (Repaid or Defaulted)

## Technical Approach

### Data Cleaning

- Filled missing values in `person_emp_length` and `loan_int_rate` using median values
- Removed duplicate records
- Removed unrealistic values:
  - Age > 100
  - Employment length > 60 years
  - Income ≤ 0

### Feature Engineering

Created Loan-to-Income Ratio (LTI):

```python
df['LTI'] = df['loan_amnt'] / df['person_income']
```

Created three LTI risk groups:

- Low Risk (< 0.2)
- Medium Risk (0.2–0.4)
- High Risk (> 0.4)

### Exploratory Analysis

- Correlation analysis using heatmaps
- Default rate analysis by loan intent
- Default rate analysis by previous default history
- Income and LTI comparison between defaulted and non-defaulted borrowers
- Grouped analysis using Pandas

## Key Findings

- Previous default history was the strongest indicator of future default.
- Borrowers with high LTI ratios showed higher default rates.
- Personal and medical loans had higher default rates than several other loan categories.
- Lower-income borrowers generally showed higher credit risk.

## Recommendations

- Apply additional review for high-LTI applicants.
- Increase scrutiny for borrowers with previous defaults.
- Monitor personal and medical loan categories more closely.
- Consider risk-based pricing using borrower risk characteristics.

## Files

```text
├── credit-risk-analysis.ipynb
```
