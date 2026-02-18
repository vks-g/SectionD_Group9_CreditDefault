# 📊 Credit Default Risk & Portfolio Analysis
**Section D | Group 9 | Newton School of Technology, Rishihood University**
**Course:** Data Visualization & Analytics (CSA-224)
**Faculty:** Satyaki Das, Archit Raj
**Team:** Gokul VKS · Nachiket Amlekar · Isha Tomar · Saksham Sontakke · Abhiney Siraparapu · Adarsh Vashistha

---

## 📁 Repository Structure
```
SectionD_Group9_CreditDefault/
├── RawDataset/          # Original Kaggle dataset (10,000 records)
├── CleanedDataset/      # Cleaned & transformed dataset with engineered features
├── Calculations/        # KPI calculations and pivot analysis (Google Sheets export)
├── Dashboard/           # Dashboard screenshots and exported visuals
├── Documentation/       # Full project report (PDF)
└── Presentation/        # Final presentation deck
```

---

## 🧭 Project Overview

This project analyzes a **10,000-record home loan dataset** from 2019 (sourced from [Kaggle](https://www.kaggle.com/)) to identify credit default risk patterns across a $3.3 billion mortgage portfolio.

The goal was to build a **KPI-driven analytical framework** that surfaces which borrower profiles, loan structures, and geographic markets carry disproportionate default risk — enabling data-backed underwriting and pricing decisions.

**Key outputs:**
- 9 portfolio-level risk KPIs
- Multi-dimensional EDA across region, LTV, credit score, loan type, and demographics
- 11-visualization interactive dashboard built in Google Sheets
- 5 actionable business recommendations with quantified impact

---

## 📋 Data Dictionary

| Column | Type | Description |
|---|---|---|
| ID | Numeric | Unique loan record identifier |
| Year | Numeric | Loan origination year (2019) |
| Loan Limit | Categorical | Conforming / Non-Conforming |
| Gender | Categorical | Male / Female / Joint / Unknown |
| Approved in Advance | Categorical | Pre-approved (Yes/No) |
| Loan Type | Categorical | Conventional / FHA / VA |
| Loan Purpose | Categorical | Home Purchase / Refinancing / Cash-out Refinancing / Home Improvement |
| Credit Worthiness | Categorical | Standard / Non-Standard |
| Loan Amount | Currency (USD) | Sanctioned loan amount |
| Rate of Interest | Percentage | Annual interest rate |
| Interest Rate Spread | Numeric | Spread over benchmark rate |
| Upfront Charges | Currency (USD) | One-time origination fees |
| Term (months) | Numeric | Repayment term |
| Property Value | Currency (USD) | Assessed collateral value |
| Occupancy Type | Categorical | Principal Residence / Secondary / Investment Property |
| Income | Currency (USD) | Borrower monthly income |
| Credit Score | Numeric | Borrower FICO/credit score |
| LTV | Percentage | Loan Amount / Property Value × 100 |
| Region | Categorical | North / South / Central / North-East |
| Default Status | Categorical | **Target variable** — Yes / No |
| Debt-to-Income Ratio | Numeric | Monthly debt as % of gross income |
| LTV Risk Bucket *(engineered)* | Categorical | Low / Moderate / High / Very High |
| Credit Score Bucket *(engineered)* | Categorical | Poor / Fair / Good / Very Good / Excellent |

> **Total columns:** 36 (34 original + 2 engineered) · **Total records:** 10,000 · **Portfolio value:** $3.30B

---

## 🧹 Data Cleaning Notes

All cleaning was performed in **Google Sheets** (Tab: `CreditDefault_cleaned`). A full log of every step and the responsible team member is maintained in the **Logs tab**.

### Missing Value Handling
| Column | Method |
|---|---|
| Rate of Interest | Median imputation via `ARRAYFORMULA(IF(..., MEDIAN(...), ...))` |
| Property Value, DTI, Income, Interest Rate Spread | Column median imputation |
| Upfront Charges | Zero-filled (absence of charges is a valid scenario) |
| Loan Limit, Approved in Advance, Loan Purpose, Submission of Application, Negative Amortization | Column mode fill |

### Outlier Treatment
- LTV was **recalculated from raw data** as `(Loan Amount / Property Value) × 100` to correct pre-existing errors.
- LTV values > 100% were **retained** as valid edge cases (distressed lending) and classified as *Very High* risk — not removed.

### Transformations Applied
- `Age` column split into `Min Age` and `Max Age` (two numeric columns)
- `Region` standardized using `=PROPER()` to fix case inconsistencies (e.g., `south` → `South`)
- Ambiguous codes expanded to full text: `type1/type2/type3` → `Conventional/FHA/VA`, `p1/p2/p3/p4` → loan purposes, etc.
- `Sex Not Available` renamed to `Unknown` in Gender column
- Monetary columns formatted as USD currency
- Duplicate records removed

### Feature Engineering
| Feature | Logic |
|---|---|
| **LTV Risk Bucket** | Low (<60%), Moderate (60–80%), High (80–100%), Very High (>100%) — via `XLOOKUP` helper table |
| **Credit Score Bucket** | Poor (<580), Fair (580–669), Good (670–739), Very Good (740–799), Excellent (800+) — via `XLOOKUP` helper table |

---

## 📐 KPI Framework

| KPI | Formula | Result |
|---|---|---|
| Portfolio Default Rate | `COUNTIF(Default,"Yes") / COUNTA(ID) × 100` | **23.67%** |
| Exposure at Default (EAD) | `SUMIF(Default,"Yes", Loan_Amount)` | **$749.2M** |
| Loss Contribution % | `EAD / Total Portfolio × 100` | **22.7%** |
| Avg Exposure per Defaulter | `EAD / COUNT(Defaults)` | **$316,525** |
| High Risk Borrower % | `COUNTIF(LTV_Bucket,"Very High") / Total × 100` | 4.04% |
| Income Stress Ratio | `AVG DTI (Defaulters) / AVG DTI (All) × 100` | 39.66 vs 37.96 |
| Prime Borrower Ratio | `COUNTIF(Credit_Score_Bucket,"Excellent") / Total × 100` | — |
| Approval Effectiveness Ratio | `Non-defaults among approved / Total approved × 100` | **0.18** |
| Risk-Based Pricing Gap | `Avg Rate (Defaulters) − Avg Rate (Non-Defaulters)` | — |

---

## 🔍 Key Insights

### Portfolio Overview
- **23.67% overall default rate** — nearly 1 in 4 loans defaults
- **$749.2M Exposure at Default** against a $3.3B total portfolio
- Average loan amount: **$330,042** · Average interest rate: **4.02%**

### Regional Default Rates
| Region | Loans | Defaults | Default Rate |
|---|---|---|---|
| North-East | 73 | 26 | **35.62%** ⚠️ |
| Central | 579 | 155 | 26.77% |
| South | 4,279 | 1,084 | 25.33% |
| North | 5,069 | 1,102 | 21.74% |

### LTV Risk — Strongest Predictor
| LTV Bucket | Default Rate |
|---|---|
| Very High (>100%) | **88.12%** 🚨 |
| Low (<60%) | 26.63% |
| High (80–100%) | 21.75% |
| Moderate (60–80%) | **16.02%** ✅ |

### Loan Type
| Type | Default Rate |
|---|---|
| FHA | **34.01%** |
| VA | 25.41% |
| Conventional | 21.56% |

### Credit Score — Weak Predictor
All five credit score bands (Poor → Excellent) default within a **narrow 22–24% range**, challenging the conventional reliance on credit scores in underwriting. LTV and DTI are significantly more predictive.

### Demographics
- **Joint applicants**: 19.65% default rate — lowest of all groups
- **Investment properties**: 28.24% vs 23.43% for principal residences
- **DTI of defaulters** (39.66) is modestly higher than non-defaulters (37.44)
- **Average credit score**: defaulters (697.9) vs non-defaulters (698.8) — nearly identical

---

## 📊 Dashboard Summary

**Title:** Strategic Credit Risk & Portfolio Insights
**Built in:** Google Sheets (Tab: `Dashboard`) using PivotTables, `COUNTIFS`, `SUMIFS`

| # | Visualization | Type | Business Question |
|---|---|---|---|
| 1 | Total Capital Exposure by Region | Bar | Where is the largest lending concentration? |
| 2 | Geographical Default Concentration | Bar | Which regions have the highest default rates? |
| 3 | Loan Default Distribution by Purpose | Bar | Does loan purpose influence default behavior? |
| 4 | Portfolio Vulnerability by LTV Buckets | Stacked Bar | How does collateral coverage relate to default risk? |
| 5 | Credit Score Segment Analysis | Segmented Bar | Do credit scores predict default in this portfolio? |
| 6 | Exposure by LTV Risk Bucket | Bar | How much capital is tied to each LTV tier? |
| 7 | Valuation Dispersion by Occupancy & Structure | Stacked Bar | How does property value vary by occupancy type? |
| 8 | Interest Yield & Spread Analysis by Loan Type | Dual-Axis | Are riskier loan types priced appropriately? |
| 9 | Multivariate Demographic Risk Matrix | Radar | Which age + income + gender combos carry most risk? |
| 10 | Average Credit Score by Gender | Bar | Are there credit quality differences by demographics? |
| 11 | Collateral Risk by Occupancy Status | Clustered Bar | Do investment properties carry higher collateral risk? |

**Filters:** Region · Loan Type · LTV Risk Bucket · Credit Score Band

---

## 💡 Recommendations

1. **Enforce a Hard LTV Cap at 100%** — Very High LTV loans default at 88.12%. Declining these 404 loans would avoid ~356 defaults and reduce EAD by ~$125–150M, improving portfolio default rate from 23.67% → ~20.93%.

2. **Apply Regional Risk Premiums** — Add 25–50 bps for North-East and Central borrowers, or require lower maximum LTV in these markets.

3. **Tighten FHA Loan Standards** — Require minimum credit score of 620+, lower max LTV, and mandatory income verification. A 5% improvement in FHA default rate preserves ~$25M.

4. **Incentivize Joint Applications** — Offer 10–15 bps rate discount for joint applicants. Shifting 10% of individual applications to joint could improve portfolio default rate by ~0.5%.

5. **Replace Credit Score Screening with a Dual-Trigger Model** — Require both LTV ≤ 80% AND DTI ≤ 36% for standard approval. A 2% portfolio-wide improvement from this model represents ~$66M in reduced EAD.

---

## ⚠️ Limitations

- **Single-year dataset (2019)** — no multi-year trend analysis possible
- **No macroeconomic variables** — unemployment, GDP, housing market conditions not included
- **Median imputation** — assumes missing values follow the same distribution as observed
- **No default timeline** — only final default outcome recorded; early vs. late defaults not distinguished
- **No recovery data** — Loss Given Default (LGD) cannot be computed

---

## 🔭 Future Scope

- Incorporate 2017–2022 data for cyclical default pattern analysis
- Build a logistic regression or decision tree model for borrower-level default probability scoring
- Add property auction recovery data to enable full Expected Loss calculation (EL = PD × LGD × EAD)
- Overlay macroeconomic variables (Fed Funds Rate, unemployment) at origination
- Migrate dashboard to **Looker Studio** for enhanced interactivity and auto-refresh

---

## 📄 License

This project was developed as an academic capstone for CSA-224 at Newton School of Technology, Rishihood University. Dataset sourced from [Kaggle — Loan Default Dataset](https://www.kaggle.com/).
