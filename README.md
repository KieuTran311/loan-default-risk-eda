# Loan Default Risk — Exploratory Data Analysis
Tools: Python, Pandas, Seaborn, Matplotlib, Jupyter Notebook

## The Business Problem

Lending is core to how banks make money but it only works if borrowers pay back. When they don't, the bank absorbs the loss directly: bad debt hits the P&L, provisioning costs go up. If it gets bad enough, it squeezes everything downstream including bonuses and headcount.

The challenge is that banks can't just reject every risky application. That kills revenue too. The real goal is to lend smarter, approve the right borrowers, price risk appropriately, and catch warning signs early enough to act.

This project looks at a loan dataset to answer a straightforward question: _"What borrower characteristics actually predict default and what should a credit team do differently based on that?"_

This is an exploratory analysis, the goal is to surface patterns in the data that can inform credit decisions, not to build a prediction model.

## Dataset

Credit Risk Dataset from Kaggle. Each row represents one loan application and includes:

- Borrower info: age, annual income, employment length, home ownership status
- Loan details: loan amount, interest rate, loan intent (education, medical, personal, etc.), loan grade
- Risk indicators: prior default on file
- Target variable: `loan_status` — 0 = repaid, 1 = defaulted

## Data Cleaning

Before any analysis, cleaned up several issues in the raw data:

- Missing values: `person_emp_length` and `loan_int_rate` both had nulls, filled with median rather than dropping rows.
- Duplicates: removed
- Outliers: filtered out age > 100, employment length > 60 years, income ≤ 0. These are clearly data entry errors that would distort any pattern-finding.

## Feature Engineering

Created one new feature: _Loan-to-Income (LTI) ratio_

```python
df['LTI'] = df['loan_amnt'] / df['person_income']
```

LTI measures how heavy the debt burden is relative to income. A person earning $30,000 taking out a $20,000 loan is in a very different position from someone earning $100,000 taking out the same amount. LTI captures that difference in a single number.

Grouped borrowers into three risk tiers:
- Low Risk: LTI < 0.2
- Medium Risk: LTI 0.2-0.4
- High Risk: LTI > 0.4

## Exploratory Analysis

Used a mix of visualizations to understand how each variable relates to default:

- Heatmap: Correlation between numeric features to identify which variables move together with loan_status
- Count plots: Default distribution by loan intent and prior default history
- Boxplots: Income and LTI distributions for defaulted vs. non-defaulted borrowers
- Histograms with KDE: age distribution by loan status

## Key Findings

_**Prior default history is the strongest signal:**_

Borrowers with `cb_person_default_on_file = Y` show substantially higher default rates. This makes intuitive sense, past behavior is the most reliable predictor of future behavior. Someone who has defaulted before has already demonstrated they will do it again under financial pressure.

_**High LTI correlates with higher default**_

Borrowers in the High Risk tier (LTI > 0.4) consistently show higher default rates in the boxplot analysis. They're carrying more debt than their income can comfortably support and when something goes wrong (job loss), they have no buffer.

**Loan intent matters**

Personal loans and medical loans have noticeably higher default rates than education or home improvement loans. This likely reflects the borrower's situation at the time of application — someone taking out a medical loan is often already under financial stress, not planning ahead.

_**Lower income = higher risk**_

Across most loan intent categories, lower-income borrowers default more frequently. Combined with LTI, this suggests the issue isn't just how much someone borrows. It's how much they borrow relative to what they can realistically repay.

## What a Credit Team Should Do Differently

Banks can't reject every high-risk application, that kills revenue. The goal is to lend smarter: approve the right borrowers, manage exposure on borderline cases and price risk appropriately.

Based on the patterns found in this analysis:

_**1. Tighten approval criteria for high-LTI applicants (LTI > 0.4)**_

These borrowers show elevated default rates consistently. Options for this group:
- Approve but reduce the loan amount to bring LTI below the threshold.
- Require additional collateral to offset the higher risk.
- Reject if neither adjustment is feasible.

The key point is that a blanket rejection isn't necessary, but lending the full requested amount at standard terms to a high-LTI borrower is leaving risk unmanaged.

_**2. Apply extra scrutiny to applicants with prior default on file**_

This is the single strongest predictor found in the data. For these applicants, a tiered review process makes sense:
- Request additional documentation (income verification, current debt obligations).
- Lower the approved loan limit.
- Apply risk-based interest rate pricing to compensate for higher expected loss.

_**3. Monitor personal and medical loan categories more closely**_

These intents carry higher default rates, likely because borrowers are already under financial stress when they apply. Early warning systems like flagging missed payments sooner, proactive outreach could reduce loss given default even if the loan is ultimately approved.

_**4. Move toward risk-based interest pricing**_

A flat interest rate across all borrowers doesn't reflect actual risk. Pricing loans based on a combination of LTI, prior default history, and loan intent would allow the bank to maintain revenue on higher-risk loans while the higher rate compensates for expected losses rather than simply losing money when those loans go bad.


## Files
```
├── credit-risk-analysis.ipynb
```
