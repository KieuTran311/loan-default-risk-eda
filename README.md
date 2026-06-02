# Loan Default Risk — Exploratory Data Analysis
Tools: Python, Pandas, Seaborn, Matplotlib, Jupyter Notebook

## What's this about

When a bank approves a loan and the borrower defaults, the bank loses money. The question is: are there patterns in borrower data that could have flagged the risk earlier?

This project explores a loan dataset to understand which borrower characteristics tend to appear before a default. The goal isn't to build a prediction model — it's to get familiar with the data, understand the relationships between variables, and surface patterns that could inform smarter lending decisions.

## Dataset

Credit Risk Dataset from Kaggle. Each row represents one loan application and includes:

- Borrower info: age, annual income, employment length, home ownership status
- Loan details: loan amount, interest rate, loan intent (education, medical, personal, etc.), loan grade
- Risk indicators: loan-to-income ratio, prior default on file
- Target variable: `loan_status` - 0 = repaid, 1 = defaulted

## Data Cleaning

Before doing any analysis, I cleaned up a few issues in the raw data:

- Missing values `person_emp_length` and `loan_int_rate` both had nulls. I filled them with the median value for each column rather than dropping the rows.
- Duplicates: Removed duplicate rows.
- Outliers: Filtered out rows where age > 100 (clearly data entry errors), employment length > 60 years, and income ≤ 0. These values would distort any analysis if left in.

## Feature Engineering

I created one new feature: Loan-to-Income (LTI) ratio.

```python
df['LTI'] = df['loan_amnt'] / df['person_income']
```

This measures how heavy the loan burden is relative to the borrower's income. A person earning $30,000 taking out a $20,000 loan is in a very different situation from someone earning $100,000 taking out the same loan, LTI captures that difference.

I then grouped borrowers into three risk tiers based on LTI:
- Low Risk: LTI < 0.2
- Medium Risk: LTI 0.2–0.4
- High Risk: LTI > 0.4

## Exploratory Analysis

I used a mix of visualizations to understand how each variable relates to loan_status:

- Heatmap: Correlation between all numeric features. Helped identify which variables are most associated with default.
- Count plots: Distribution of defaults by loan intent and by prior default history.
- Boxplots: Comparing income and LTI distributions between defaulted and non-defaulted borrowers.
- Histograms with KDE: Age distribution by loan status.

## Key Findings

Prior default history is the strongest signal: Borrowers with `cb_person_default_on_file = Y` (meaning they've defaulted before) show substantially higher default rates in the current dataset. Past behavior is the best predictor of future behavior.

High LTI correlates with higher default: Borrowers in the High Risk tier (LTI > 0.4) consistently show higher default rates in the boxplot analysis. They're taking on more debt than their income can comfortably support.

Loan intent matters: Personal loans and medical loans have noticeably higher default rates compared to education or home improvement loans. This could reflect the borrower's financial situation at the time, medical emergencies and personal cash needs often come from a place of financial stress.

Lower income = higher risk: Across most loan intent categories, lower-income borrowers default more frequently. This is expected, but the magnitude of the difference reinforces the importance of income verification in the approval process.

## Recommendations

Based on the patterns found in the data, here are four things a lending team could consider:

1. Stricter approval criteria for high-LTI applicants: Borrowers with LTI > 0.4 show elevated risk. Adding a hard cap or requiring additional collateral for these cases could reduce default exposure.

2. Extra scrutiny for applicants with prior default on file: This is the single strongest indicator found in the data. A tiered review process for these applicants (additional documentation, lower loan limits) seems warranted.

3. Closer monitoring of personal and medical loan categories: These have higher default rates and may benefit from more frequent check-ins or earlier intervention when payments are missed.

4. Risk-based interest pricing: Rather than a flat rate, pricing loans based on a risk score (incorporating LTI, prior default, loan intent) would better reflect the actual risk the bank is taking on for each borrower.


## Files
```
├── credit-risk-analysis.ipynb
```
