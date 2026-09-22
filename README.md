# Loan Default Risk - Exploratory Data Analysis

Tools: Python, Pandas, Matplotlib, Seaborn

## What this is

I picked the Credit Risk Dataset from Kaggle to practice EDA and get more comfortable with Pandas. The idea I had in mind: banks want to lend money (that's how they make money), but they also don't want to lend to people who won't pay it back. So I wanted to see what patterns in the data actually line up with default. This is EDA only, not a prediction model. I'm not trying to build something that decides who gets a loan, I'm just looking for patterns worth investigating further.

## About the data

- 32,581 rows, 12 columns
- Each row is a loan, with borrower info (age, income, employment length...) and
  loan info (amount, interest rate, purpose...)
- Target column is `loan_status`: 0 = did not default, 1 = defaulted

## What I did to the data first

- Checked for duplicates and dropped them (there were 165)
- Filled missing values in `person_emp_length` and `loan_int_rate` with the median. I did duplicates first, then filled missing values, so I wouldn't accidentally turn two different rows into duplicates just because they both got the same filled-in number.
- Checked for weird values, found a handful of rows with age over 100 (one is 144, which is obviously not real) and a couple with 123 years of employment length. I didn't just delete these, I only checked how many rows would be affected (7 rows out of 32k) and it barely moves the overall default rate either way, so I left them in and just noted it.

## Overall default rate

**21.87%** of loans in this dataset defaulted.

## What I looked at

### Default rate by loan purpose (loan_intent)

| Loan Intent | Default Rate |
|---|---:|
| DEBTCONSOLIDATION | 28.68% |
| MEDICAL | 26.76% |
| HOMEIMPROVEMENT | 26.15% |
| PERSONAL | 19.90% |
| EDUCATION | 17.25% |
| VENTURE | 14.85% |

Debt consolidation loans have the highest default rate in this dataset, while venture loans have the lowest. I got these numbers by grouping the loans by `loan_intent` and calculating the default rate for each group. It shows a difference between loan purposes but it does not mean that the loan purpose itself causes a higher or lower default rate.

### Previous default history

Borrowers with a previous default on file (`Y`) had a default rate of **37.87%**, vs **18.43%** for those without (`N`). That's a pretty big gap but again this is just an association in the data, not proof that past default causes future default, there could be other things going on with that group of borrowers.

### Loan-to-Income ratio (LTI)

I made a new column: `LTI = loan_amnt / person_income`. Basically it tells you how big the loan is compared to how much the person earns.

I also checked this and LTI turns out to be almost perfectly correlated with an existing column, `loan_percent_income` (corr ≈ 0.999). Makes sense since they're measuring almost the same thing.

I split LTI into buckets to make it easier to compare:

| LTI Group | Default Rate |
|---|---:|
| < 10% | 11.55% |
| 10-20% | 14.60% |
| 20-30% | 21.42% |
| 30-40% | 64.17% |
| 40%+ | 74.52% |

This one surprised me the most out of everything I looked at. The jump from 20-30% to 30-40% is huge with the default rate almost tripling. I don't know why there's such a sharp jump instead of a smoother increase but it's the most noticeable change in the LTI groups.

### Loan Intent × LTI Group together

I also crossed loan_intent with LTI_Group to see if the LTI pattern holds up across every loan purpose or if it's mostly driven by one or two categories. Highest combination: MEDICAL loans in the 40%+ LTI group, at 84.6% default rate. Lowest: VENTURE loans under 10% LTI, at 5.1%. Default rates generally increase as LTI increases across different loan intents.

## Things I'd want to be careful about saying

- Everything above is association, not causation. I'm not running a model that controls for other variables, so I can't say "X causes default."
- The LTI groups I made up are just buckets for comparing, they're not an official risk scale a bank would actually use.
- I didn't remove the handful of unrealistic age/employment values, just flagged
  them, so there's a small amount of noise in the data.

## What I'd do next

- Use LTI as a feature in a prediction model, starting with something simple like logistic regression instead of only comparing default rates between LTI groups.

- Look more closely at previous default history to understand why the default rate is so much higher for borrowers with a previous default. I would also check whether this information gives us anything new or is something a bank would already have.

- Check the jump in default rate around 30% LTI with a larger dataset to see whether the same pattern appears or if it is specific to this dataset.

## Files

```text
├── credit-risk-analysis.ipynb
```
