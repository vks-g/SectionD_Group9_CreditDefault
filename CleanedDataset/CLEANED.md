# Data Cleaning Log — Credit Default Dataset
**Project:** Credit Default Risk & Portfolio Analysis
**Section D | Group 9**
**Sheet Tab:** CreditDefault_cleaned

---

## Summary of Changes

| # | Action | Column(s) Affected | Method | Done By |
|---|--------|--------------------|--------|---------|
| 1 | Split age range into two separate numeric columns | Age → Min Age, Max Age | Manual split | Nachiket |
| 2 | Renamed all header columns to descriptive labels and froze header row | All columns | Manual rename | Gokul |
| 3 | Removed duplicate records | All columns | Google Sheets Remove Duplicates | Gokul |
| 4 | Standardized Region column values (fixed casing inconsistencies) | Region | =PROPER() via temporary helper column | Gokul |
| 5 | Filled missing values in Rate of Interest using column median | Rate of Interest | =ARRAYFORMULA(IF(L2:L="", MEDIAN(L2:L), L2:L)) | Isha |
| 6 | Filled missing values using column median | Property Value, Debt-to-Income Ratio, Income, Interest Rate Spread | Median imputation | Isha |
| 7 | Filled missing values in Upfront Charges with zero | Upfront Charges | Zero-fill | Isha |
| 8 | Recalculated Loan-to-Value column from scratch | Loan to Value | =(Loan Amount / Property Value) × 100 | Isha |
| 9 | Replaced "Sex Not Available" with "Unknown" | Gender | Find & Replace | Isha |
| 10 | Filled missing values using column mode | Loan Limit | Mode fill | Isha |
| 11 | Filled missing values using column mode | Approved in Advance, Loan Purpose, Submission of Application, Negative Amortization | Mode fill | Isha |
| 12 | Formatted currency columns | Loan Amount, Upfront Charges, Property Value | Currency formatting (USD) | Nachiket |
| 13 | Converted all ambiguous coded terms to Yes/No | Negative Amortization, Interest Only, Lump Sum Payment, Open Credit, Business or Commercial | Binary standardization | Nachiket |
| 14 | Expanded coded column values to full meaningful text | Loan Type, Loan Purpose, Approved in Advance, Submission of Application, etc. | Manual text expansion | Saksham |
| 15 | Applied conditional formatting based on Default Status | Default Status | Conditional Formatting rule | Gokul |
| 16 | Handled missing values in Min Age and Max Age post-split | Min Age, Max Age | Mode/median fill | Gokul |
| 17 | Created LTV Risk Bucket helper table and applied to cleaned sheet | LTV Risk Bucket (new column) | VLOOKUP from helper table | Gokul |
| 18 | Created Credit Score Band helper table and applied to cleaned sheet | Credit Score Bucket (new column) | VLOOKUP from helper table | Gokul |

---

## Detailed Notes

### 1. Age Column Split
- Original column contained age ranges as a single string (e.g., "35-44", "65-74", ">74").
- Split into two numeric columns: **Min Age** and **Max Age** to enable quantitative analysis.
- Edge case ">74" was handled as Min Age = 74, Max Age = 74.

### 2. Header Renaming & Freeze
- Original headers used abbreviated or coded names (e.g., `dtir1`, `approv_in_adv`, `Neg_ammortization`).
- All headers renamed to descriptive, reader-friendly labels.
- Row 1 frozen to keep headers visible during scrolling.

### 3. Duplicate Removal
- Used Google Sheets built-in: Data → Data Cleanup → Remove Duplicates.
- Checked across all columns.

### 4. Region Standardization
- Raw data contained inconsistent casing: "north", "south", "NORTH", etc.
- Applied `=PROPER()` in a temporary helper column, then paste-as-values back into Region column.

### 5–6. Median Imputation
- Columns with missing numeric values were filled using `MEDIAN()` of the non-null values in that column.
- This approach preserves the central tendency of the distribution without introducing bias from mean (which is sensitive to outliers in loan data).
- Columns treated: Rate of Interest, Property Value, Debt-to-Income Ratio, Income, Interest Rate Spread.

### 7. Upfront Charges — Zero Fill
- Missing upfront charges are logically interpreted as "no fee charged at origination" rather than missing data.
- Filled with 0 rather than median to preserve the correct business meaning.

### 8. LTV Recalculation
- The original LTV column contained calculation errors in some rows.
- Recalculated entirely as: `= (Loan Amount / Property Value) × 100`
- Some records show LTV > 100% — these are valid edge cases (loans issued above property value) and were retained.

### 9. Gender Standardization
- "Sex Not Available" replaced with "Unknown" for clarity and consistency.

### 10–11. Mode Fill for Categorical Columns
- Categorical columns with missing values were filled using the column mode (most frequent value).
- This is appropriate for categorical data where a "most common" value is a reasonable proxy.

### 12. Currency Formatting
- Loan Amount, Upfront Charges, and Property Value formatted as USD currency.
- Purely cosmetic — does not alter underlying numeric values.

### 13. Binary Standardization
- Columns originally coded with abbreviations (e.g., `neg_amm` / `not_neg`, `int_only` / `not_int`, `lpsm` / `not_lpsm`, `nopc` / `opc`, `b/c` / `nob/c`) were converted to clear **Yes / No** values.

### 14. Coded Value Expansion
- Loan Type: `type1` → Conventional, `type2` → Federal Housing Administration, `type3` → Veterans Affairs
- Loan Purpose: `p1` → Home Purchase, `p2` → Home Improvement, `p3` → Refinancing, `p4` → Cash-out Refinancing
- Approved in Advance: `pre` → Pre-Approved, `nopre` → Not Pre-Approved
- Submission of Application: `to_inst` → Yes, `not_inst` → No
- Occupancy Type: `pr` → Principal Residence, `sr` → Secondary Residence, `ir` → Investment Property
- Construction Type: `sb` → Site-Built

### 17. LTV Risk Bucket (Engineered Feature)
- A helper lookup table was created mapping LTV ranges to risk labels:
  - LTV < 60% → Low
  - LTV 60–80% → Moderate
  - LTV 80–100% → High
  - LTV > 100% → Very High
- Applied to cleaned sheet via VLOOKUP from the helper table.

### 18. Credit Score Bucket (Engineered Feature)
- A helper lookup table was created mapping credit scores to standard bands:
  - < 580 → Poor
  - 580–669 → Fair
  - 670–739 → Good
  - 740–799 → Very Good
  - 800+ → Excellent
- Applied to cleaned sheet via VLOOKUP from the helper table.

---

## Assumptions
- Median imputation assumes missing values follow a similar distribution to observed values.
- Zero-fill for Upfront Charges assumes no fee was charged, not that data is unknown.
- LTV values > 100% are valid distressed-lending scenarios, not data errors.
- Mode fill for categorical columns assumes the most frequent category is the most reasonable default.
- All records represent completed, valid loan originations.

---

## Before vs After — Key Statistics

| Metric | Raw Dataset | Cleaned Dataset |
|--------|-------------|-----------------|
| Total Columns | 34 | 36 (+ LTV Risk Bucket, Credit Score Bucket) |
| Missing: Rate of Interest | Present | Filled (median) |
| Missing: Property Value | Present | Filled (median) |
| Missing: Income | Present | Filled (median) |
| Missing: Upfront Charges | Present | Filled (0) |
| Coded column values (e.g., type1, p3) | Present | Expanded to full text |
| Age column format | Single range string | Two numeric columns (Min Age, Max Age) |
| LTV | Original (with errors) | Recalculated from source |
| Region casing | Inconsistent | Standardized (Title Case) |
| Duplicates | Present | Removed |
